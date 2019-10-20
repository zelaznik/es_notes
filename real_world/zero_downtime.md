# Zero Downtime

- As a No-SQL database, Elasticsearch does not have the ACID guarantees that one might get with a Postgres migration.  As a result, we need to be more creative when upgrading indices to maintain zero downtime.  The makers of elasticsearch created a [blog post](https://www.elastic.co/blog/changing-mapping-with-zero-downtime) on this very topic, which we're going to do here.  The steps are as follows:

  - Create an alias that points to the old index
  - Update the code in Rails to point to the alias
  - Create a new index with the new analyzer for the fields __first_name__ and __last_name__.
  - Copy all the data from the old index into the new one
  - Update the alias to point to the new index
  - Delete the old index
