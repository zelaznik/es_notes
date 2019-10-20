# Zero Downtime

- As a No-SQL database, Elasticsearch does not have the ACID guarantees that one might get with a Postgres migration.  As a result, we need to be more creative when upgrading indices to maintain zero downtime.  The makers of elasticsearch created a [blog post](https://www.elastic.co/blog/changing-mapping-with-zero-downtime) on this very topic, which we're going to do here.  The steps are as follows:

  - [Create an alias that points to the old index](#create-an-alias-that-points-to-the-old-index)
  - [Update the code in Rails to point to the alias](#update-the-code-in-rails-to-point-to-the-alias)
  - Create a new index with the new analyzer for the fields __first_name__ and __last_name__.
  - Copy all the data from the old index into the new one
  - Update the alias to point to the new index
  - Delete the old index

### Create an alias that points to the old index
  - Enter the following command in kibana:

    ```
    POST _aliases
    {
      "actions": [
        {
          "add": {
            "alias": "patients_alias",
            "index": "patients_development"
          }
        }
      ]
    }
    ```

### Update the code in Rails to point to the alias

  - Open the code on your development machine and apply this change:

    ```diff
    diff --git a/app/models/es/patient.rb b/app/models/es/patient.rb
    index 1790765491..e29193ed85 100644
    --- a/app/models/es/patient.rb
    +++ b/app/models/es/patient.rb
    @@ -38,6 +38,10 @@ module Es
           "patients_#{Rails.env}"
         end

    +    def self.aliased_name
    +      "patients_alias"
    +    end
    +
         def self.document_type
           'patient'
         end
    ```

  - Or you can run this command in the `~/src/icisstaff` directory.

    ```sh
    git apply <<-DIFF
    --- a/app/models/es/patient.rb
    +++ b/app/models/es/patient.rb
    @@ -38,6 +38,10 @@ module Es
           "patients_#{Rails.env}"
         end

    +    def self.aliased_name
    +      "patients_alias"
    +    end
    +
         def self.document_type
           'patient'
         end
    DIFF
    ```

  - Now open up Chirp's development environment in your browser.  Check that the [patient search](http://icisstaff.localhost/chirp/patients?searchTerm=O%27Brien) still works as expected.  You should see exactly one search result, "Conan O'Brien".
  