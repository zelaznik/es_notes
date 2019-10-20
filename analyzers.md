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

## Custom Analyser
 - Later in the exercise we're going to want to modify Chirp so that users don't have to type apostrophes and other special characters when searching for patients.  In order to do that, we need to build a custom analyzer.  Let's do that here.  I'm skpping a lot of steps with this next command, but I'll try to explain them all.

    ```
    PUT index-3
    {
      "analysis": {
        "analyzer": {
          "alphanumeric_only": {
            "filter": [
              "lowercase"
            ],
            "char_filter": [
              "remove_special_chars"
            ],
            "type": "custom",
            "tokenizer": "standard"
          }
        },
        "char_filter": {
          "remove_special_chars": {
            "type": "pattern_replace",
            "pattern": "[^a-zA-Z \t]",
            "replacement": ""
          }
        }
      }
    }
    ```

  - A custom analyzer very much feels like functional programming.  We're daisy chaining a series of immutable functions together.  First we filter the text string.  Then we run an ordered array of character filters.  Then we tokenize them.  In this case, all we want to do is remove anything that's not alpha numeric.  It's important that we preserve the spaces so that multiple words can be broken into multiple tokens.  Now the important thing is to check our work.

  - Because we added this custom analyzer `alphanumeric_only` to the index `index-3`, we'll use that endpoint to check our work.
  
    ```
    POST index-3/_analyze
    {
      "text": "O'Brien",
      "analyzer": "alphanumeric_only"
    }
    ```
   
    <details>
      <summary>Notice the token (below) is lowercase and ignores the apostrophe.</summary>
      <p>

      ```json
      {
        "tokens": [
          {
            "token": "obrien",
            "start_offset": 0,
            "end_offset": 7,
            "type": "<ALPHANUM>",
            "position": 0
          }
        ]
      }
      ```
      </p>
    </details>
