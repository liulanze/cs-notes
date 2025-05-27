# Kafka Notes

**Kafka** is a **distributed, partitioned, replicated commit log service** and a powerful **distributed streaming platform**.

---

## What Kafka Does

Kafka provides three core capabilities:

1. **Publish and Subscribe to Streams of Records**  
   - Acts similarly to a **message queue**.
   - Enables **decoupled communication** between producers and consumers.

2. **Store Streams of Records**  
   - Data is **replicated across 3 nodes** (by default) to ensure fault tolerance.
   - Messages are **persisted to disk** to prevent data loss.
   - Kafka uses a **master-slave (leader-follower)** model for replication.

3. **Process Streams of Records**  
   - Kafka can integrate with data processing systems like:
     - **Apache Spark**
     - **Apache Hadoop**
     - **Flink**, etc.
   - Enables **real-time** and **batch** data processing pipelines.

---

## Summary

Kafka is ideal for:
- **Event-driven architecture**
- **Log aggregation**
- **Stream processing**
- **Real-time analytics**

---

## Core Concepts

### Topics and Partitions

- Kafka stores messages (also called **records**) in **topics**.
- A **topic** groups messages of the same type.
- Each topic can have multiple **partitions**, which are distributed across Kafka servers (brokers), supporting horizontal scalability.

### Offset

- Each message in a partition has a unique **offset**, which is a sequential ID used to locate the message.
- Offsets are:
  - **Monotonically increasing** within a partition.
  - **Independent across partitions** (offset `10` in one partition is unrelated to offset `10` in another).
- Kafka **does not support querying messages directly by offset** — you retrieve messages by scanning from a specific offset onward.

---

## Message Structure and Partition Assignment

- A **message/record** in Kafka consists of a **key** and a **value**, both in byte format.
- Kafka guarantees that:
  - Messages with the **same key** are always routed to the **same partition**.
  - **Each message belongs to exactly one partition** — Kafka never splits a message across partitions.

### Partition Strategy

- If a **key is provided**, Kafka uses **hashing** on the key to determine the partition.
- If **no key is provided**, Kafka uses **round-robin** to distribute messages evenly across partitions.

### broker is like server or node in Kafka

## Kafka Architecture

### ZooKeeper vs. KRaft

- **Legacy Kafka versions** required **ZooKeeper** for:
  - Cluster coordination
  - Controller election
  - Configuration storage
- **Modern Kafka versions** (starting from Kafka 2.8, fully stable in 3.3+) introduced **KRaft (Kafka Raft Metadata mode)**:
  - Eliminates the need for ZooKeeper
  - Uses its own built-in consensus protocol (Raft)
  - Simplifies cluster setup and management

---

## Kafka CLI Example

### Create a Topic

To create a topic named `test` with 3 partitions and a replication factor of 2 (legacy ZooKeeper mode):

```bash
bin/kafka-topics.sh --zookeeper localhost:2181 \
    --create --topic test \
    --partitions 3 \
    --replication-factor 2
```

In KRaft mode, use --bootstrap-server instead of --zookeeper.

### Cluster Deployment

- A Kafka **cluster** consists of multiple **broker nodes** that handle data ingestion, replication, and storage.
- Kafka brokers can be deployed on:
  - **Virtual Machines (VMs)**
  - **Containers** (e.g., Docker)
- In **cloud environments like AWS**, where communication is internal:
  - Brokers can use **hostnames or private IP addresses** to communicate with each other.
  - There’s no need for public IPs or external DNS as long as all brokers are within the same network.

### Listener Configuration

- Once a topic and cluster are set up, Kafka uses two types of listener configuration:
  - **`listener`**: Configures how **producers or internal clients** connect to the Kafka broker.
  - **`advertised.listeners`**: Configures how **external clients (consumers)** discover and connect to the broker, especially important in containerized or cloud environments where internal and external addresses differ.

