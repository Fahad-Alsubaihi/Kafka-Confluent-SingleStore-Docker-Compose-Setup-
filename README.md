# Kafka + Confluent + SingleStore Docker Compose Setup 🐳

A Docker Compose setup for building real-time data pipelines between Apache Kafka and SingleStore (MemSQL). This repository provides a ready-to-use template for integrating Kafka with SingleStore using JDBC connectors, with additional support for MySQL. Perfect for streaming data applications and real-time analytics.

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Access Services](#-access-services)
- [Troubleshooting](#-troubleshooting)
- [Advanced Usage](#-advanced-usage)
- [SingleStore Pipeline](#-singlestore-pipeline)
- [JDBC Connector](#-jdbc-connector)
- [Resources](#-resources)

## 🛠 Prerequisites

- Docker Engine 20.10+
- Docker Compose 2.4+
- SingleStore License Key ([Get Free Trial](https://portal.singlestore.com))
- Minimum 8GB RAM allocated to Docker

## 🚀 Quick Start

### 1. Configure Environment

1. Download `docker-compose.yml`
2. Insert your SingleStore license key ([singlestore.com](https://www.singlestore.com/)):
   ```yaml
   environment:
     LICENSE_KEY: ###  # 👈 Replace this
   ```

### 2. Start Containers

```yaml
docker-compose up -d
```

### 3. Verify Services

| Service                  | URL                   | Credentials   |
| ------------------------ | --------------------- | ------------- |
| Confluent Control Center | http://localhost:9021 | none          |
| SingleStore Studio       | http://localhost:8080 | `root`/`root` |

## 🔍 Access Services

### SingleStore Studio

1. Login at http://localhost:8080

2. Create database:

```sql
CREATE DATABASE kafka_demo;
USE kafka_demo;
```

### Confluent Control Center

1. Open http://localhost:9021

2. Monitor cluster health and topics

## ⚠️ Troubleshooting

### Image Pull Errors

If `docker-compose up` fails, pull images manually:

```bash
docker pull confluentinc/cp-zookeeper:7.3.2

docker pull confluentinc/cp-server:7.3.2

docker pull memsql/cluster-in-a-box

docker pull confluentinc/cp-schema-registry:7.3.2

docker pull cnfldemos/cp-server-connect-datagen:0.5.3-7.1.0

docker pull confluentinc/cp-ksqldb-server:7.3.2

docker pull confluentinc/cp-enterprise-control-center:7.3.2

docker pull confluentinc/cp-ksqldb-cli:7.3.2

docker pull confluentinc/ksqldb-examples:7.3.2

docker pull confluentinc/cp-kafka-rest:7.3.2
```

### Common Issues

- Port conflicts: Ensure ports 9021, 8080, 9092 are free

- Memory issues: Allocate more RAM to Docker

- Startup delays: Wait 2-3 minutes for services to initialize

## 🔧 Advanced Usage

### 🗂 Create Kafka Topic with Avro Schema

#### Using Confluent Control Center

1. Access Control Center:

   - Open your browser and navigate to:
     http://localhost:9021

2. Create a New Topic:

- Go to the "Topics" section.
- Click "Add a topic".
- Fill in the topic details:
  - Topic Name: your_topic_name
  - Partitions: 1 (or your desired number)
  - Replication Factor: 1 (for local development)

3. Add Avro Schema:

- After creating the topic, navigate to the "Schema" tab.

- Click "Set a schema".

- Paste the following Avro schema:

```json
{
  "type": "record",
  "name": "sampleRecord",
  "namespace": "com.example",
  "fields": [
    { "name": "f1", "type": "string" },
    { "name": "f2", "type": "string" },
    { "name": "f3", "type": "string" }
  ]
}
```

4. Save the Schema:

- Click "Save" to register the schema with the Schema Registry.

### 📨 Produce and Consume **Avro** Messages

1. Access `Schema Registry` Container
   The `schema-registry` container supports the `kafka-avro-console-producer` and `kafka-avro-console-consumer` commands.

Using Docker CLI:

```bash
docker exec -it schema-registry bash
```

2. Start Avro Producer
   Run the following command inside the schema-registry container to start producing Avro-formatted messages:

```bash
kafka-avro-console-producer \
  --bootstrap-server broker:29092 \
  --topic your_topic \
  --property value.schema='{
    "type": "record",
    "name": "sample",
    "fields": [
      {"name": "f1", "type": "string"},
      {"name": "f2", "type": "string"},
      {"name": "f3", "type": "string"}
    ]
  }'
```

Example Messages:

```json
{"f1": "value1", "f2": "value2", "f3": "value3"}
{"f1": "valueA", "f2": "valueB", "f3": "valueC"}
```

Press `Ctrl+C` to stop the producer.

3. Start Avro Consumer
   Run the following command inside the schema-registry container to consume messages from the topic:

```bash
kafka-avro-console-consumer \
  --bootstrap-server broker:29092 \
  --topic your_topic \
  --from-beginning
```

Expected Output:

```json
{"f1": "value1", "f2": "value2", "f3": "value3"}
{"f1": "valueA", "f2": "valueB", "f3": "valueC"}
```

Press `Ctrl+C` to stop the consumer.

## 🛠 SingleStore Pipeline

[Create SingleStore Pipeline](https://github.com/Fahad-Alsubaihi/Kafka-SingleStore-Real-Time-Pipeline/blob/main/SingleStore%20Pipeline.md).

## 🔌 JDBC Connector

[Create JDBC Connector](https://github.com/Fahad-Alsubaihi/Kafka-SingleStore-Real-Time-Pipeline/blob/main/JDBC%20Connector.md).

## 📚 Resources

[SingleStore Documentation](https://docs.singlestore.com/)

[Confluent Platform Docs](https://docs.confluent.io/platform/current/index.html)

[Kafka Connect Guide](https://docs.confluent.io/platform/current/connect/index.html)
