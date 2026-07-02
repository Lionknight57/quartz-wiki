---
title: Kafka & Spark
tags: [kafka, spark, streaming, big-data, event-driven, messaging, pyspark]
related: [Databases-NoSQL, Cloud-AWS-Azure, Python]
sources: [Kafka/Kafka.md, Kafka/Kafka with Python.md, Kafka/Spark.md, Kafka/Spark Data.md, Kafka/Spark+Kafka.md]
updated: 2026-04-12
---

# Kafka & Spark

Two complementary Apache tools for large-scale data:
- **Kafka** — event streaming platform (real-time message transport)
- **Spark** — distributed data processing engine (batch and stream analytics)

They're often used together: Kafka ingests events, Spark processes them.

---

## Apache Kafka

### What is Kafka?

Kafka is a **resilient, high-throughput, scalable event streaming platform**. It acts as a distributed, immutable append-only log of messages — the source of truth for events.

**Why Kafka over alternatives:**

| System | Limitation |
|---|---|
| RabbitMQ | Queue-based (not a log); 1000x slower than Kafka |
| Amazon SQS | Queue-based; expensive at scale |
| Amazon SNS / Google Pub/Sub | Cloud-locked; expensive |
| Redis | No message history; not built for streaming |
| Mulesoft | Requires exposed APIs per channel; complex |

Kafka's log model provides **message history** — consumers can replay past events, which none of the above support.

---

### Kafka architecture

```
Producer  →  Broker (Kafka Cluster)  →  Consumer
               ↑          ↑
          Topic/Partition  REST Proxy
               ↑
         Kafka Connect (external sources)
         Kafka Streams  (stream processing)
         Schema Registry (message contracts)
         ksqlDB          (SQL over streams)
```

**Components:**

| Component | Role |
|---|---|
| **Broker** | A Kafka server. Run an odd number (3, 5) for fault tolerance |
| **Topic** | Named channel where messages are published |
| **Partition** | A topic is split into partitions for parallelism |
| **Offset** | Position of a message within a partition (immutable) |
| **Producer** | Publishes messages to a topic |
| **Consumer** | Subscribes to a topic and reads messages |
| **Consumer Group** | Multiple consumers sharing partitions for parallel processing |
| **Zookeeper / KRaft** | Cluster coordination and leader election (KRaft replaces ZooKeeper in modern Kafka) |
| **Kafka Connect** | Plug-and-play connectors to/from external systems (MongoDB, S3, Elasticsearch, JDBC) |
| **Kafka Streams** | Stream processing library for microservices |
| **REST Proxy** | HTTP interface to interact with Kafka (health checks, produce/consume via curl) |

---

### Message structure

```
Header(s):  Key/Value pairs (metadata)
Content:
  Key        — routing key (determines partition)
  Value      — the payload (any bytes; often JSON or Avro)
  Timestamp  — epoch millis
```

---

### Deployment commands (Docker)

```bash
docker compose up -d          # start cluster
docker compose ps             # check status
docker logs broker-1          # view broker logs
docker compose down           # stop cluster

# Via REST Proxy
curl localhost:8082/brokers   # list brokers
```

**Broker ports:**
- `29092` — internal (broker-to-broker)
- `9092` — external (clients)

---

### Delivery guarantees

| Guarantee | Description |
|---|---|
| **At most once** | Messages may be lost; never duplicated |
| **At least once** | No message loss; duplicates possible |
| **Exactly once** | No loss, no duplicates — holy grail; supported by Kafka Streams |

---

### Packaging options

| Option | Notes |
|---|---|
| **Apache Kafka** | Open-source, self-managed |
| **Confluent Community / Cloud** | Adds Schema Registry, REST Proxy, ksqlDB, connectors |
| **Cloudera** | Enterprise Hadoop/Kafka distribution |
| **Strimzi** | Helm charts to deploy Kafka on Kubernetes |
| **AWS MSK** | Managed Kafka on AWS |
| **Azure HDInsight** | Managed Kafka on Azure |

---

### Producer / Consumer (Python — confluent-kafka)

```python
from confluent_kafka import Producer, Consumer

# Producer
producer = Producer({'bootstrap.servers': 'localhost:9092'})

def delivery_report(err, msg):
    if err:
        print(f'Delivery failed: {err}')
    else:
        print(f'Delivered to {msg.topic()} [{msg.partition()}] at offset {msg.offset()}')

producer.produce('orders', key='order-1', value='{"item":"Widget","qty":3}',
                 callback=delivery_report)
producer.flush()   # block until all messages are sent

# Consumer
consumer = Consumer({
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'order-processor',
    'auto.offset.reset': 'earliest'
})
consumer.subscribe(['orders'])

try:
    while True:
        msg = consumer.poll(timeout=1.0)
        if msg is None:
            continue
        if msg.error():
            print(f'Error: {msg.error()}')
        else:
            print(f'Received: {msg.value().decode()} from partition {msg.partition()}')
finally:
    consumer.close()
```

**Consumer groups:** Multiple consumers in the same `group.id` split partitions between them — achieving parallel processing. Each partition is consumed by exactly one member of the group.

---

### Kafka Connect

Kafka Connect provides ready-made connectors to integrate Kafka with external systems — no custom producer/consumer code needed.

**Source connectors** (external → Kafka): databases (via JDBC / Debezium CDC), files, S3, Elasticsearch  
**Sink connectors** (Kafka → external): MongoDB, MariaDB, Sumo Logic, Elasticsearch, S3

