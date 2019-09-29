# Interactive Exhibit

- SSH into the staging environment

```
ssh deployer@staging.icisapp.com
function es() { curl -X $1 "$ELASTICSEARCH_URL/$2" -k 2>/dev/null; }
```

- Elastic Search uses a REST-API.  This `es` function is purely to save ourselves a lot of typing.
- The `-k` flag is because we're using self signed certificates.
- Let's type this into the search engine:

```
$ es GET _cat/nodes?pretty
10.0.3.200 10.0.3.200  7 61 0.08 d m services-03
10.0.2.200 10.0.2.200 11 94 0.00 d * services-02
10.0.1.200 10.0.1.200 15 93 0.00 d m services-01
```

This shows us that we have three nodes that are running.  Here's a diagram on how ElasticSearch is distributed.  I stole this from the LinuxAcademy tutorial that I'm going through.

https://raw.githubusercontent.com/zelaznik/es_notes/master/elastic_diagram.gif?token=ACMKJQP6YHNKHTBZZKTK6WS5TI2SA



- 

# Elastic Search Terms Defined:
  ## Node:
    - Description TBA
  ## Shard:
    - Description TBA
  ## Document:
    - Description TBA
  ## Index:
    - Description TBA

# Security Issues With ElasticSearch:
- A plain HTTP endpoint can be accessed by anybody.
- Iora Limits the IP addresses from which the ES clusters can be accessed?

# Query Terms:
 - must: (exact match, equivalent to AND)
 - must_not: (exact NOT match)
 - should: (equivalent to OR)
     - the default for "SHOULD" is to match one or more terms.

# Goal:
  ## BE ABLE TO GET SAME RESULTS FROM ElasticSearch as from staging app
      - Let's look the tasks page in staging.
      - Here's how to find the elastic-search queries in staging:
         ```
         ssh deployer@staging.icisapp.com
         cd apps/icis/staging/current
         cat log/staging.log | egrep "curl -X GET 'http://localhost:9200"
         ```
