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
