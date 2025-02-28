# 🚀 Create SingleStore Pipeline
Guide to setting up a SingleStore Pipeline using SQL commands. It includes a step-by-step guide for creating a database, table structures, and pipelines to load data from Kafka topics into SingleStore tables. The guide also covers testing and starting the pipeline, troubleshooting common errors, and key notes to ensure a successful setup.
## 📋 Table of Contents
- [Step-by-Step Guide](#-step-by-step-guide)
- [Troubleshooting](#-troubleshooting)
- [Key Notes](#-key-notes)
- [Resources](#-resources)


## 🚩 Step-by-Step Guide
- Create Database
    ```sql
    CREATE DATABASE IF NOT EXISTS name_of_DB;
    USE name_of_DB;
    ```
- Create Table Structure
   ```sql
   CREATE TABLE name_of_table(
   f1 varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL
   ,f2 varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL
   ,f3 varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL
   );
   ```
- Create Pipeline
   ```sql
   CREATE PIPELINE name_of_pipeline
   AS LOAD DATA KAFKA 'IP:Port/topic_name(ex:localhost:9092/test_docker_kafka)'
   BATCH_INTERVAL 2500
   ENABLE OUT_OF_ORDER OPTIMIZATION
   SKIP CONSTRAINT ERRORS
   INTO TABLE name_of_table
   FORMAT AVRO
   SCHEMA REGISTRY 'IP:Port(ex:localhost:8081)'
   (
   `name_of_table`.`f1` <- `f1`,
   `name_of_table`.`f2` <- `f2`,
   `name_of_table`.`f3` <- `f3`
   );
   ```
    - Test Pipeline
   ```sql
   TEST PIPELINE name_of_pipeline;
   ```
    - Start Pipeline
   ```sql
   START PIPELINE name_of_pipeline;
   ```
## 🔧 Troubleshooting
  - Debug Pipeline Errors
   ```sql
   SELECT PIPELINE_NAME , ERROR_KIND, ERROR_CODE, ERROR_MESSAGE, LOAD_DATA_LINE_NUMBER, LOAD_DATA_LINE
   FROM information_schema.PIPELINES_ERRORS
   WHERE PIPELINE_NAME = 'pl_docker_kafka';
  ```
   - Common Checks
   ```sql
   SHOW PIPELINES;
   SELECT * FROM name_of_table LIMIT 10;
   ```
## 💡 Key Notes
- Replace all name_of_* placeholders with actual names

- Use actual IP:port values for Kafka brokers and Schema Registry

- Messages written to Kafka will appear in SingleStore automatically

## 📚 Resources
 - [SingleStore Pipeline Documentation](https://docs.singlestore.com/cloud/reference/information-schema-reference/data-ingest/pipelines/)
