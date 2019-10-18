# Summary:

- Elastic Search is a distributed, REST API
- Based on [Apache Lucene](http://lucene.apache.org/)
- "Just in time" schema implementation
- Flexible text analysis

# Goals of Tutorial
- Become familiar with Rest-API and query patterns
- Be able to debug Elasticsearch in Chirp


# Installation Guide

- Download this git repository

    ```sh
    cd ~/src/
    git clone git@github.com:zelaznik/es_notes.git
    cd es_notes
    ```

- Start up the docker images

    ```sh
    ~/src/es_notes(master)
    $ docker-compose up -d
    Creating network "es_notes_default" with the default driver
    Creating volume "es_notes_elasticsearch-chirp-ed" with default driver
    Creating volume "es_notes_elasticsearch-chirp-ed-logs" with default driver
    Creating es_notes_kibana_chirp_ed_1        ... done
    Creating es_notes_elasticsearch_chirp_ed_1 ... done
    ```

- Check that docker is running special containers for both elasticsearch and kibana

    ```sh
    ~/src/es_notes(master)
    $ docker container ls
    CONTAINER ID        IMAGE                               COMMAND                  CREATED              STATUS              PORTS                                                                                        NAMES
    e1348397f01b        kibana:5.1.1                        "/docker-entrypoint.…"   12 seconds ago       Up 10 seconds       0.0.0.0:5611->5601/tcp                                                                       es_notes_kibana_chirp_ed_1
    445299333c41        elasticsearch:5.1.1-alpine          "/docker-entrypoint.…"   12 seconds ago       Up 10 seconds       0.0.0.0:9211->9200/tcp, 0.0.0.0:9311->9300/tcp                                               es_notes_elasticsearch_chirp_ed_1
    684faad37ab0        kibana:4.6.6                        "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:5601->5601/tcp                                                                       chirpstrap_kibana_1
    70d3070bf530        elasticsearch:2.4-alpine            "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:9200->9200/tcp, 9300/tcp                                                             chirpstrap_elasticsearch_1
    fbd6110a4977        postgres:9.4.21-alpine              "docker-entrypoint.s…"   7 days ago           Up 17 minutes       0.0.0.0:5432->5432/tcp                                                                       chirpstrap_db_1
    7637c8b2d433        rabbitmq:3.6.15-management-alpine   "docker-entrypoint.s…"   7 days ago           Up 17 minutes       4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, 15671/tcp, 25672/tcp, 0.0.0.0:15672->15672/tcp   chirpstrap_rabbitmq_1
    d80848840c40        redis:3.2.6-alpine                  "docker-entrypoint.s…"   7 days ago           Up 17 minutes       0.0.0.0:6379->6379/tcp                                                                       chirpstrap_redis_1
    ```
