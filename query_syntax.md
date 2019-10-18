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
