# Aggregations

```
GET patients_improved/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "active": true
          }
        }
      ]
    }
  },
  "aggs": {
    "patients_per_practice": {
      "terms": {
        "field": "practice_id"
      },
      "aggs": {
        "active_or_not": {
          "terms": {
            "field": "active"
          }
        }
      }
    }
  }
}
```

<details>
  <summary>And here is the response (below)</summary>
  <p>
  
  ```json
  {
    "took": 7,
    "timed_out": false,
    "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
    },
    "hits": {
      "total": 19993,
      "max_score": 0,
      "hits": []
    },
    "aggregations": {
      "patients_per_practice": {
        "doc_count_error_upper_bound": 0,
        "sum_other_doc_count": 46,
        "buckets": [
          {
            "key": 98,
            "doc_count": 10566,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 10566
                }
              ]
            }
          },
          {
            "key": 97,
            "doc_count": 7915,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 7915
                }
              ]
            }
          },
          {
            "key": 93,
            "doc_count": 513,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 513
                }
              ]
            }
          },
          {
            "key": 1,
            "doc_count": 507,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 507
                }
              ]
            }
          },
          {
            "key": 3,
            "doc_count": 341,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 341
                }
              ]
            }
          },
          {
            "key": 18,
            "doc_count": 33,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 33
                }
              ]
            }
          },
          {
            "key": 2,
            "doc_count": 25,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 25
                }
              ]
            }
          },
          {
            "key": 87,
            "doc_count": 17,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 17
                }
              ]
            }
          },
          {
            "key": 12,
            "doc_count": 15,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 15
                }
              ]
            }
          },
          {
            "key": 15,
            "doc_count": 15,
            "active_or_not": {
              "doc_count_error_upper_bound": 0,
              "sum_other_doc_count": 0,
              "buckets": [
                {
                  "key": 1,
                  "key_as_string": "true",
                  "doc_count": 15
                }
              ]
            }
          }
        ]
      }
    }
  }
  ```
</details>
