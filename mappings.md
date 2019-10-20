# Mappings

## Bullet Points:

  - Mappings are the equivalent of a database schema.  It tells us what fields to expect and in what format.
  - New fields can be added to a mapping at any time.
  - An existing field in a mapping cannot be changed.
  - If you want to change the data type of a particular field, you have to create a new index entirely.

## Build a mapping from scratch

  - Let's create a new index with one item in it:

    ```
    PUT index-4/doc/1
    {
      "created_at": "2019-01-01"
    }
    ```

  - Now let's see what the index looks like:

    ```
    GET index-4
    ```

    <details>
      <summary>Notice that elasticsearch mapping (below) looked at a string and inferred that it was a date.</summary>
      <p>
      
      ```json
      {
        "index-4": {
          "aliases": {},
          "mappings": {
            "doc": {
              "properties": {
                "created_at": {
                  "type": "date"
                }
              }
            }
          },
          "settings": {
            "index": {
              "creation_date": "1571543778163",
              "number_of_shards": "5",
              "number_of_replicas": "1",
              "uuid": "1KdOyfIiTzm2tcwedMePCA",
              "version": {
                "created": "5010199"
              },
              "provided_name": "index-4"
            }
          }
        }
      }
      ```
      </p>
    </details>

  - Now I'm going to create another document that doesn't fit the date format

    ```
    PUT index-4/doc/2
    {
      "created_at": "hello, world"
    }
    ```

    <details>
      <summary>Elastic search throws an error (below)</summary>
      <p>
      
      ```json
      {
        "error": {
          "root_cause": [
            {
              "type": "mapper_parsing_exception",
              "reason": "failed to parse [created_at]"
            }
          ],
          "type": "mapper_parsing_exception",
          "reason": "failed to parse [created_at]",
          "caused_by": {
            "type": "illegal_argument_exception",
            "reason": "Invalid format: \"hello, world\""
          }
        },
        "status": 400
      }
      ```
      </p>
    </details>

  - __ORDER OF OPERATIONS MATTERS__
  
  - Now let's create a new index, and add the same documents, but in the opposite order.

    ```
    PUT index-5/doc/1
    {
      "created_at": "hello, world"
    }
    ```

  - Before adding the second document, let's look at the index mapping

    ```
    GET index-5
    ```

    <details>
      <summary>Notice that the mapping (below) has "created_at" as type "text".  (We'll get to the "fields" property in a moment.)</summary>
      <p>
    
    ```json
    {
      "index-5": {
        "aliases": {},
        "mappings": {
          "doc": {
            "properties": {
              "created_at": {
                "type": "text",
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
          "index": {
            "creation_date": "1571544228493",
            "number_of_shards": "5",
            "number_of_replicas": "1",
            "uuid": "cCbBKzYMTpuRZ-G_iQQghQ",
            "version": {
              "created": "5010199"
            },
            "provided_name": "index-5"
          }
        }
      }
    }
    ```
  </p>
  </details>

  - Now we can add the second document.
  
    ```
    PUT index-5/doc/2
    {
      "created_at": "2019-01-01"
    }
    ```

    <details>
    <summary>Elasticsearch acknowledges that the record was created (below)</summary>
    <p>

    ```json
    {
      "_index": "index-5",
      "_type": "doc",
      "_id": "2",
      "_version": 3,
      "result": "created",
      "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
      },
      "created": true
    }
    ```
    </p>
    </details>

  - The record was created without error, but storing a date as text is not as useful.  In the last lesson we skipped [range queries](https://www.elastic.co/guide/en/elasticsearch/reference/5.1/query-dsl-range-query.html).  For example "Get me all the records after January 1st, 2019.".  If the date is stored as pure text, elasticsearch will throw an error if you try to run a range query.

## Text vs Keyword Fields

  - Let's go back to our "index-2" field that we created in an earlier lesson.
  - We're going to send elasticsearch a query that will throw an error

    ```
    GET index-2/_search
    {
      "query": {
        "query_string": {
          "query": "*"
        }
      },
      "sort": [
        "last_name",
        "first_name"
      ]
    }
    ```

    <details>
    <summary>Because <b>last_name</b> and <b>first_name</b> are full text fields, that means that all the tokens have to be reassembled in order to sort on them.  This is computationally expensive as is explained in the error (below).</summary>
    <p>

    ```json
    {
      "error": {
        "root_cause": [
          {
            "type": "illegal_argument_exception",
            "reason": "Fielddata is disabled on text fields by default. Set fielddata=true on [last_name] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory."
          }
        ],
        "type": "search_phase_execution_exception",
        "reason": "all shards failed",
        "phase": "query",
        "grouped": true,
        "failed_shards": [
          {
            "shard": 0,
            "index": "index-2",
            "node": "UluhRfZ7TCyCVTbPRqIaSA",
            "reason": {
              "type": "illegal_argument_exception",
              "reason": "Fielddata is disabled on text fields by default. Set fielddata=true on [last_name] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory."
            }
          }
        ],
        "caused_by": {
          "type": "illegal_argument_exception",
          "reason": "Fielddata is disabled on text fields by default. Set fielddata=true on [last_name] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory."
        }
      },
      "status": 400
    }
    ```
    </p>
    </details>

  - To get around this problem, the text fields are stored twice.  The first way, the "text" field, has the string broken into tokens, indexed and and analyzed.  The second copy, the "keyword" field, stores the original text.  When searching the "keyword" field, it either matches or it doesn't.
  - Almost only counts in horseshoes and hand grenades.
