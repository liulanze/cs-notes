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

ToDo: continue the note...
