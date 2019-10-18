  ```
  GET index-1/_search
  ```

  <details><summary>Response Body:</summary>
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
      "max_score": 1,
      "hits": [
        {
          "_index": "index-1",
          "_type": "doc",
          "_id": "AW3dn-eVzYJjTv60xAic",
          "_score": 1,
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


- Let's add two more items to the index and then search.

  ```
  POST index-1/doc?refresh=true
  {
    "first_name": "Becky",
    "last_name": "Young"
  }

  POST index-1/doc?refresh=true
  {
    "first_name": "Conan",
    "last_name": "O'Brien"
  }

  GET index-1/_search
  ```

  <details>
  <summary>The results should look like this:</summary>
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
      "total": 3,
      "max_score": 1,
      "hits": [
        {
          "_index": "index-1",
          "_type": "doc",
          "_id": "AW3drIOLzYJjTv60xAie",
          "_score": 1,
          "_source": {
            "first_name": "Andrew",
            "last_name": "Zimmerman"
          }
        },
        {
          "_index": "index-1",
          "_type": "doc",
          "_id": "AW3drIQbzYJjTv60xAif",
          "_score": 1,
          "_source": {
            "first_name": "Becky",
            "last_name": "Young"
          }
        },
        {
          "_index": "index-1",
          "_type": "doc",
          "_id": "AW3drISozYJjTv60xAig",
          "_score": 1,
          "_source": {
            "first_name": "Conan",
            "last_name": "O'Brien"
          }
        }
      ]
    }
  }
  ```
  </p>
  </details>

## Deleting Data


## Querying The Results

```
GET index-1/_search
{
  "query": {
    "simple_query_string": {
      "query": "Andr AND Zimmerman"
    }
  }
}
```

```
GET index-1/_mapping

POST index-1/doc?refresh=true
{
  "first_name": "John",
  "last_name": "Doe",
  "id": "23c5bf4e-2baf-403c-8903-6d6321253d1a"
}

GET index-1/_mapping

POST index-1/doc?refresh=true
{
  "first_name": "Cynthia",
  "last_name": "Bowman",
  "id": 3
}

GET index-1/_search

GET index-1/_mapping/doc

GET index-1/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "first_name.keyword": "Cynthia"
          }
        }
      ]
    }
  }
}
```
