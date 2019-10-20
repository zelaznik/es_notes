# Our First Elasticsearch Index

## Main takeaways from this exercise.
  - Elasticserch is dynamic.
  - Indices and field types are by default computed just in time

## Warning:
  - PLEASE USE THE KIBANA CONSOLE WHEN FOLLOWING ALONG.  If you use *curl* in your bash terminal you might accidentally use the wrong port number and connect to the wrong Elasticsearch cluster.  For this Chirp-Ed session, elasticsearch lives on port *9211*.  As long as you connect to kibana on http://localhost:5611, you'll be fine.

## Add Data To The Index:

- In the Kibana Conole, let's execute the following command:

  ```
  POST index-1/doc?refresh=true
  {
    "first_name": "Andrew",
    "last_name": "Zimmerman"
  }
  ```

  <details><summary>Notice that the response body (below) contains the id of the record</summary>
  <p>

  ```json
  {
    "_index": "index-1",
    "_type": "doc",
    "_id": "AW3eJDcBwmLbVN91LFF7",
    "_version": 1,
    "result": "created",
    "forced_refresh": true,
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

- In the example above we let elasticsearch pick the ID for us.  We can also choose our own unique identifier.  We do this in Chirp, 

  ```
  PUT index-1/doc/2718
  {
    "first_name": "Leonhard",
    "last_name": "Euler"
  }
  ```

## Getting Data From The Index:

- Now we can fetch the document by its ID.

  ```
  GET index-1/doc/2718
  ```

  <details><summary>response (below)</summary>
  <p>

  ```json
  {
    "_index": "index-1",
    "_type": "doc",
    "_id": "2718",
    "_version": 1,
    "found": true,
    "_source": {
      "first_name": "Leonhard",
      "last_name": "Euler"
    }
  }
  ```

  </p>
  </details>

## Updating Data To The Index:

- In the Kibana Conole, let's execute the following command:

  ```
  POST index-1/doc/2718/_update?pretty
  {
    "doc": {
      "last_name": "Shelby"
    }
  }
  ```

  <details>
  <summary>Now let's take another look at our document returned to us:</summary>
  <p>

    ```json
    {
      "_index": "index-1",
      "_type": "doc",
      "_id": "2718",
      "_version": 2,
      "found": true,
      "_source": {
        "first_name": "Leonhard",
        "last_name": "Shelby"
      }
    }
    ```
  </p>
  </details>

- The *_version* attribute has been incremented to "2".  Documents are immutable in elasticsearch.  If you perform an update, you delete version __x__ of a document and replace it with version __x+1__.
- We only updated the __last_name__ field.  All the other fields remain intact in this new version of the document.

- This only works with the ___update__ endpoint.  If we use the __PUT__ request, we'll end up overwriting the entire document.  Let's try it:

  ```
  PUT index-1/doc/2718?pretty
  {
    "doc": {
      "first_name": "Richard"
    }
  }
  ```

  <details>
  <summary>Now the field "last_name" has disappeared from the response (below)</summary>
  <p>

    ```json
    {
      "_index": "index-1",
      "_type": "doc",
      "_id": "2718",
      "_version": 3,
      "found": true,
      "_source": {
        "first_name": "Richard"
      }
    }
    ```
  </p>
  </details>

## Deleting Data From The Index

- You probably guessed from the REST-API CRUD pattern that we'd be sending a DELETE request to the same endpoint.

  ```
  DELETE index-1/doc/2718
  ```

  <details><summary>In the response body (below), we're told that the document was found and successfully deleted.</summary>
  <p>

  ```json  
  {
    "found": true,
    "_index": "index-1",
    "_type": "doc",
    "_id": "2718",
    "_version": 2,
    "result": "deleted",
    "_shards": {
      "total": 2,
      "successful": 1,
      "failed": 0
    }
  }
  ```
  </p>
  </details>

## Key Takeaways

- Notice that "index-1" didn't even exist before this exercise.  It was generated in real time.
- This is flexible, but it makes elasticsearch vulnerable to typos.  If I misspell the name of the index, elasticsearch will simply create a new index and never return an error to me.