## Kafka Messaging Models

Kafka supports multiple messaging patterns:

- **Point-to-Point (Queue-like)**:  
  One consumer reads each message.

- **Publish-Subscribe**:  
  Multiple consumers can read the same message from the topic independently.

- **Partitioning and Message Ordering**:  
  - Messages within the same partition maintain **order**.
  - Messages with the **same key** go to the same partition, preserving their relative order.

### Message Delivery Semantics

Kafka supports three types of message delivery guarantees:

1. **At Least Once**  
   - Default behavior.
   - Messages may be **re-delivered** if acknowledgments are lost.

2. **At Most Once**  
   - Messages are **not retried**, even if delivery fails.

3. **Exactly Once**  
   - Requires idempotence and transactional features.
   - Guarantees **no duplication** and **no loss**.

## Partitioning & Consumer Groups

1. **Partition is the smallest unit of parallelism** in Kafka.
2. **One consumer** can consume data from **multiple partitions**.
3. **One partition** can be consumed by **multiple consumers**, but **only if they belong to different consumer groups**.
4. Within the **same consumer group**, **one partition is assigned to only one consumer** to avoid locking and ensure load balancing.
5. In real-world scenarios, a **consumer group often maps to a microservice or a team** within a company.

### Message Ordering Across Partitions

- When a **producer sends messages** to a topic, the data is distributed across multiple **partitions**.
- Within the **same partition**, Kafka guarantees **message ordering by offset**.
- Across **different partitions**, offsets are independent, so the **global ordering is not guaranteed**.
- To preserve message order for related data, you should:
  - **Set a message key**, so that messages with the same key are always sent to the **same partition**.

## Producer API

- Kafka provides official **Java dependencies via Maven** for integrating the Producer API.
- The core method is `producer.send()`, which sends messages **asynchronously**.
  - When a message is sent, it is placed in an internal **buffer (`buffer.memory`)**.
  - An **IO thread** running in the background is responsible for pushing these messages to the broker.
  - This is similar to a **logistics system**: message → delivery locker → final recipient.

### Acknowledgment Settings (`acks`)
- `acks=1`: Producer receives acknowledgment from the **leader** broker only.
- `acks=all`: The leader waits for **all in-sync replicas (followers)** to confirm they have received the message before acknowledging.
- Kafka uses a **leader-follower (replica)** model for each partition to ensure high availability and consistency.

---

## Consumer API

### Offset Management

- Kafka has a built-in topic named `__consumer_offsets` that stores:
  - Which **topic**
  - Which **partition**
  - The **last committed offset**
- This allows consumers to **resume consumption** from where they left off after restarts or failures.

### Core Methods

- `consumer.poll()`: Fetches messages from Kafka.
- `consumer.commitSync()`: Manually commits the current offset, marking messages as processed and ensuring they won’t be reprocessed on restart.

---

## Exactly-Once Semantics (EOS)

### For Producers

- Set the following properties to ensure **idempotent writes**:
  ```properties
  enable.idempotence=true
  retries=Integer.MAX_VALUE
  acks=all
  ```

### For Consumers

Relying solely on offset commits is not enough to prevent duplicate consumption.

A common strategy is to include a unique ID in each message and perform deduplication at the business logic level (e.g., checking if the ID has already been processed).

### Transactions
Kafka supports transactions to ensure atomic writes across multiple partitions and topics.

This can be used to produce messages and commit offsets in one atomic operation — useful for exactly-once processing pipelines.

## Serialization & Deserialization

Kafka messages must be **serialized** before being sent and **deserialized** when consumed.

### Common Message Formats

- **CSV**
- **JSON**
- **Protobuf** (Protocol Buffers)

### Benefits of Serialization

1. **Reduces message size** → improves **network transmission efficiency**.
2. Enables **cross-platform** compatibility.
3. Supports **cross-language** communication between services.

## event is same as message in Kafka, same thing.
