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

This shows us that we have three nodes that are running.  Now let's compare that to the environment on our local machine.

```
$ curl localhost:9200/_cat/nodes?pretty
172.18.0.3 172.18.0.3 9 75 0.00 d * Alex
```

There is only one node running.

Now let's look all the indexes that we have in elasticsearch.  Let's go back into staging:

```
$ es GET _cat/indices?v
health status index                                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   patient_log_service_staging               5   1     241415            3    226.3mb        113.1mb
green  open   system                                    5   1          0            0      1.5kb           795b
green  open   patient_log_service_unacked_staging       5   1       1469            0        5mb          2.5mb
green  open   .kibana                                   1   2          2            0       64kb         21.3kb
green  open   measurements_staging                      5   1       5940            1        6mb            3mb
green  open   patient_log_service_most_recent_staging   5   1      47770            5     74.4mb         37.2mb
green  open   kibana-int                                5   1          1            0     19.9kb          9.9kb
green  open   admissions_staging                        5   1        159           36      1.1mb        601.1kb
green  open   tasks_staging                             5   1       3679            7      4.1mb            2mb
green  open   notes_staging                             5   1       2524           29      6.5mb          3.2mb
green  open   patients_staging                          5   1      31134           21     75.5mb         37.7mb
green  open   customer                                  5   1          0            0      1.5kb           795b
```

- Notice what we have stored in elastic search:
 - tasks
 - notes
 - patients
 - admissions

- Also note that every index that we use has the environment name as a suffix.

If we go into our development console, we'll see the _development suffix.

```
$ curl localhost:9200/_cat/indices?v
health status index                           pri rep docs.count docs.deleted store.size pri.store.size
yellow open   patient_log_service_development   5   1     241116            0    105.5mb        105.5mb
yellow open   tasks_test                        5   1        195           32    390.6kb        390.6kb
yellow open   patients_test                     5   1         73            5    567.6kb        567.6kb
yellow open   tasks_development                 5   1       2902            0      1.7mb          1.7mb
yellow open   patient_log_service_test          5   1          3            0     52.3kb         52.3kb
yellow open   admissions_test                   5   1         11            0     58.7kb         58.7kb
yellow open   patients_development              5   1      31134            0     37.8mb         37.8mb
yellow open   admissions_development            5   1        156            0    441.7kb        441.7kb
yellow open   .kibana                           1   1          2            0      6.1kb          6.1kb
yellow open   notes_test                        5   1          7            0     68.2kb         68.2kb
yellow open   notes_development                 5   1       2521            0      3.4mb          3.4mb
yellow open   measurements_development          5   1       5944            0      2.9mb          2.9mb
yellow open   measurements_test                 5   1         16            0    135.8kb        135.8kb
```

Notice that we have a `yellow` status locally and a `green` status on staging.

# Health Statuses Explained:

  ## GREEN:
    - One or more nodes can fail and the cluster will not lose any data.
  ## YELLOW:
    - All of the data is available.
    - If one node fails, we will not be able to access all the data.
  ## RED:
    - We cannot access all of our data

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
