# Building a "Hello, world" index in Elasticsearch

- The explanations will be filled in at a later time.
- In the meantime, here is a list of instructions to type into the kibana console.

```
DELETE index-1

POST index-1/doc?refresh=true
{
  "first_name": "Andrew",
  "last_name": "Zimmerman"
}

GET index-1/_search

POST index-1/doc?refresh=true
{
  "first_name": "Becky",
  "last_name": "Young"
}

GET index-1/_search

POST index-1/doc?refresh=true
{
  "first_name": "Conan",
  "last_name": "O'Brien"
}

GET index-1/_search

GET index-1/_search
{
  "query": {
    "simple_query_string": {
      "query": "Andr AND Zimmerman"
    }
  }
}

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
