# Use JDBC Connector

This guide provides detailed instructions on how to set up and use the JDBC Connector with Kafka, Confluent, SingleStore, and MySQL. It covers the installation of the JDBC Connector plugin, configuration of JDBC connectors, and deploying them via the Control Center UI.

## 📋 Table of Contents
- [Install JDBC Connector Plugin](#-install-jdbc-connector-plugin)
- [SingleStore with JDBC Connector](#-singlestore-with-jdbc-connector)
- [MySQL with JDBC Connector](#-mysql-with-jdbc-connector)
- [Configure JDBC Connectors](#-configure-jdbc-connectors)
- [Deploy JDBC Connectors via Control Center UI](#-deploy-jdbc-connectors-via-control-center-ui)
- [Resources](#-resources)


## 🚀 Install JDBC Connector Plugin

### 🔧 Step 1: Install Plugin
Run the following command inside the `connect` container:
```bash
docker exec -it connect bash
confluent-hub install confluentinc/kafka-connect-jdbc:10.7.3
exit
```
## 🗄 SingleStore with JDBC Connector
### 📥 Step 1: Add JDBC Driver
1. Download the SingleStore JDBC driver from [S2-JDBC-Connector Releases](https://github.com/memsql/S2-JDBC-Connector/releases).

2. Copy the JAR file to the connect container:

``` bash
docker cp singlestore-jdbc-<version>.jar connect:/usr/share/confluent-hub-components/confluentinc-kafka-connect-jdbc/lib/
```
3. Restart the connect container:

```bash
docker-compose restart connect
```
### ✅  Step 2: Verify Installation
Check if the driver is installed:

``` bash
docker exec connect ls /usr/share/confluent-hub-components/confluentinc-kafka-connect-jdbc/lib/
```
## 🐬 MySQL with JDBC Connector
### Add MySQL to Docker Compose
#### Add the following to your `docker-compose.yml`:

```yaml
mysql:
image: mysql:5.7
hostname: mysql
environment:
MYSQL_ROOT_PASSWORD: root
MYSQL_DATABASE: kafka_mysql
ports:
- "3307:3306"  # Use a different port if SingleStore is running
```
### 🔑 Access MySQL Container
#### Start the container:

``` bash
docker-compose up -d
```
### Access the MySQL shell:

```bash
docker exec -it mysql mysql -uroot -proot
```
## ⚙ Configure JDBC Connectors
#### 📤 Source Connector (`source_connector.json`)
This connector creates topics and schemas automatically.

```json
{
    "name":"Source_connector_Name",
    "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
    "transforms": "setSchema",
    "transforms.setSchema.type": "org.apache.kafka.connect.transforms.SetSchemaMetadata$Value",
    "transforms.setSchema.schema.name": "Schema_Name",
    "connection.url": "jdbc:singlestore://memsql:3306/DB_Name",
    "connection.user": "root",
    "connection.password": "root",
    "tasks.max":"1",
    "query": "SELECT * FROM Your_Table_Name",
    "table.type": "TABLE",
    "topic.prefix": "Topic_Name",
    "incrementing.column.name": "Your_Column_Name",
    "mode":"incrementing",
    "numeric.mapping":"best_fit",
    "poll.interval.ms": "5000",
    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081",
    "value.converter.schema.registry.url": "http://schema-registry:8081"
}
```
#### 📥 Sink Connector (sink_connector.json)
This connector creates tables automatically.

```json
{
    "name":"test03_Sink_Connector",
    "connector.class": "io.confluent.connect.jdbc.JdbcSinkConnector",
    "topics": "Your_Topic_Name",
    "connection.url": "jdbc:singlstore://memsql:3306/DB_Name",
    "connection.user": "root",
    "connection.password": "root",
    "tasks.max":"1",
    "auto.create":"true",
    "auto.evolve": "true",
    "insert.mode": "insert",
    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081",
    "value.converter.schema.registry.url": "http://schema-registry:8081"

}
```
## 🖥 Deploy JDBC Connectors via Control Center UI
### Step 1: Access Control Center
1. Open your browser and navigate to:
http://localhost:9021

2. Go to the "Connect" section in the left-hand menu.

### Step 2: Deploy Source Connector
1. Click "Add Connector".

2. Select "JDBC Source Connector OR JDBC Sink Connector".

3. Fill in the configuration fields using the following template:

4. Click "Launch" to deploy the connector.

## ✅ Verify Connectors
1. Go back to the "Connect" section in Control Center.

2. Check the status of your connectors:

    - 🟢 Green: Running successfully.

    - 🔴 Red: Error (click to view logs and troubleshoot).

## 📚 Resources
[Confluent JDBC Connector Documentation](https://docs.confluent.io/kafka-connectors/jdbc/current/overview.html)

[SingleStore JDBC Driver Releases](https://github.com/memsql/S2-JDBC-Connector/releases)

[Confluent Control Center Guide](https://docs.confluent.io/platform/current/control-center/index.html)

[MySQL Docker Image Documentation](https://hub.docker.com/_/mysql)
