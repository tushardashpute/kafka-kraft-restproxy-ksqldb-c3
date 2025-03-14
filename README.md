```markdown
# 🔄 Confluent Kafka REST Proxy + ksqlDB + Control Center Setup (KRaft Mode)

This project provides an end-to-end setup of the Confluent Platform using **KRaft mode** (Kafka without Zookeeper) via Docker Compose. It includes essential tools like Kafka REST Proxy, Schema Registry, ksqlDB, and Control Center for an interactive data streaming experience.

---

## 📘 Overview of Components

| Component                       | Description |
|---------------------            |-------------|
| **Kafka (KRaft)**               | Kafka in KRaft mode replaces Zookeeper with an internal consensus protocol. It's a distributed event streaming platform for building real-time pipelines and applications. |
| **Kafka REST Proxy**            | Enables producing and consuming Kafka messages over HTTP/REST APIs instead of using native Kafka clients. Useful for quick integrations and testing. |
| **Schema Registry**             | Stores and manages Avro/JSON/Protobuf schemas for Kafka topics, ensuring compatibility and structure across producers and consumers. |
| **Confluent Control Center**    | A GUI-based monitoring and management tool to visualize Kafka clusters, topics, consumers, ksqlDB queries, connectors, and more. |
| **ksqlDB**                      | A streaming SQL engine for real-time analytics on Kafka topics. You can run SQL queries on streaming data using intuitive syntax. |
| **ksqlDB CLI**                  | A terminal-based interface to interact with ksqlDB server, define streams/tables, and run queries. |

---

## 📦 Docker Compose Setup

### 🔧 Prerequisites

- Docker and Docker Compose installed

### 🚀 Run the Stack

```bash
docker-compose up -d
```

---

## 🌐 Access Services

| Service                 | URL                             |
|------------------------|----------------------------------|
| Kafka REST Proxy        | [http://localhost:8082](http://localhost:8082) |
| Schema Registry         | [http://localhost:8081](http://localhost:8081) |
| Control Center          | [http://localhost:9021](http://localhost:9021) |
| ksqlDB Server (API)     | [http://localhost:8088](http://localhost:8088) |

---

## 📤 Produce Sample JSON Messages (via REST Proxy)

```bash
curl -X POST http://localhost:8082/topics/test-topic \
  -H "Content-Type: application/vnd.kafka.json.v2+json" \
  -d '{
        "records": [
          { "value": { "id": 1, "name": "Alice", "email": "alice@example.com" }},
          { "value": { "id": 2, "name": "Bob", "email": "bob@example.com" }},
          { "value": { "id": 3, "name": "Charlie", "email": "charlie@example.com" }}
        ]
      }'
```

✅ This produces structured JSON messages to the Kafka topic `test-topic`.

---

## 📥 Consume Messages Using ksqlDB

### 1️⃣ Connect to ksqlDB CLI

```bash
docker exec -it ksqldb-cli ksql http://ksqldb-server:8088
```

### 2️⃣ Create a Stream from the Kafka Topic

```sql
CREATE STREAM users_raw (
  id INT,
  name STRING,
  email STRING
) WITH (
  KAFKA_TOPIC='test-topic',
  VALUE_FORMAT='JSON'
);
```

### 3️⃣ Query the Stream

```sql
SELECT * FROM users_raw EMIT CHANGES;
```

### 🔍 Output (Example):

```
+----+---------+---------------------+
| ID | NAME    | EMAIL               |
+----+---------+---------------------+
| 1  | Alice   | alice@example.com   |
| 2  | Bob     | bob@example.com     |
| 3  | Charlie | charlie@example.com |
```

---

## 📊 Monitor Data in Control Center

Open [http://localhost:9021](http://localhost:9021):

- View **Kafka Cluster Health**
- Inspect **Topics**, **Consumers**, and **Streams**
- Track **ksqlDB queries and results**

---

## 📂 Project Structure

```
.
├── docker-compose.yaml
└── README.md
```

---

## ✅ Summary

This setup provides:

- Lightweight local Kafka setup (no Zookeeper required)
- REST-based Kafka interactions
- Schema-aware data streaming
- Real-time SQL analytics with ksqlDB
- Visual cluster monitoring with Control Center

Whether you're building event-driven applications or just learning Kafka and ksqlDB — this project provides everything you need in one place.
