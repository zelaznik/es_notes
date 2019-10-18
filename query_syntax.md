# Elasticsearch Query DSL Fundamentals

## Setup

- Before we go any further, let's create a new index __index-2__ and seed some data into it.  All of these commands can be run in bulk in kibana by selecting/highlighting them all and then pressing PLAY.

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

## Query Strings

- The freest form way to query search results is with the query string.  This is how we allow users in Chirp to type content into search bars.  Let's enter this into Kibana.

  ```
  GET index-2/_search
  {
    "query": {
      "query_string": {
        "query": "Andrew Zimmerman"
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

```
GET index-2/_search
{
  "query": {
    "query_string": {
      "query": "Zimmerman AND Andrew"
    }
  }
}
```

```
GET index-2/_search
{
  "query": {
    "bool": {
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

```
GET index-2/_mapping
```

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

```
GET index-2/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "first_name.keyword": "Becky"
          }
        },
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
            "last_name.keyword": "Zimmerman"
          }
        }
      ]
    }
  }
}
```

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
