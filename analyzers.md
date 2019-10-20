# Analyzers

- These are the heart of full text search.  Bodies of text are broken into tokens and which are then searchable.
- Analyzers are often an act of data compression.  INFORMATION CAN BE LOST
  - punctuation
  - special characters
  - singular or plural
  - common words such as "the" can be ignored
  
## English Analyzer

- We can examine analyzers by posting to the `_analyze` endpoint.  Let's to go Kibana:

  ```
  POST _analyze
  {
    "text": "The quick brown fox jumped over the lazy dogs."
    "analyzer": "english"
  }
  ```

  <details>
    <summary>We get back a list of tokens (below)</summary>
    <p>
    
    ```json
    {
      "tokens": [
        {
          "token": "quick",
          "start_offset": 4,
          "end_offset": 9,
          "type": "<ALPHANUM>",
          "position": 1
        },
        {
          "token": "brown",
          "start_offset": 10,
          "end_offset": 15,
          "type": "<ALPHANUM>",
          "position": 2
        },
        {
          "token": "fox",
          "start_offset": 16,
          "end_offset": 19,
          "type": "<ALPHANUM>",
          "position": 3
        },
        {
          "token": "jump",
          "start_offset": 20,
          "end_offset": 26,
          "type": "<ALPHANUM>",
          "position": 4
        },
        {
          "token": "over",
          "start_offset": 27,
          "end_offset": 31,
          "type": "<ALPHANUM>",
          "position": 5
        },
        {
          "token": "lazi",
          "start_offset": 36,
          "end_offset": 40,
          "type": "<ALPHANUM>",
          "position": 7
        },
        {
          "token": "dog",
          "start_offset": 41,
          "end_offset": 45,
          "type": "<ALPHANUM>",
          "position": 8
        }
      ]
    }
    ```
    </p>
  </details>
