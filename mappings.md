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
