---

```markdown
# Kafka KRaft Full Stack (Kafka + Kafka REST Proxy + Control Center + KSQLDB)

This project provides a fully functional Apache Kafka stack using **KRaft (KRaft mode - no Zookeeper)** via Docker Compose.  
It includes:

- Kafka (in KRaft mode)
- Kafka REST Proxy
- Confluent Control Center
- KSQLDB Server & CLI

---

## 📦 Architecture

```
+------------------------+
|   Kafka (KRaft mode)   |
|  Ports: 9092, 29092    |
|  Cluster ID: stored via kafka-storage format
+------------------------+
           |
           | PLAINTEXT
           v
+------------------------+       +------------------------+
|  Kafka REST Proxy      | <---> |  Control Center        |
|  Port: 8082            |       |  Port: 9021             |
+------------------------+       +------------------------+
                                      |
                                      v
                          +--------------------------+
                          |     KSQLDB Server        |
                          |     Port: 8088           |
                          +--------------------------+
```

---

## 🐳 Docker Compose Services

| Service           | Image                                  | Ports    | Description                            |
|------------------|----------------------------------------|----------|----------------------------------------|
| kafka-kraft       | confluentinc/cp-kafka:7.9.0            | 9092, 29092, 9101 | Kafka in KRaft mode                     |
| kafka-rest        | confluentinc/cp-kafka-rest:7.9.0       | 8082     | REST API access to Kafka topics        |
| control-center    | confluentinc/cp-enterprise-control-center:7.9.0 | 9021 | Kafka monitoring dashboard             |
| ksqldb-server     | confluentinc/cp-ksqldb-server:7.9.0    | 8088     | Stream processing with SQL-like queries |
| ksqldb-cli        | confluentinc/cp-ksqldb-cli:7.9.0       | -        | CLI interface for KSQLDB               |

---

## 🚀 How to Run

> Ensure you've already formatted the Kafka storage with a Cluster ID before starting the stack:

### 1. Format Kafka storage (one-time setup):
```bash
docker run --rm \
  -v "$PWD/kafka.properties:/tmp/kafka.properties" \
  -v "$PWD/kafka-data:/var/lib/kafka/data" \
  confluentinc/cp-kafka:7.9.0 \
  bash -c "kafka-storage format --config /tmp/kafka.properties --cluster-id <CLUSTER_ID> --ignore-formatted"
```

> Use a valid base64 UUID for `<CLUSTER_ID>` (e.g., `MTO0G7CmS4iKcyzLjCOT1w`)

### 2. Start the stack
```bash
docker-compose up -d
```

---

## 🔌 Port Mapping

| Component         | Port  | Access URL                      |
|------------------|-------|----------------------------------|
| Kafka (External)  | 9092  | `localhost:9092`                |
| Kafka (Internal)  | 29092 | Used internally by other containers |
| JMX Monitoring    | 9101  | JMX metrics                     |
| Kafka REST Proxy  | 8082  | `http://localhost:8082`         |
| Control Center    | 9021  | `http://localhost:9021`         |
| KSQLDB Server     | 8088  | `http://localhost:8088`         |

---

## ✅ Testing Kafka REST Proxy

### Create a topic:
```bash
curl -X POST http://localhost:8082/topics/test-topic
```

### Produce a message:
```bash
curl -X POST -H "Content-Type: application/vnd.kafka.json.v2+json" \
     -d '{"records":[{"value":{"foo":"bar"}}]}' \
     http://localhost:8082/topics/test-topic
```

### Consume messages:
```bash
curl -X GET "http://localhost:8082/consumers/my-group/instances/my-instance/records"
```

---

## 🛠 Control Center Dashboard
Access at: [http://localhost:9021](http://localhost:9021)

You can explore:
- Kafka topics
- Message throughput
- Connect cluster (if enabled)
- KSQL queries

---

## 🔎 KSQLDB Usage

Open a KSQL CLI shell:
```bash
docker exec -it ksqldb-cli ksql http://ksqldb-server:8088
```

Example query:
```sql
SHOW STREAMS;
```

---

## 📌 Notes

- **Cluster ID should not be redefined in docker-compose after formatting.**
- **Replication factor error in logs is harmless if you are running a single-broker setup.**

---

## 📁 Directory Structure

```
├── kafka.properties
├── kafka-data/               # Persistent Kafka storage
├── docker-compose.yaml
└── README.md
```
