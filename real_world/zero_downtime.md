# Zero Downtime

- As a No-SQL database, Elasticsearch does not have the ACID guarantees that one might get with a Postgres migration.  As a result, we need to be more creative when upgrading indices to maintain zero downtime.  The makers of elasticsearch created a [blog post](https://www.elastic.co/blog/changing-mapping-with-zero-downtime) on this very topic, which we're going to do here.  The steps are as follows:

  - [Create an alias that points to the old index](#create-an-alias-that-points-to-the-old-index)
  - [Update the code in Rails to point to the alias](#update-the-code-in-rails-to-point-to-the-alias)
  - [Create a new index with the new analyzer for the fields __first_name__ and __last_name__](#create-a-new-index-with-the-new-analyzer-for-the-fields-first_name-and-last_name).
  - [Copy all the data from the old index into the new one](#copy-all-the-data-from-the-old-index-into-the-new-one)
  - Update the alias to point to the new index
  - Delete the old index

### Create an alias that points to the old index
  - Enter the following command in kibana:

    ```
    POST _aliases
    {
      "actions": [
        {
          "add": {
            "alias": "patients_alias",
            "index": "patients_development"
          }
        }
      ]
    }
    ```

### Update the code in Rails to point to the alias

  - Open the code on your development machine and apply this change:

    ```diff
    diff --git a/app/models/es/patient.rb b/app/models/es/patient.rb
    index 1790765491..e29193ed85 100644
    --- a/app/models/es/patient.rb
    +++ b/app/models/es/patient.rb
    @@ -38,6 +38,10 @@ module Es
           "patients_#{Rails.env}"
         end

    +    def self.aliased_name
    +      "patients_alias"
    +    end
    +
         def self.document_type
           'patient'
         end
    ```

  - Or you can run this command in the `~/src/icisstaff` directory.

    ```sh
    git apply <<-DIFF
    --- a/app/models/es/patient.rb
    +++ b/app/models/es/patient.rb
    @@ -38,6 +38,10 @@ module Es
           "patients_#{Rails.env}"
         end

    +    def self.aliased_name
    +      "patients_alias"
    +    end
    +
         def self.document_type
           'patient'
         end
    DIFF
    ```

  - Now open up Chirp's development environment in your browser.  Check that the [patient search](http://icisstaff.localhost/chirp/patients?searchTerm=O%27Brien) still works as expected.  You should see exactly one search result, "Conan O'Brien".
  
### Create a new index with the new analyzer for the fields first_name and last_name.
  - Here's the list of kibana commands:

    ```
    PUT patients_improved
    {
      "mappings": {
        "patient": {
          "properties": {
            "first_name": {
              "type": "text",
              "analyzer": "alphanumeric_only", 
              "fields": {
                "keyword": {
                  "type": "keyword",
                  "ignore_above": 256
                }
              }
            },
            "last_name": {
              "type": "text",
              "analyzer": "alphanumeric_only",
              "fields": {
                "keyword": {
                  "type": "keyword",
                  "ignore_above": 256
                }
              }
            }
          }
        }
      },
      "settings": {
        "analysis": {
          "analyzer": {
            "alphanumeric_only": {
              "filter": [
                "lowercase"
              ],
              "char_filter": [
                "remove_special_chars"
              ],
              "type": "custom",
              "tokenizer": "standard"
            }
          },
          "char_filter": {
            "remove_special_chars": {
              "type": "pattern_replace",
              "pattern": "[^a-zA-Z \t]",
              "replacement": ""
            }
          }
        }
      }
    }
    ```

### Copy all the data from the old index into the new one

  - Let's go back to Kibana:

    ```
    POST _reindex
    {
      "source": {
        "index": "patients_development"
      },
      "dest": {
        "index": "patients_improved"
      }
    }
    ```

    The `_reindex` method allows us to copy data from the one index to another.  This does not change the mapping of the destination index.  All the first and last names will now be analyzed with our special `alphanumeric_only` analyzer.  Unfortunately this new analyzer doesn't affect the `query_string` parameter.  Let's try searching for *OBrien* without an apostrophe.

    ```
    GET patients_improved/_search
    {
      "query": {
        "query_string": {
          "query": "OBrien"
        }
      }
    }
    ```
    
    <details>
      <summary>If you see the results (below), nothing matches</summary>
      <p>
      
      ```json
      {
        "took": 1,
        "timed_out": false,
        "_shards": {
          "total": 5,
          "successful": 5,
          "failed": 0
        },
        "hits": {
          "total": 0,
          "max_score": null,
          "hits": []
        }
      }
      ```
      </p>
    </details>
    
    The `query_string` method uses a computed field called [\_all](https://www.elastic.co/guide/en/elasticsearch/reference/5.1/mapping-all-field.html).  If there's a way to use a custom analyser on the __\_all__ field, I haven't found it yet.  It doesn't seem like this field was built to be customized.  Because of this, we're going to need to use a different method.  Let's try a __match_phrase__ query:

    ```
    GET patients_improved/_search
    {
      "_source": [
        "first_name",
        "last_name",
        "email",
        "guid"
      ],
      "query": {
        "match_phrase": {
          "last_name": "OBrien"
        }
      }
    }
    ```
    
    <details>
    <summary>This response (below) gives us the one search result we want.</summary>
    <p>

    ```json
    {
      "took": 1,
      "timed_out": false,
      "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
      },
      "hits": {
        "total": 1,
        "max_score": 8.352037,
        "hits": [
          {
            "_index": "patients_improved",
            "_type": "patient",
            "_id": "625",
            "_score": 8.352037,
            "_source": {
              "guid": "ae4cda2170d22e599a87bc8d33de40f0",
              "last_name": "O'Brien",
              "first_name": "Conan",
              "email": "conan@example.com"
            }
          }
        ]
      }
    }
    ```
    </p>
    </details>

    In a moment we're going to need to combine the __query_string__ and the __match_phrase__ methods in our Rails code, but in the meantime, this is how to check your work in kibana:

    ```
    GET patients_improved/_search
    {
      "_source": [
        "first_name",
        "last_name",
        "email",
        "guid"
      ],
      "query": {
        "bool": {
          "should": [
            {
              "query_string": {
                "query": "OBrien"
              }
            },
            {
              "match_phrase": {
                "first_name": "OBrien"
              }
            },
            {
              "match_phrase": {
                "last_name": "OBrien"
              }
            }
          ]
        }
      }
    }
    ```
    
    Now we need to redirect the alias from the old index, `patients_development`, to the new one, `patients_improved`.
    
    ```
    POST _aliases
    {
      "actions": [
        {
          "add": {
            "alias": "patients_alias",
            "index": "patients_improved"
          }
        },
        {
          "remove": {
            "alias": "patients_alias",
            "index": "patients_development"
          }
        }
      ]
    }
    ```


  - Now let's go back to Chirp Development in the browser.  [http://icisstaff.localhost/chirp/patients?searchTerm=OBrien](http://icisstaff.localhost/chirp/patients?searchTerm=OBrien) We're still not getting the result from "OBrien".  This is because our ruby code is still only using the `query_string` method to search.  Let's fix that.

  - Go into the file __app/models/es/patient.rb__ and add the following method:

    ```ruby
    def self.query_string(expanded_term)
      {
        bool: {
          should: [
            {
              query_string: {
                query: expanded_term
              }
            },
            {
              match_phrase: {
                first_name: expanded_term
              }
            },
            {
              match_phrase: {
                last_name: expanded_term
              }
            }
          ]
        }
      }
    end
    ```

    We want to add the "match_phrase" queries when the user is typing content into the search bar.  At the same time we don't want to lose any of the old functionality that the user expects.  The only option is to use a "should" construction, which is the same as an "OR" construction.  The old query_string is wrapped in a `{ bool: { should: { ... } }` object, and we add the `match_phrase` query for first and last names.
