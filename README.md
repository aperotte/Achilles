Achilles
========
 
Automated Characterization of Health Information at Large-scale Longitudinal Evidence Systems (ACHILLES) - descriptive statistics about a OMOP CDM v4/v5 database

Getting Started
===============
(Please review the [Achilles Wiki](https://github.com/OHDSI/Achilles/wiki/Additional-instructions-for-Linux) for specific details for Linux)

1. Make sure you have your data in the OMOP CDM v4/v5 format  (v4 link http://omop.org/cdm v5 link:http://www.ohdsi.org/web/wiki/doku.php?id=documentation:cdm).

2. Make sure that you have Java installed. If you don't have Java already intalled on your computed (on most computers it already is installed), go to [java.com](http://java.com) to get the latest version.  (If you have trouble building with rJava below, be sure on Windows that your Path variable includes the path to jvm.dll (Windows Button --> type "path" --> Edit Environmental Variables --> Edit PATH variable, add to end ;C:/Program Files/Java/jre/bin/server) or wherever it is on your system.)

3. in R, use the following commands to install Achilles (if you have prior package installations of aony of these packages, you may need to first unistall them using the command remove.packages()).

  ```r
  install.packages("devtools")
  library(devtools)
  install_github("ohdsi/SqlRender")
  install_github("ohdsi/DatabaseConnector")
  install_github("ohdsi/Achilles")
  ```
  
4. To run the Achilles analysis, use the following commands in R:

  ```r
  library(Achilles)
  connectionDetails <- createConnectionDetails(dbms="redshift", server="server.com", user="secret",
                              password='secret', schema="cdm5_inst", port="5439")
  achillesResults <- achilles(connectionDetails, cdmDatabaseSchema="cdm5_inst", 
                              resultsDatabaseSchema="results", sourceName="My Source Name", 
                              cdmVersion = "cdm version", vocabDatabaseSchema="vocabulary")
  ```
  "cdm4_inst" cdmDatabaseSchema parmater, "results" resultsDatabaseSchema parameter, and "vocabulary" vocabDatabaseSchema are the names of the schemas holding the CDM data, targeted for result writing, and holding the Vocabulary data respectively. See the [DatabaseConnector](https://github.com/OHDSI/DatabaseConnector) package for details on settings the connection details for your database, for example by typing
  
  Execution of all Achilles pre-computations may take a long time. See notes.md file to find out how some analyses can be excluded to make the execution faster (excluding cost pre-computations) 
  ```r
  ?createConnectionDetails
  ```
  Currently "sql server", "oracle", "postgresql", and "redshift" are supported as dbms.
  "cdmVersion" can be either 4 or 5.

5. To use [AchillesWeb](https://github.com/OHDSI/AchillesWeb) to explore the Achilles statistics, you must first export the statistics to JSON files:
  ```r
  exportToJson(connectionDetails, cdmDatabaseSchema = "cdm4_inst", resultsDatabaseSchema = "results", outputPath = "c:/myPath/AchillesExport", cdmVersion = "cdm version", vocabDatabaseSchema = "vocabulary")
  ```

6. To run only Achilles Heel (component of Achilles), use the following command:
  ```r
  achillesHeel(connectionDetails, cdmDatabaseSchema = "cdm4_inst", resultsDatabaseSchema = "results", cdmVersion = "cdm version", vocabDatabaseSchema = "vocabulary")
  ```

Getting Started with Docker
===========================
This is an alternative method for running Achilles that does not require R and Java installations, using a Docker container instead.

1. Install [Docker](https://docs.docker.com/installation/) and [Docker Compose](https://docs.docker.com/compose/install/).

2. Clone this repository with git (`git clone https://github.com/OHDSI/Achilles.git`) and make it your working directory (`cd Achilles`).

3. Copy `env_vars.sample` to `env_vars` and fill in the variable definitions. The `ACHILLES_DB_URI` should be formatted as `<dbms>://<username>:<password>@<host>/<schema>`.

4. Copy `docker-compose.yml.sample` to `docker-compose.yml` and fill in the data output directory.

5. Build the docker image with `docker-compose build`.

6. Run Achilles in the background with `docker-compose run -d achilles`.

Alternatively, you can run it with one long command line, like in the following example:

```bash
docker run \
  --rm \
  --net=host \
  -v "$(pwd)"/output:/opt/app/output \
  -e ACHILLES_SOURCE=DEFAULT \
  -e ACHILLES_DB_URI=postgresql://webapi:webapi@localhost:5432/ohdsi \
  -e ACHILLES_CDM_SCHEMA=cdm5 \
  -e ACHILLES_VOCAB_SCHEMA=cdm5 \
  -e ACHILLES_RES_SCHEMA=webapi \
  -e ACHILLES_CDM_VERSION=5 \
  <image name>
```

License
=======
Achilles is licensed under Apache License 2.0

Development
===========
Achilles is being developed in R Studio.

###Development status
[![Build Status](https://travis-ci.org/OHDSI/Achilles.svg?branch=master)](https://travis-ci.org/OHDSI/Achilles)
[![codecov.io](https://codecov.io/github/OHDSI/Achilles/coverage.svg?branch=master)](https://codecov.io/github/OHDSI/Achilles?branch=master)



# Acknowledgements
- This project is supported in part through the National Science Foundation grant IIS 1251151.