```bash
# REST API to manage connectors
GET  /connectors
POST /connectors   { "name": "...", "config": { ... } }
```

**Debezium** — CDC (Change Data Capture) connector that captures database change events (INSERT/UPDATE/DELETE) and streams them to Kafka topics.

---

### ksqlDB

SQL-like query language for Kafka streams:

```sql
-- Create a stream from a topic
CREATE STREAM orders (orderId STRING, amount DOUBLE)
  WITH (KAFKA_TOPIC='orders', VALUE_FORMAT='JSON');

-- Query it
SELECT orderId, amount FROM orders WHERE amount > 100 EMIT CHANGES;

-- Aggregate into a table
CREATE TABLE order_totals AS
  SELECT customerId, SUM(amount) AS total
  FROM orders GROUP BY customerId EMIT CHANGES;
```

---

## Apache Spark

### What is Spark?

Apache Spark is a **distributed in-memory data processing engine**. It's up to 100× faster than Hadoop (disk-based MapReduce) by keeping intermediate results in memory.

**Unified platform for:**
- Batch processing
- Stream processing (Structured Streaming)
- Machine learning (MLlib)
- Graph computation (GraphX)
- SQL queries (Spark SQL)

**Language support:** Python (PySpark), Scala, Java, R.

---

### Spark architecture

```
Driver (SparkSession)
   ↓
Cluster Manager  (Standalone / YARN / Kubernetes / Mesos)
   ↓  ↓  ↓
Executor  Executor  Executor
(Tasks)   (Tasks)   (Tasks)
```

| Component | Role |
|---|---|
| **Driver** | Hosts the SparkSession; orchestrates the job; determines partitions |
| **Cluster Manager** | Allocates resources (workers) |
| **Executor** | Runs tasks; one task per partition per core |
| **Job** | Triggered when an action is called (`.collect()`, `.count()`, `.show()`) |
| **Stage** | Group of tasks that can run in parallel (split at shuffle boundaries) |
| **Task** | Unit of work on one partition |
| **Partition** | A chunk of data; controls parallelism |

**Parallelism rule:** Number of parallel tasks = number of cores × number of executors.

---

### Core APIs

| API | Description |
|---|---|
| **RDD** (Resilient Distributed Dataset) | Low-level; immutable distributed collection |
| **DataFrame** | High-level; tabular data with schema; optimised by Catalyst |
| **Dataset** | Type-safe DataFrame (Scala/Java only) |
| **Spark SQL** | SQL interface over DataFrames |

Prefer **DataFrames** / **Spark SQL** over RDDs — the Catalyst optimizer generates efficient execution plans automatically.

---

### PySpark basics

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("MyApp") \
    .getOrCreate()

# Read data
df = spark.read.csv("data.csv", header=True, inferSchema=True)
df = spark.read.json("data.json")
df = spark.read.parquet("data.parquet")

# Transformations (lazy — not executed until action called)
df_filtered = df.filter(df.age > 18)
df_selected = df.select("name", "age")
df_sorted   = df.orderBy("age", ascending=False)
df_grouped  = df.groupBy("department").count()

# Actions (trigger execution)
df.show(5)
df.count()
result = df.collect()    # returns list — use sparingly on large data

# SQL interface
df.createOrReplaceTempView("people")
spark.sql("SELECT name, age FROM people WHERE age > 30").show()

# Write
df.write.parquet("output/")
df.write.mode("overwrite").csv("output/")
```

---

### Structured Streaming (real-time)

```python
# Read from Kafka
stream = spark.readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "orders") \
    .load()

# Process
parsed = stream.selectExpr("CAST(value AS STRING) as json")

# Write output
query = parsed.writeStream \
    .format("console") \
    .outputMode("append") \
    .start()

query.awaitTermination()
```

---

### Delta Lake

Built on Parquet + transaction log, Delta Lake adds ACID transactions and time travel to Spark:

```python
# Write as Delta
df.write.format("delta").save("/data/events")

# Read
df = spark.read.format("delta").load("/data/events")

# Time travel
df_old = spark.read.format("delta").option("versionAsOf", 3).load("/data/events")

# UPSERT (MERGE)
from delta.tables import DeltaTable
deltaTable = DeltaTable.forPath(spark, "/data/events")
deltaTable.alias("target").merge(
    updates.alias("source"),
    "target.id = source.id"
).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
```

---

### Kafka + Spark integration

Kafka streams events → Spark processes them in micro-batches:

```
Kafka Topic  →  Spark Structured Streaming  →  Delta Lake / DB / Dashboard
```

```python
# Full pipeline: Kafka → aggregate → write
orders = spark.readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "orders") \
    .load()

from pyspark.sql.functions import from_json, col, sum as _sum
from pyspark.sql.types import StructType, DoubleType, StringType

schema = StructType() \
    .add("customerId", StringType()) \
    .add("amount", DoubleType())

parsed = orders.select(from_json(col("value").cast("string"), schema).alias("data")) \
    .select("data.*")

totals = parsed.groupBy("customerId").agg(_sum("amount").alias("total"))

totals.writeStream \
    .format("delta") \
    .outputMode("complete") \
    .option("checkpointLocation", "/checkpoints/totals") \
    .start("/data/order_totals")
```

---

## See also
- [[Databases-NoSQL]] — Kafka Connect integrates with MongoDB, Elasticsearch, Redis
- [[Cloud-AWS-Azure]] — AWS MSK (managed Kafka), Azure HDInsight
- [[Python]] — PySpark is Python-first
