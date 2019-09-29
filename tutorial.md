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

This shows us that we have three nodes that are running.  We can see that service is 

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

# Goal: BE ABLE TO GET SAME RESULTS FROM ElasticSearch as from staging app
- Let's look at on