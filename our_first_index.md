# Our First Elasticsearch Index

## Main takeaways from this exercise.
  - Elasticserch is dynamic.
  - Indices and field types are by default computed just in time

## Warning:
  - PLEASE USE THE KIBANA CONSOLE WHEN FOLLOWING ALONG.  If you use *curl* in your bash terminal you might accidentally use the wrong port number and connect to the wrong Elasticsearch cluster.  For this Chirp-Ed session, elasticsearch lives on port *9211*.  As long as you connect to kibana on http://localhost:5611, you'll be fine.

## Steps Below:

- In the Kibana Conole, let's execute the following command:

  ```
  POST index-1/doc?refresh=true
  {
    "first_name": "Andrew",
    "last_name": "Zimmerman"
  }
  ```

- Notice that "index-1" didn't even exist until now.  Now if we search for the index, we'll find the document:

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
