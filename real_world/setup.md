# Setting Up Your Environment

- We're going to do this live coding session in your development environment.  Checkout the `md-es5` branch from chirp.  We need to use this branch because today's Chirp-Ed uses Elasticsearch-5, which has breaking changes.  This git branch deals with those breaking changes.

  ```sh
  $ cd ~/src/icisstaff/
  ~/src/icisstaff(master)
  $ git branch -D md-es5 2>/dev/null
  ~/src/icisstaff(master)
  $ git fetch
  ~/src/icisstaff(master)
  $ git checkout -t origin/md-es5
  Branch 'md-es5' set up to track remote branch 'md-es5' from 'origin'.
  Switched to a new branch 'md-es5'
  ```

- Now we want to start up the chirp app, but we want to point Rails to the the version of elasticsearch we've spun up for Chirp-Ed.  We set an environment variable `ELASTICSEARCH_URL='localhost:9211'`.

  ```sh
  ~/src/icisstaff(md-es5)
  $ ELASTICSEARCH_URL='localhost:9211' foreman start
  05:04:12 staff.1  | started with pid 4792
  05:04:12 gulp.1   | started with pid 4793
  05:04:12 ember.1  | started with pid 4794
  05:04:12 resque.1 | started with pid 4795
  05:04:12 rabbit.1 | started with pid 4796
  05:04:13 staff.1  | I, [2019-10-20T05:04:13.662006 #4792]  INFO -- : Refreshing Gem list
  ```

- __WARNING__ if you haven't run the elasticsearch index tasks, please index the patients if you want to continue coding interactively with us.

  ```sh
  ELASTICSEARCH_URL='localhost:9211' bin/rake es:index_patients
  ```
  
- Finally, make sure also to start `snowflake` and `feature_toggles` locally.
