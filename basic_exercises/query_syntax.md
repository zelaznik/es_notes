# Elasticsearch Query DSL Fundamentals

## Setup

- Before we go any further, let's create a new index __index-2__ and seed some data into it.  

  <details>
    <summary>All of these commands (below) can be run in bulk in kibana by selecting/highlighting them all and then pressing PLAY.</summary>
    <p>

  ```
  PUT index-2/doc/1?refresh=true
  {
    "first_name": "Becky",
    "last_name": "Young"
  }

  PUT index-2/doc/2?refresh=true
  {
    "first_name": "Becky",
    "last_name": "Zimmerman"
  }

  PUT index-2/doc/3?refresh=true
  {
    "first_name": "Andrew",
    "last_name": "Young"
  }

  PUT index-2/doc/4?refresh=true
  {
    "first_name": "Andrew",
    "last_name": "Zimmerman"
  }

  PUT index-2/doc/5?refresh=true
  {
    "first_name": "Conan",
    "last_name": "O'Brien"
  }
  ```
  </p>
  </details>

## Query Strings

- The freest form way to query search results is with the query string.  This is how we allow users in Chirp to type content into search bars.  Let's enter this into Kibana.

  ```
  GET index-2/_search
  {
    "query": {
      "query_string": {
        "query": "Zimmerman Andrew"
      }
    }
  }
  ```

  <details><summary>The results (below) match any word to any field.  It's an OR query of OR queries</summary>
  <p>

  ```json
  {
    "took": 2,
    "timed_out": false,
    "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
    },
    "hits": {
      "total": 3,
      "max_score": 0.78549397,
      "hits": [
        {
          "_index": "index-2",
          "_type": "doc",
          "_id": "4",
          "_score": 0.78549397,
          "_source": {
            "first_name": "Andrew",
            "last_name": "Zimmerman"
          }
        },
        {
          "_index": "index-2",
          "_type": "doc",
          "_id": "3",
          "_score": 0.25811607,
          "_source": {
            "first_name": "Andrew",
            "last_name": "Young"
          }
        },
        {
          "_index": "index-2",
          "_type": "doc",
          "_id": "2",
          "_score": 0.16358379,
          "_source": {
            "first_name": "Becky",
            "last_name": "Zimmerman"
          }
        }
      ]
    }
  }
  ```

  </p>
  </details>

- If we want both words to match, we have to specify the __AND__ operator.

  ```
  GET index-2/_search
  {
    "query": {
      "query_string": {
        "query": "Andrew AND Zimmerman"
      }
    }
  }
  ```

  <details><summary>Now we get back one single result (below)</summary>
  <p>

  ```json
  {
    "took": 2,
    "timed_out": false,
    "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
    },
    "hits": {
      "total": 1,
      "max_score": 0.78549397,
      "hits": [
        {
          "_index": "index-2",
          "_type": "doc",
          "_id": "4",
          "_score": 0.78549397,
          "_source": {
            "first_name": "Andrew",
            "last_name": "Zimmerman"
          }
        }
      ]
    }
  }
  ```

  </p>
  </details>

## Filter Query

- The filter query is like the "WHERE" clause in a SQL database.  It's like a match query, except that it doesn't affect the search ranking.  In the next section we'll cover why we use the `.keyword` suffix in these searches.

  ```
  GET index-2/_search
  {
    "query": {
      "bool": {
        "filter": [
          {
            "term": {
              "last_name.keyword": "Zimmerman"
            }
          }
        ]
      }
    }
  }
  ```

  <details><summary>You should see two documents in the response(below)</summary>
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
      "total": 2,
      "max_score": 0,
      "hits": [
        {
          "_index": "index-2",
          "_type": "doc",
          "_id": "2",
          "_score": 0,
          "_source": {
            "first_name": "Becky",
            "last_name": "Zimmerman"
          }
        },
        {
          "_index": "index-2",
          "_type": "doc",
          "_id": "4",
          "_score": 0,
          "_source": {
            "first_name": "Andrew",
            "last_name": "Zimmerman"
          }
        }
      ]
    }
  }
  ```

  </p>
  </details>

- As an exercise, try removing the `.keyword` and you'll find you get no results back.

- Filter terms can be aggregated together:

  ```
  GET index-2/_search
  {
    "query": {
      "bool": {
        "filter": [
          {
            "term": {
              "first_name": "Becky"
            }
          },
          {
            "term": {
              "last_name": "Zimmerman"
            }
          }
        ]
      }
    }
  }
  ```

- We can also mix query strings and filter contexts:

  ```
  GET index-2/_search
  {
    "query": {
      "bool": {
        "must": [
          {
            "query_string": {
              "query": "Becky"
            }
          }
        ],
        "filter": [
          {
            "term": {
              "last_name": "Zimmerman"
            }
          }
        ]
      }
    }
  }
  ```

## Recreating an "OR" query.

- In elasticsearch 5, they got rid of the "OR" query.  They replaced it with the unintuitive "should".  This query should get you three results back.  "Becky Zimmerman", "Becky Young", and "Andrew Zimmerman"

  ```
  GET index-2/_search
  {
    "query": {
      "bool": {
        "should": [
          {
            "match": {
              "first_name": "Becky"
            }
          },
          {
            "match": {
              "last_name": "Zimmerman"
            }
          }
        ]
      }
    }
  }
  ```

- If we want more than one term to match, we can change the `minimum_should_match` parameter, as in here.

  ```
  GET index-2/_search
  {
    "query": {
      "bool": {
        "should": [
          {
            "match": {
              "first_name": "Becky"
            }
          },
          {
            "match": {
              "last_name": "Zimmerman"
            }
          }
        ],
        "minimum_number_should_match": 2
      }
    }
  }
  ```

# LIVE CODING EXERCISE

- Go to the tasks, admissions, or patients pages in Chirp staging or development.  Try to build queries in elasticsearch that give you equivalent results.  Time permitting, we'll do that live here in Chirp-Ed.

### Up Next [Mappings](mappings.md)
