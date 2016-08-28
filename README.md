## A simple, ready-to-use docker image of `fhirbase`

A docker simple docker image created from the following ingredients:
- The official `PostgreSQL` image with `plv8` extension installed
- Latest **released** `fhirbase`

Together, this creates a very specific, yet stable docker image suitable for all docker environments.

### Usage

The command

    $ docker pull dryfhir/fhirbase-postgres-plv8

downloads the image locally and you are ready to go. To start the image, you can use

    $ docker run -d -v fhirbasevolume:/var/lib/postgresql/data --name postgres dryfhir/fhirbase-postgres-plv8

On first startup, the image will run all needed `CREATE` statements, which may take some time. During this time, the database is not available yet.

You can now use

    $ docker run --name some-app --link postgres -d application-that-uses-postgres

to connect your app with the fhirbase image.

### Changes versus the original `postgres` and `postgres-plv8` images

1. The database namespace and user are `fhirbase`. That means that all functions and tables created with the initialization script are found in the fhirbase namespace and you should connect as that user.
2. The `plv8` extension is activated for every connection by default. This may be seen as a security risk. however, since the purpose of this image is to use fhirbase, which needs `plv8`, this is a quality of life change for the developer.

### Update policy

Tags are used to publish new version of the image with the latest `fhirbase` sql. For those releases, the latest `clkao/postgres-plv8:9.4-x` image is used.

If images with volumes of older `fhirbase` installations are started, there is currently no automigration. Instead you'll want to run the patch.sql file of newer releases to get you up to date. For that, you need to start up the image, then run

    $ docker exec -i <container-id> /usr/lib/postgresql/9.4/bin/psql -U fhirbase fhirbase < <patchfile.sql>