# Messaging Queues & Async — Consolidated HLD Notes

## Table of Contents

- [1. Messaging & Async Fundamentals](#1-messaging--async-fundamentals)
  - [1.1 Introduction to Messaging Systems](#11-introduction-to-messaging-systems)
    - [1.1.1 Why Messaging Matters](#111-why-messaging-matters)
    - [1.1.2 Decoupling Services](#112-decoupling-services)
    - [1.1.3 Asynchronous Processing](#113-asynchronous-processing)
    - [1.1.4 Reliability & Durability](#114-reliability--durability)
    - [1.1.5 Scalability Benefits](#115-scalability-benefits)
  - [1.2 Core Messaging Concepts](#12-core-messaging-concepts)
    - [1.2.1 Producer](#121-producer)
    - [1.2.2 Consumer](#122-consumer)
    - [1.2.3 Broker](#123-broker)
    - [1.2.4 Topic](#124-topic)
    - [1.2.5 Partition](#125-partition)
    - [1.2.6 Offset](#126-offset)
    - [1.2.7 Consumer Group](#127-consumer-group)
  - [1.3 Queue vs Stream](#13-queue-vs-stream)
    - [1.3.1 Traditional Queue](#131-traditional-queue)
    - [1.3.2 Event Stream](#132-event-stream)
    - [1.3.3 Data Retention Differences](#133-data-retention-differences)
    - [1.3.4 Consumer Behavior](#134-consumer-behavior)
    - [1.3.5 Real-World Usage Patterns](#135-real-world-usage-patterns)
  - [1.4 Message Queue vs Publish-Subscribe](#14-message-queue-vs-publish-subscribe)
    - [1.4.1 Point-to-Point Queue](#141-point-to-point-queue)
    - [1.4.2 Publish-Subscribe Model](#142-publish-subscribe-model)
    - [1.4.3 Fan-Out Messaging](#143-fan-out-messaging)
    - [1.4.4 Comparison & Tradeoffs](#144-comparison--tradeoffs)
- [2. Messaging Systems](#2-messaging-systems)
  - [2.1 RabbitMQ](#21-rabbitmq)
    - [2.1.1 Architecture](#211-architecture)
    - [2.1.2 Exchanges & Queues](#212-exchanges--queues)
    - [2.1.3 Routing Mechanisms](#213-routing-mechanisms)
    - [2.1.4 Acknowledgements](#214-acknowledgements)
    - [2.1.5 Typical Use Cases](#215-typical-use-cases)
    - [2.1.6 Strengths & Limitations](#216-strengths--limitations)
  - [2.2 Apache Kafka](#22-apache-kafka)
    - [2.2.1 Kafka Architecture](#221-kafka-architecture)
    - [2.2.2 Topics & Partitions](#222-topics--partitions)
    - [2.2.3 Brokers](#223-brokers)
    - [2.2.4 Consumer Groups](#224-consumer-groups)
    - [2.2.5 Replication](#225-replication)
    - [2.2.6 Message Retention](#226-message-retention)
    - [2.2.7 Ordering Guarantees](#227-ordering-guarantees)
    - [2.2.8 Typical Use Cases](#228-typical-use-cases)
    - [2.2.9 Strengths & Limitations](#229-strengths--limitations)
  - [2.3 Kafka vs RabbitMQ](#23-kafka-vs-rabbitmq)
    - [2.3.1 Throughput Comparison](#231-throughput-comparison)
    - [2.3.2 Latency Comparison](#232-latency-comparison)
    - [2.3.3 Ordering Guarantees](#233-ordering-guarantees)
    - [2.3.4 Persistence Models](#234-persistence-models)
    - [2.3.5 Replay Capability](#235-replay-capability)
    - [2.3.6 Operational Complexity](#236-operational-complexity)
    - [2.3.7 When to Choose Which](#237-when-to-choose-which)
  - [2.4 Other Messaging Systems](#24-other-messaging-systems)
    - [2.4.1 Amazon SQS](#241-amazon-sqs)
    - [2.4.2 Google Pub/Sub](#242-google-pubsub)
    - [2.4.3 Apache Pulsar](#243-apache-pulsar)
    - [2.4.4 NATS](#244-nats)
    - [2.4.5 Redis Streams](#245-redis-streams)
- [3. Delivery Guarantees & Reliability](#3-delivery-guarantees--reliability)
  - [3.1 Delivery Semantics](#31-delivery-semantics)
    - [3.1.1 At-Most-Once Delivery](#311-at-most-once-delivery)
    - [3.1.2 At-Least-Once Delivery](#312-at-least-once-delivery)
    - [3.1.3 Exactly-Once Delivery](#313-exactly-once-delivery)
    - [3.1.4 Tradeoffs Between Guarantees](#314-tradeoffs-between-guarantees)
  - [3.2 Exactly-Once Processing](#32-exactly-once-processing)
    - [3.2.1 Producer Idempotency](#321-producer-idempotency)
    - [3.2.2 Transactions](#322-transactions)
    - [3.2.3 Offset Management](#323-offset-management)
    - [3.2.4 Kafka Exactly-Once Semantics](#324-kafka-exactly-once-semantics)
  - [3.3 Reliability Mechanisms](#33-reliability-mechanisms)
    - [3.3.1 Acknowledgements (ACKs)](#331-acknowledgements-acks)
    - [3.3.2 Retries](#332-retries)
    - [3.3.3 Dead Letter Queues (DLQ)](#333-dead-letter-queues-dlq)
    - [3.3.4 Retry Backoff](#334-retry-backoff)
    - [3.3.5 Poison Message Handling](#335-poison-message-handling)
  - [3.4 Replication & Durability](#34-replication--durability)
    - [3.4.1 Leader-Follower Replication](#341-leader-follower-replication)
    - [3.4.2 ISR (In-Sync Replicas)](#342-isr-in-sync-replicas)
    - [3.4.3 Replication Factor](#343-replication-factor)
    - [3.4.4 Durability vs Latency Tradeoff](#344-durability-vs-latency-tradeoff)
- [4. Idempotency](#4-idempotency)
  - [4.1 Why Idempotency Matters](#41-why-idempotency-matters)
    - [4.1.1 Duplicate Message Problems](#411-duplicate-message-problems)
    - [4.1.2 Retry Safety](#412-retry-safety)
    - [4.1.3 Financial Transaction Safety](#413-financial-transaction-safety)
  - [4.2 Idempotency Techniques](#42-idempotency-techniques)
    - [4.2.1 Natural Idempotency](#421-natural-idempotency)
    - [4.2.2 Message IDs](#422-message-ids)
    - [4.2.3 Deduplication Tables](#423-deduplication-tables)
    - [4.2.4 Database Constraints](#424-database-constraints)
    - [4.2.5 Optimistic Locking](#425-optimistic-locking)
    - [4.2.6 Version Numbers](#426-version-numbers)
    - [4.2.7 Kafka Idempotent Producer](#427-kafka-idempotent-producer)
  - [4.3 Real-World Idempotency](#43-real-world-idempotency)
    - [4.3.1 Payment Processing](#431-payment-processing)
    - [4.3.2 Order Creation](#432-order-creation)
    - [4.3.3 Email Notifications](#433-email-notifications)
- [5. Asynchronous Processing Patterns](#5-asynchronous-processing-patterns)
  - [5.1 Event-Driven Architecture](#51-event-driven-architecture)
    - [5.1.1 Event Producers](#511-event-producers)
    - [5.1.2 Event Consumers](#512-event-consumers)
    - [5.1.3 Event Bus](#513-event-bus)
    - [5.1.4 Loose Coupling](#514-loose-coupling)
  - [5.2 Publish-Subscribe Pattern](#52-publish-subscribe-pattern)
    - [5.2.1 Fan-Out Messaging](#521-fan-out-messaging)
    - [5.2.2 Multiple Consumers](#522-multiple-consumers)
    - [5.2.3 Event Broadcasting](#523-event-broadcasting)
  - [5.3 Work Queue Pattern](#53-work-queue-pattern)
    - [5.3.1 Task Distribution](#531-task-distribution)
    - [5.3.2 Competing Consumers](#532-competing-consumers)
    - [5.3.3 Background Job Processing](#533-background-job-processing)
  - [5.4 Saga Pattern](#54-saga-pattern)
    - [5.4.1 Distributed Transactions](#541-distributed-transactions)
    - [5.4.2 Choreography](#542-choreography)
    - [5.4.3 Orchestration](#543-orchestration)
    - [5.4.4 Compensation Actions](#544-compensation-actions)
  - [5.5 CQRS + Event Sourcing](#55-cqrs--event-sourcing)
    - [5.5.1 Command Side](#551-command-side)
    - [5.5.2 Query Side](#552-query-side)
    - [5.5.3 Event Store](#553-event-store)
    - [5.5.4 Replayability](#554-replayability)
- [6. Scaling Messaging Systems](#6-scaling-messaging-systems)
  - [6.1 Partitioning](#61-partitioning)
    - [6.1.1 Why Partitioning Matters](#611-why-partitioning-matters)
    - [6.1.2 Partition Keys](#612-partition-keys)
    - [6.1.3 Ordering Within Partitions](#613-ordering-within-partitions)
    - [6.1.4 Hot Partition Problems](#614-hot-partition-problems)
  - [6.2 Consumer Scaling](#62-consumer-scaling)
    - [6.2.1 Consumer Groups](#621-consumer-groups)
    - [6.2.2 Parallel Consumption](#622-parallel-consumption)
    - [6.2.3 Rebalancing](#623-rebalancing)
    - [6.2.4 Lag Monitoring](#624-lag-monitoring)
  - [6.3 Backpressure Handling](#63-backpressure-handling)
    - [6.3.1 Slow Consumers](#631-slow-consumers)
    - [6.3.2 Queue Build-Up](#632-queue-build-up)
    - [6.3.3 Rate Limiting](#633-rate-limiting)
    - [6.3.4 Flow Control](#634-flow-control)
    - [6.3.5 Load Shedding](#635-load-shedding)
  - [6.4 Message Ordering](#64-message-ordering)
    - [6.4.1 Global Ordering Challenges](#641-global-ordering-challenges)
    - [6.4.2 Per-Partition Ordering](#642-per-partition-ordering)
    - [6.4.3 Ordering vs Scalability Tradeoff](#643-ordering-vs-scalability-tradeoff)
- [7. Common Messaging Problems](#7-common-messaging-problems)
  - [7.1 Duplicate Messages](#71-duplicate-messages)
    - [7.1.1 Causes](#711-causes)
    - [7.1.2 Deduplication Strategies](#712-deduplication-strategies)
  - [7.2 Message Loss](#72-message-loss)
    - [7.2.1 Broker Failures](#721-broker-failures)
    - [7.2.2 Replication Failures](#722-replication-failures)
    - [7.2.3 ACK Failures](#723-ack-failures)
  - [7.3 Consumer Lag](#73-consumer-lag)
    - [7.3.1 Causes](#731-causes)
    - [7.3.2 Monitoring](#732-monitoring)
    - [7.3.3 Scaling Consumers](#733-scaling-consumers)
  - [7.4 Poison Messages](#74-poison-messages)
    - [7.4.1 Retry Loops](#741-retry-loops)
    - [7.4.2 DLQ Strategies](#742-dlq-strategies)
    - [7.4.3 Failure Isolation](#743-failure-isolation)
  - [7.5 Ordering Issues](#75-ordering-issues)
    - [7.5.1 Out-of-Order Processing](#751-out-of-order-processing)
    - [7.5.2 Rebalancing Side Effects](#752-rebalancing-side-effects)
- [8. Messaging Patterns by System Type](#8-messaging-patterns-by-system-type)
  - [8.1 Summary: Messaging & Queues Techniques by Pattern](#81-summary-messaging--queues-techniques-by-pattern)
  - [8.2 Pattern A – CRUD & Storage Systems (Messaging & Queues Focus)](#82-pattern-a---crud--storage-systems-messaging--queues-focus)
  - [8.3 Pattern B – Feed / Timeline Systems (Messaging & Queues Focus)](#83-pattern-b---feed--timeline-systems-messaging--queues-focus)
  - [8.4 Pattern C – Real-Time Systems (Messaging & Queues Focus)](#84-pattern-c---real-time-systems-messaging--queues-focus)
  - [8.5 Pattern D – Search Systems (Messaging & Queues Focus)](#85-pattern-d---search-systems-messaging--queues-focus)
  - [8.6 Pattern E – Geospatial & Location-Based Systems (Messaging & Queues Focus)](#86-pattern-e---geospatial--location-based-systems-messaging--queues-focus)
  - [8.7 Pattern F – Financial & Critical Consistency Systems (Messaging & Queues Focus)](#87-pattern-f---financial--critical-consistency-systems-messaging--queues-focus)
  - [8.8 Pattern G – Large-Scale Processing & Infrastructure (Messaging & Queues Focus)](#88-pattern-g---large-scale-processing--infrastructure-messaging--queues-focus)
  - [8.9 Pattern H – Collaborative & Infrastructure Utilities (Messaging & Queues Focus)](#89-pattern-h---collaborative--infrastructure-utilities-messaging--queues-focus)
- [9. Real-World Messaging Architectures](#9-real-world-messaging-architectures)
  - [9.1 Uber-Like Systems](#91-uber-like-systems)
    - [9.1.1 Driver Location Streams](#911-driver-location-streams)
    - [9.1.2 Ride Event Pipelines](#912-ride-event-pipelines)
    - [9.1.3 Dispatch Messaging](#913-dispatch-messaging)
  - [9.2 E-Commerce Systems](#92-e-commerce-systems)
    - [9.2.1 Order Processing Pipelines](#921-order-processing-pipelines)
    - [9.2.2 Inventory Updates](#922-inventory-updates)
    - [9.2.3 Payment Workflows](#923-payment-workflows)
  - [9.3 Social Media Systems](#93-social-media-systems)
    - [9.3.1 Notification Pipelines](#931-notification-pipelines)
    - [9.3.2 Feed Generation](#932-feed-generation)
    - [9.3.3 Analytics Events](#933-analytics-events)
- [10. Interview-Oriented HLD Notes](#10-interview-oriented-hld-notes)
  - [10.1 Key Tradeoffs](#101-key-tradeoffs)
    - [10.1.1 Throughput vs Ordering](#1011-throughput-vs-ordering)
    - [10.1.2 Durability vs Latency](#1012-durability-vs-latency)
    - [10.1.3 Consistency vs Availability](#1013-consistency-vs-availability)
    - [10.1.4 Simplicity vs Scalability](#1014-simplicity-vs-scalability)
  - [10.2 Common Interview Questions](#102-common-interview-questions)
    - [10.2.1 Kafka vs RabbitMQ?](#1021-kafka-vs-rabbitmq)
    - [10.2.2 How to Achieve Exactly-Once?](#1022-how-to-achieve-exactly-once)
    - [10.2.3 How to Handle Duplicate Messages?](#1023-how-to-handle-duplicate-messages)
    - [10.2.4 How to Scale Consumers?](#1024-how-to-scale-consumers)
    - [10.2.5 How to Maintain Ordering?](#1025-how-to-maintain-ordering)
  - [10.3 Quick Revision Material](#103-quick-revision-material)
    - [10.3.1 Queue vs Stream Cheat Sheet](#1031-queue-vs-stream-cheat-sheet)
    - [10.3.2 Delivery Guarantees Summary](#1032-delivery-guarantees-summary)
    - [10.3.3 Kafka Terminology Cheat Sheet](#1033-kafka-terminology-cheat-sheet)
    - [10.3.4 Key Interview Sentences](#1034-key-interview-sentences)
    - [10.3.5 Self-Test Questions](#1035-self-test-questions)

---

## 1. Messaging & Async Fundamentals

### 1.1 Introduction to Messaging Systems

A messaging system allows applications to send and receive messages asynchronously. Instead of communicating directly (point‑to‑point), services exchange messages via a middleware (message broker). This decouples producers (senders) from consumers (receivers), improving fault tolerance, scalability, and flexibility.

#### 1.1.1 Why Messaging Matters
Messaging is the backbone of every scalable distributed system. It enables decoupling, asynchronous processing, and reliable communication. Without it, services would be tightly coupled, slow, and brittle – a failure in one service would cascade.

#### 1.1.2 Decoupling Services
Producers and consumers don’t need to know about each other. Producers publish messages to a broker; consumers subscribe to topics or queues. Adding a new consumer requires no changes to the producer.

**Without queues (tight coupling):**
```
User posts tweet → App directly calls Notification, Search, Analytics, Fan‑out → all must succeed synchronously.
```
**With queues (decoupled):**
```
User posts tweet → App writes to DB, puts message in queue → immediately returns success.
Queue delivers to Notification, Search, Analytics, Fan‑out independently.
```

#### 1.1.3 Asynchronous Processing
Producers can continue working without waiting for the consumer to finish. Long‑running tasks (e.g., video encoding, sending emails) are handled in the background. The user gets a fast response; processing happens later.

#### 1.1.4 Reliability & Durability
Messages can be persisted on disk and replicated across nodes. Even if a broker fails, messages are not lost. Retry mechanisms and dead‑letter queues handle processing failures.

#### 1.1.5 Scalability Benefits
- **Load leveling:** The queue absorbs traffic spikes, consumers process at a steady rate.
- **Parallelism:** Multiple consumers can read from the same topic/queue in parallel.
- **Elasticity:** Consumer groups auto‑scale based on queue depth (e.g., AWS Lambda + SQS).

### 1.2 Core Messaging Concepts

#### 1.2.1 Producer
An application that sends (publishes) messages to a broker. Producers don’t know who will consume the messages.

#### 1.2.2 Consumer
An application that receives and processes messages from a broker. Consumers can be part of a consumer group.

#### 1.2.3 Broker
The messaging server that stores and routes messages. Examples: Kafka broker, RabbitMQ node.

#### 1.2.4 Topic
A named category of messages. Producers write to a topic, consumers subscribe to a topic. Similar to a table in a database.

#### 1.2.5 Partition
A topic is split into multiple partitions for scalability. Each partition is an ordered, append‑only log of messages. More partitions = more parallelism.

#### 1.2.6 Offset
The position of a message within a partition. Consumers track their offset to know where to resume after a restart. Offsets allow replay.

#### 1.2.7 Consumer Group
A set of consumers that cooperate to consume a topic. Each partition is assigned to exactly one consumer in the group. If a consumer fails, partitions are reassigned (rebalancing).

### 1.3 Queue vs Stream

#### 1.3.1 Traditional Queue
A queue is point‑to‑point: each message is delivered to exactly one consumer. Once consumed, the message is deleted. Ideal for task distribution (e.g., background jobs). Examples: RabbitMQ, Amazon SQS.

```
Producer → [M1][M2][M3] → Consumer (one message per consumer)
```

#### 1.3.2 Event Stream
A stream is a persistent, ordered log of events. Multiple consumers can read the same messages independently, each at their own offset. Messages are retained for a configurable time (days, weeks). Examples: Kafka, Kinesis.

```
Producer → [M1][M2][M3][M4]... → Consumer A (offset=2), Consumer B (offset=4)
```

#### 1.3.3 Data Retention Differences
- **Queue:** Messages are deleted after acknowledgment.
- **Stream:** Messages remain on disk after consumption (retention period). Enables replay and multiple independent consumers.

#### 1.3.4 Consumer Behavior
- **Queue:** Competing consumers share the work; each message goes to one consumer.
- **Stream:** Each consumer (or consumer group) reads the entire stream independently.

#### 1.3.5 Real-World Usage Patterns
- **Queue:** Task processing, email sending, image resizing – where each job is done once.
- **Stream:** Event sourcing, analytics pipelines, log aggregation – where multiple services need the same events.

### 1.4 Message Queue vs Publish-Subscribe

#### 1.4.1 Point-to-Point Queue
Producer sends a message to a queue. Exactly one consumer (from a pool of competing consumers) receives and processes it. Load balancing is built‑in.

**Use cases:** Background job processing, workload distribution, reliable task execution with retries.

#### 1.4.2 Publish-Subscribe Model
Producers publish messages to a topic. Multiple subscribers can receive each message. Each subscriber gets its own copy.

**Use cases:** Event‑driven architectures, real‑time feeds (stock prices, sports scores), log aggregation.

#### 1.4.3 Fan-Out Messaging
A single message is broadcast to multiple consumers. This is the essence of pub/sub. Kafka topics naturally support fan‑out because each consumer group reads all messages.

#### 1.4.4 Comparison & Tradeoffs
| Feature | Message Queue | Publish-Subscribe |
|---------|---------------|-------------------|
| Delivery target | One consumer | Multiple subscribers |
| Message removal | After consumption | Retained per subscriber |
| Load balancing | Yes (competing consumers) | No (each gets all) |
| Typical use | Task distribution | Event broadcasting |
| Examples | RabbitMQ (queue), SQS | Kafka, SNS |

---

## 2. Messaging Systems

### 2.1 RabbitMQ

#### 2.1.1 Architecture
RabbitMQ is a traditional message broker implementing AMQP. Producers send messages to an **exchange**, which routes them to **queues** based on routing keys and bindings. Consumers pull from queues.

#### 2.1.2 Exchanges & Queues
- **Exchange:** Receives messages from producers and routes them.
- **Queue:** Stores messages until consumed.
- **Binding:** Defines the relationship between an exchange and a queue.

#### 2.1.3 Routing Mechanisms
- **Direct exchange:** Route by exact routing key.
- **Topic exchange:** Route by pattern (wildcards).
- **Fanout exchange:** Broadcast to all bound queues.
- **Headers exchange:** Route based on message headers.

#### 2.1.4 Acknowledgements
Consumers acknowledge messages after successful processing. If a consumer fails before ack, the message is requeued and redelivered (at‑least‑once).

#### 2.1.5 Typical Use Cases
- Microservices RPC with reply queues.
- Background job queues (Celery).
- Complex routing workflows.

#### 2.1.6 Strengths & Limitations
**Strengths:** Flexible routing, low latency, mature ecosystem.  
**Limitations:** Lower throughput than Kafka, messages not retained after consumption by default.

### 2.2 Apache Kafka

#### 2.2.1 Kafka Architecture
Kafka is a distributed event streaming platform. It consists of brokers, topics, partitions, producers, and consumers. A cluster manages metadata via ZooKeeper or KRaft.

#### 2.2.2 Topics & Partitions
A topic is split into partitions. Each partition is an ordered, immutable, append‑only log. Order is guaranteed within a partition.

#### 2.2.3 Brokers
A Kafka server that hosts partitions. Each partition has one leader broker that handles reads/writes, and followers that replicate data.

#### 2.2.4 Consumer Groups
Consumers in the same group coordinate to share partitions. Each partition is assigned to exactly one consumer in the group. This provides parallel consumption and load balancing.

#### 2.2.5 Replication
Each partition has multiple replicas (replication factor). The leader handles all reads/writes; followers replicate and act as standbys. If the leader fails, a follower is elected.

#### 2.2.6 Message Retention
Kafka retains messages on disk for a configurable time (e.g., 7 days) or size, regardless of consumption. Consumers can replay messages by resetting their offsets.

#### 2.2.7 Ordering Guarantees
Messages within a partition are strictly ordered. If a producer uses a key, all messages with the same key go to the same partition, preserving order per key.

#### 2.2.8 Typical Use Cases
- Event sourcing and CQRS.
- Real‑time stream processing (Kafka Streams, Flink).
- Log aggregation and metrics.
- Data integration pipelines.

#### 2.2.9 Strengths & Limitations
**Strengths:** Very high throughput (millions msg/sec), durability, replayability, horizontal scalability.  
**Limitations:** No complex routing, higher operational complexity, higher single‑message latency than RabbitMQ.

### 2.3 Kafka vs RabbitMQ

#### 2.3.1 Throughput Comparison
- **Kafka:** Millions of messages per second.
- **RabbitMQ:** Tens of thousands per second.

#### 2.3.2 Latency Comparison
- **Kafka:** Milliseconds (optimized for batching).
- **RabbitMQ:** Microseconds (low overhead).

#### 2.3.3 Ordering Guarantees
- **Kafka:** Per‑partition ordering.
- **RabbitMQ:** Per‑queue ordering (with single consumer).

#### 2.3.4 Persistence Models
- **Kafka:** Always persisted to disk; configurable retention.
- **RabbitMQ:** Optional persistence; messages deleted after consumption.

#### 2.3.5 Replay Capability
- **Kafka:** Full replay by rewinding offset.
- **RabbitMQ:** No replay (messages gone).

#### 2.3.6 Operational Complexity
- **Kafka:** Requires managing ZooKeeper/KRaft, brokers, partition balancing.
- **RabbitMQ:** Simpler setup and management.

#### 2.3.7 When to Choose Which
**Choose Kafka for:** Event streaming, high throughput, multiple consumers, replay, audit logs.  
**Choose RabbitMQ for:** Task queues, complex routing, request‑reply patterns, simpler setups.

### 2.4 Other Messaging Systems

#### 2.4.1 Amazon SQS
Managed message queue. Standard queues offer at‑least‑once; FIFO queues offer exactly‑once with limited throughput. No complex routing.

#### 2.4.2 Google Pub/Sub
Fully managed pub/sub with at‑least‑once delivery and global routing. Supports push and pull subscriptions.

#### 2.4.3 Apache Pulsar
Next‑gen messaging system with separate compute (brokers) and storage (BookKeeper). Supports multi‑tenancy, geo‑replication, and unified queue/stream model.

#### 2.4.4 NATS
Lightweight, high‑performance messaging system. Supports pub/sub, request‑reply, and queue groups. Simpler than Kafka, often used in IoT and microservices.

#### 2.4.5 Redis Streams
Append‑only log data structure in Redis. Supports consumer groups, message persistence, and replay. Suitable for lighter streaming workloads.

---

## 3. Delivery Guarantees & Reliability

### 3.1 Delivery Semantics

#### 3.1.1 At-Most-Once Delivery
Message may be lost, never duplicated. Fastest, but not safe for critical data.  
**Use when:** Loss is acceptable (metrics, some logs).

#### 3.1.2 At-Least-Once Delivery
Message will not be lost, but duplicates are possible. Consumer must handle idempotency.  
**Use when:** Most business logic with idempotent consumers.

#### 3.1.3 Exactly-Once Delivery
Message processed exactly once – no loss, no duplication. Requires coordination and has performance overhead.  
**Use when:** Financial transactions, critical updates.

#### 3.1.4 Tradeoffs Between Guarantees
| Semantic | Data Loss | Duplicates | Overhead |
|----------|-----------|------------|----------|
| At‑most‑once | Possible | No | Lowest |
| At‑least‑once | No | Possible | Medium |
| Exactly‑once | No | No | Highest |

### 3.2 Exactly-Once Processing

#### 3.2.1 Producer Idempotency
Producers assign a sequence number to each message. The broker deduplicates by sequence number, preventing duplicate writes from retries.

#### 3.2.2 Transactions
Producers can wrap multiple operations in a transaction. Either all messages are committed or none. Kafka supports transactional producers.

#### 3.2.3 Offset Management
Consumers commit offsets after processing. In exactly‑once scenarios, the offset commit must be atomic with the processing output (Kafka’s transactional API).

#### 3.2.4 Kafka Exactly-Once Semantics
Kafka provides EOS through:
- Idempotent producer (`enable.idempotence=true`)
- Transactional writes across partitions
- Consumer isolation level `read_committed`

### 3.3 Reliability Mechanisms

#### 3.3.1 Acknowledgements (ACKs)
In Kafka, `acks` config:
- `acks=0`: producer does not wait (at‑most‑once).
- `acks=1`: leader acknowledgment (at‑least‑once, minimal durability).
- `acks=all` (or `-1`): all in‑sync replicas acknowledge (strongest durability).

#### 3.3.2 Retries
On transient failures, consumers and producers retry. Retries must be bounded and use exponential backoff with jitter.

#### 3.3.3 Dead Letter Queues (DLQ)
After max retries, messages are moved to a DLQ for manual inspection. Prevents poison messages from blocking the main queue.

#### 3.3.4 Retry Backoff
Exponential backoff: wait 100ms, 200ms, 400ms… with random jitter to avoid thundering herd.

#### 3.3.5 Poison Message Handling
A poison message is one that consistently fails. Move it to a DLQ after a configured number of attempts to avoid infinite retry loops.

### 3.4 Replication & Durability

#### 3.4.1 Leader-Follower Replication
Each partition has a leader and followers. The leader handles all reads/writes; followers replicate the log.

#### 3.4.2 ISR (In-Sync Replicas)
The set of replicas that are fully caught up with the leader. Only ISR members can be elected as the new leader to prevent data loss.

#### 3.4.3 Replication Factor
Number of copies of each partition (e.g., RF=3). Higher RF improves durability but uses more storage.

#### 3.4.4 Durability vs Latency Tradeoff
Higher durability (`acks=all`) means more latency (wait for all replicas). Lower durability (`acks=1`) reduces latency but risks data loss.

---

## 4. Idempotency

### 4.1 Why Idempotency Matters

#### 4.1.1 Duplicate Message Problems
At‑least‑once delivery can deliver duplicates. Without idempotency, a payment could be charged twice, an order created twice, or an email sent multiple times.

#### 4.1.2 Retry Safety
Network failures, broker restarts, and consumer crashes all trigger retries. Idempotent operations make retries safe.

#### 4.1.3 Financial Transaction Safety
In payments, exactly‑once processing is critical. Idempotency keys prevent double charges and are the industry standard (Stripe, PayPal).

### 4.2 Idempotency Techniques

#### 4.2.1 Natural Idempotency
Some operations are naturally idempotent:
- Setting a specific value: `SET status = 'active'`
- Deleting a resource: `DELETE /user/123`
- Absolute value updates rather than increments.

#### 4.2.2 Message IDs
Producer attaches a unique ID to each message. Consumer stores processed IDs (e.g., in Redis with TTL). Before processing, check if ID is already stored.

#### 4.2.3 Deduplication Tables
Use a database table with the message ID as the primary key. Insert before processing; if the insert fails (duplicate), skip processing.

#### 4.2.4 Database Constraints
Use unique constraints on business keys (e.g., `order_id`). Duplicate insert throws an error that can be caught and safely ignored.

#### 4.2.5 Optimistic Locking
For updates, include a version number. If the version doesn’t match (already processed), reject the update.

#### 4.2.6 Version Numbers
Similar to optimistic locking; each record has a version. Consumer updates only if the version is newer.

#### 4.2.7 Kafka Idempotent Producer
Kafka’s idempotent producer assigns a producer ID and sequence number to each message. The broker deduplicates by sequence number, eliminating duplicates from producer retries.

### 4.3 Real-World Idempotency

#### 4.3.1 Payment Processing
- Use an `idempotency_key` passed by the client.
- Server checks if the key has been processed; if yes, return the same response without charging again.

#### 4.3.2 Order Creation
- Database `order_id` as primary key. Duplicate insert throws unique constraint violation → catch and return existing order.

#### 4.3.3 Email Notifications
- Not strictly idempotent but duplicates are acceptable if rare. If exact‑once is required, store a hash of the email content and recipient; skip duplicates.

---

## 5. Asynchronous Processing Patterns

### 5.1 Event-Driven Architecture

#### 5.1.1 Event Producers
Services that emit events when state changes (e.g., “Order Placed”).

#### 5.1.2 Event Consumers
Services that react to events (e.g., “Send Confirmation Email”).

#### 5.1.3 Event Bus
The messaging backbone (Kafka, RabbitMQ) that routes events from producers to consumers.

#### 5.1.4 Loose Coupling
Producers and consumers are completely independent. Changes in one do not affect the other. New consumers can be added without modifying producers.

### 5.2 Publish-Subscribe Pattern

#### 5.2.1 Fan-Out Messaging
A single message is sent to multiple subscribers simultaneously. Example: a “user signup” event triggers welcome email, analytics, and CRM update.

#### 5.2.2 Multiple Consumers
Each consumer maintains its own offset or subscription, allowing independent processing rates.

#### 5.2.3 Event Broadcasting
Useful for notifications, real‑time dashboards, and cache invalidation across services.

### 5.3 Work Queue Pattern

#### 5.3.1 Task Distribution
A producer publishes tasks to a queue. Multiple worker consumers compete to process them, sharing the load.

#### 5.3.2 Competing Consumers
Workers scale horizontally. The queue ensures each task is processed by exactly one worker (point‑to‑point).

#### 5.3.3 Background Job Processing
CPU‑intensive or long‑running tasks (video transcoding, report generation) are submitted to a queue and processed asynchronously.

### 5.4 Saga Pattern

#### 5.4.1 Distributed Transactions
Instead of using 2PC across services, a saga breaks the transaction into a sequence of local transactions, each with a compensating action.

#### 5.4.2 Choreography
Services communicate via events. Each service listens to events and performs its local transaction. No central coordinator.

#### 5.4.3 Orchestration
A central saga orchestrator tells services what to do, step by step. Easier to manage complex workflows.

#### 5.4.4 Compensation Actions
If a step fails, previously completed steps are undone via compensating transactions (e.g., refund a payment, release inventory).

### 5.5 CQRS + Event Sourcing

#### 5.5.1 Command Side
Handles writes; emits events representing state changes.

#### 5.5.2 Query Side
Maintains a read‑optimized projection built from events. Can be eventually consistent.

#### 5.5.3 Event Store
The append‑only log (Kafka) that stores all events. Acts as the system of record.

#### 5.5.4 Replayability
Events can be replayed to rebuild projections, migrate schemas, or fix bugs.

---

## 6. Scaling Messaging Systems

### 6.1 Partitioning

#### 6.1.1 Why Partitioning Matters
Partitions enable parallel processing. More partitions = more consumers can work simultaneously.

#### 6.1.2 Partition Keys
Messages with the same key always go to the same partition, preserving order per key.

#### 6.1.3 Ordering Within Partitions
Kafka guarantees strict order within a partition. If global order is needed, use a single partition (limits parallelism).

#### 6.1.4 Hot Partition Problems
If a key is very active (e.g., celebrity user), its partition may become overloaded. Solutions: split the hot key, use compound keys, or add random salt.

### 6.2 Consumer Scaling

#### 6.2.1 Consumer Groups
Partitions are assigned to consumers in a group. Maximum parallelism = number of partitions.

#### 6.2.2 Parallel Consumption
Add more consumers up to the number of partitions. If consumers exceed partitions, some will be idle.

#### 6.2.3 Rebalancing
When consumers join or leave, partitions are rebalanced. During rebalancing, consumption pauses briefly.

#### 6.2.4 Lag Monitoring
Consumer lag = difference between the latest offset and the consumer’s current offset. High lag indicates consumers can’t keep up; scale consumers or optimize processing.

### 6.3 Backpressure Handling

#### 6.3.1 Slow Consumers
When consumers are slower than producers, the queue or partition grows.

#### 6.3.2 Queue Build-Up
Unbounded growth can exhaust memory/disk. Solutions: bounded queue with blocking or dropping, scaling consumers, or rate limiting producers.

#### 6.3.3 Rate Limiting
Producers can be throttled to match consumer capacity. Can be implemented at the API gateway or broker level.

#### 6.3.4 Flow Control
Kafka’s pull model naturally provides backpressure: consumers pull at their own pace. RabbitMQ can set a prefetch limit to control unacknowledged messages.

#### 6.3.5 Load Shedding
Drop non‑critical messages when the system is overloaded, preserving essential processing.

### 6.4 Message Ordering

#### 6.4.1 Global Ordering Challenges
Ensuring total order across all messages requires a single partition, which limits throughput to one consumer thread.

#### 6.4.2 Per-Partition Ordering
Use a key that groups related messages (e.g., `user_id`, `order_id`) to guarantee order within that group while maintaining parallelism across groups.

#### 6.4.3 Ordering vs Scalability Tradeoff
Strict ordering reduces parallelism. Accept eventual consistency or partial ordering where the business logic permits.

---

## 7. Common Messaging Problems

### 7.1 Duplicate Messages

#### 7.1.1 Causes
Producer retries, consumer rebalancing, network timeouts, or broker failure.

#### 7.1.2 Deduplication Strategies
- Idempotent consumers (see Section 4).
- Deduplication tables with message IDs.
- Kafka idempotent producer (for producer‑side duplicates).

### 7.2 Message Loss

#### 7.2.1 Broker Failures
If a broker crashes before replication, messages can be lost. Mitigation: `acks=all`, `min.insync.replicas > 1`.

#### 7.2.2 Replication Failures
If an ISR shrinks below the minimum, producers may fail or messages might be lost. Monitor ISR size and configure appropriately.

#### 7.2.3 ACK Failures
If a consumer crashes after processing but before committing offset, the message is redelivered (at‑least‑once). Combine with idempotency to prevent side effects.

### 7.3 Consumer Lag

#### 7.3.1 Causes
Slow processing, insufficient consumer instances, or sudden spike in production.

#### 7.3.2 Monitoring
Track `consumer_lag` metric. Alert if lag exceeds a threshold.

#### 7.3.3 Scaling Consumers
Add consumer instances (up to partition count). Optimize processing logic. Increase batch sizes if appropriate.

### 7.4 Poison Messages

#### 7.4.1 Retry Loops
A malformed message that always fails causes infinite retries, blocking progress.

#### 7.4.2 DLQ Strategies
After N retries, move the message to a dead‑letter queue. Monitor the DLQ and fix the issue manually.

#### 7.4.3 Failure Isolation
Configure retry policies (max attempts, backoff) per queue or consumer group.

### 7.5 Ordering Issues

#### 7.5.1 Out-of-Order Processing
If multiple consumers process messages from the same partition (not allowed in Kafka, but possible in other systems), ordering is lost. Kafka’s consumer group model prevents this.

#### 7.5.2 Rebalancing Side Effects
During rebalance, a consumer may process a message that was already processed by another, leading to duplicates. Idempotency handles this.

---

## 8. Messaging Patterns by System Type

### 8.1 Summary: Messaging & Queues Techniques by Pattern

| Pattern | System Type | Primary Messaging Pattern | Technology | Delivery Guarantee | Why |
|---------|-------------|---------------------------|------------|---------------------|-----|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | **Async task queue** (email, analytics, expiry) | RabbitMQ, SQS | At‑least‑once + idempotency | Non‑critical operations can be deferred; user doesn't need to wait |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | **Pub/Sub** for fan‑out; **Stream processing** | Kafka, SNS | At‑least‑once | Tweets must be delivered to followers; high throughput |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | **Message queue** for delivery; **Kafka** for persistence | Kafka, Redis Streams | At‑least‑once (idempotent consumer) | Messages must be delivered even if receiver is offline |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | **Streaming index updates** (CDC) | Kafka, Debezium | At‑least‑once | Search index must reflect new content with low latency |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | **Location update stream**; **Event sourcing** | Kafka, Kinesis | At‑least‑once (idempotent) | Driver locations are high‑volume; need to replay for analytics |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | **Transaction log**; **Saga orchestration** | Kafka, RabbitMQ (with persistence) | Exactly‑once (with idempotency) | Financial transactions cannot be lost or duplicated |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | **Event streaming** (Kafka as backbone) | Kafka, Pulsar | At‑least‑once + deduplication | High throughput, replayability, retention |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | **Operation broadcast**; **Task queue** | Redis Pub/Sub, Kafka | At‑most‑once (real‑time) or at‑least‑once | Real‑time collaboration needs low latency; crawler tasks can be retried |

---

### 8.2 Pattern A – CRUD & Storage Systems (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **URL Shortener** | **Async queue** for analytics (click logging); **Dead letter queue** for failed analytics | Click logging is not user‑facing; can be async to reduce redirect latency. | On redirect, publish message to RabbitMQ/SQS with click data (IP, user agent, timestamp). Consumer processes and writes to analytics DB. Use DLQ for failed writes. |
| **Pastebin** | **Queue for expiry deletion**; **Pub/Sub** for syntax highlighting jobs | Expiry can be background; syntax highlighting is async. | When paste is created, schedule a delayed message in SQS (visibility timeout = TTL). On message received, delete paste. For syntax highlighting, fan‑out to workers. |
| **Online Bookstore** | **Order processing queue** (payment, inventory, shipping); **Dead letter queue** for failed orders | Order workflow has multiple steps; queue decouples services. | Order placed → message to Kafka (topic `orders`). Consumers: payment service, inventory service, shipping service. Use idempotency keys to avoid duplicate processing. |
| **Ticket Booking** | **Seat lock expiry queue** (delayed messages); **Confirmation queue** (email/SMS) | Seat locks expire after 5 minutes; delayed queue handles expiry. | On seat reservation, send a delayed message to SQS (delay = 5 min). If not booked, message triggers seat release. Email/SMS sent via separate queue. |
| **Dropbox / Google Drive** | **File processing queue** (virus scan, thumbnail generation, indexing) | File upload triggers multiple background tasks. | On file upload, publish message to Kafka (topic `file-events`). Consumers: virus scanner, thumbnail generator, search indexer. Use at‑least‑once with idempotency (file_id). |

---

### 8.3 Pattern B – Feed / Timeline Systems (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **Twitter / X** | **Kafka** for tweet ingestion; **Fan‑out via pub/sub** to follower timelines | Tweets must be delivered to all followers; Kafka provides durability and replay. | Tweet published to Kafka (topic `tweets`, partition by `user_id`). Stream processor consumes and writes to each follower’s timeline cache (fan‑out). Use at‑least‑once delivery. |
| **Instagram Feed** | **Kafka** for post events; **SNS** for push notifications | Similar to Twitter, plus notifications. | Post → Kafka → Stream processor updates timeline cache. Separate consumer sends push notifications via SNS. |
| **Facebook News Feed** | **Kafka** for activity stream; **Stream processor** for ranking | News feed requires real‑time ranking. | Activity stream (likes, comments, shares) → Kafka. Flink job computes relevance scores and updates feed cache. Use event‑time processing. |
| **LinkedIn Feed** | **Kafka** for profile updates; **Pull‑based** (no fan‑out queue) | Lower volume; pull model doesn't need fan‑out queue. | Profile updates go to Kafka for search indexing. Feed reads are pull (query DB), so no queue needed for delivery. |

---

### 8.4 Pattern C – Real-Time Systems (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | **Kafka** for message persistence; **WebSocket** for real‑time delivery (not queue) | Messages must be durable even if receiver offline. Kafka stores until delivered. | Message sent → stored in Kafka (topic `messages`, partition by `conversation_id`). Delivery service consumes and pushes via WebSocket. Consumer commits offset after delivery ack. |
| **Slack / Discord** | **Kafka** for channel messages; **Redis Streams** for real‑time presence | Channel history needs Kafka; presence is ephemeral. | Channel messages → Kafka. Redis Streams for presence updates (TTL 30s). Use separate consumer groups for different features (search, notifications, analytics). |
| **Live Comments (YouTube Live)** | **Kafka** for comment ingestion (high throughput); **Redis Pub/Sub** for real‑time broadcast | Kafka buffers spikes; Redis Pub/Sub delivers to live viewers. | Comments → Kafka (topic `live-comments`). Stream processor writes to Redis sorted set. Redis Pub/Sub broadcasts to WebSocket servers for live pushing. |
| **Multiplayer Game Backend** | **UDP** (no queue) for game state; **Kafka** for analytics and matchmaking | Real‑time game state uses UDP (lossy but fast). Kafka for non‑critical logs. | Game actions over UDP. Matchmaking requests sent to Kafka (topic `matchmaking`). Analytics events (e.g., score) go to Kafka for later processing. |

---

### 8.5 Pattern D – Search Systems (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **Google Search** | **Kafka** for indexing updates (CDC from primary DB); **Batch queue** for full rebuilds | Search index must reflect content changes with low latency. | Database changes (new page, update) → Kafka (topic `index-updates`). Indexer consumes and updates Elasticsearch. Full index rebuilds are batched via MapReduce. |
| **YouTube Search** | **Kafka** for video metadata updates; **Streaming indexer** | Video metadata changes (title, views) need near‑real‑time indexing. | Video upload/update → Kafka. Flink job enriches metadata and writes to Elasticsearch. Use at‑least‑once with upsert by `video_id`. |
| **Amazon Product Search** | **Kafka** for product catalog changes; **Inventory updates via stream** | Inventory changes frequently; search must reflect real‑time availability. | Product catalog changes → Kafka. Inventory updates → separate Kafka topic. Search indexer combines both. Use `min.insync.replicas=2` for durability. |
| **Search Autocomplete** | **Kafka** for query log stream; **Batch job** to rebuild trie | Suggestions are derived from historical queries, not real‑time. | User queries anonymized → Kafka (topic `queries`). Hourly batch job aggregates counts and rebuilds trie, pushes to CDN. |

---

### 8.6 Pattern E – Geospatial & Location-Based Systems (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **Uber / Lyft** | **Kafka** for driver location stream; **Kinesis** for real‑time geospatial processing | Driver locations are high‑volume (millions per minute). Kafka buffers and replays. | Driver sends location every 3s → Kafka (topic `driver-locations`, partition by `city`). Stream processor (Flink) updates Redis Geo and detects ride requests. |
| **Google Maps / Nearby Places** | **Kafka** for place updates (new business, reviews); **Batch queue** for map tile generation | Place data changes slowly; map tiles are generated offline. | Place updates → Kafka. Consumer updates PostgreSQL/Elasticsearch. Map tile generation runs as batch job consuming from queue (SQS). |
| **Food Delivery (DoorDash)** | **Kafka** for driver location; **SQS** for order assignment retries | Order assignment must be reliable; retries handled via queue. | Driver location → Kafka. Order request → SQS; consumer attempts driver matching; on failure, message returns to queue with visibility timeout. |
| **Nearby Friends** | **Kafka** for location updates (optional); **WebSocket** for real‑time push (no queue) | Location sharing is real‑time; queue adds latency. | Client sends location via WebSocket; server broadcasts to friends via WebSocket (no persistent queue). Kafka used only for analytics. |

---

### 8.7 Pattern F – Financial & Critical Consistency Systems (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | **Kafka** for transaction log; **Saga orchestrator** using message queue | Transaction log must be immutable and replayable. Sagas need reliable messaging. | Payment events (authorized, captured, settled) → Kafka (topic `payments`). Saga orchestrator uses SQS with dead‑letter queue for compensating transactions. Idempotency keys prevent duplicates. |
| **Wallet System** | **Kafka** for transaction log; **Redis Streams** for balance updates (optional) | Every balance change is a financial event; Kafka provides durability. | All wallet transactions (debit/credit) appended to Kafka (topic `wallet-tx`). Balance service consumes and updates DB. Use exactly‑once semantics via idempotent producer + consumer. |
| **UPI-like System** | **Kafka** for inter‑bank messaging; **Saga** with compensating queue | Cross‑bank payments need reliable, ordered messaging. | Payment request → Kafka (topic `upi-requests`, partition by `transaction_id`). Each bank’s consumer processes step; on failure, sends compensation message to DLQ. |
| **Distributed Job Scheduler** | **Kafka** for job queue (persistent); **Leader election** for consumer groups | Jobs must survive scheduler failure. Kafka provides replay. | Scheduled jobs written to Kafka (topic `scheduled-jobs`, partition by job type). Workers in same consumer group; Kafka handles rebalancing on failure. |

---

### 8.8 Pattern G – Large-Scale Processing & Infrastructure (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **Google Analytics** | **Kafka** as event bus; **Kinesis** for real‑time ingestion; **Flink** for stream processing | Analytics events are high volume; Kafka is the backbone. | Events (page views, clicks) → Kafka (topic `analytics-events`, partitioned by `customer_id`). Flink consumes, aggregates windows, writes to BigQuery. Use `acks=all` for durability. |
| **Clickstream Processing** | **Kafka** for raw clickstream; **Flink** with Kafka source and sink | Clickstream needs exactly‑once processing for accurate funnels. | Clickstream → Kafka. Flink job with exactly‑once checkpointing writes back to Kafka (topic `sessionized-events`). Use transactional producers. |
| **Distributed Logging (ELK)** | **Kafka** as log buffer; **Logstash** as consumer; **Elasticsearch** as sink | Logs are high volume; Kafka decouples producers from consumers. | Logs → Kafka (topic `logs`, partition by service). Logstash consumers read, parse, and write to Elasticsearch. Kafka retains logs for 7 days for replay. |
| **Metrics Monitoring (Prometheus)** | **No queue** (Prometheus pulls); **Kafka** for long‑term aggregation (optional) | Prometheus uses pull model, not push. But metrics can be forwarded to Kafka for centralisation. | Prometheus scrapes metrics; remote write to Kafka (topic `metrics`). Thanos receiver consumes and stores in S3. |
| **Design Kafka-like System** | **Raft** for metadata; **Segmented log** for messages; **Producer/consumer APIs** | Core of Kafka is a replicated, partitioned log. | Use Raft for controller. Each partition has leader with followers. Producers write to leader; consumers read from leader or followers. Use `ISR` (in‑sync replicas) for durability. |

---

### 8.9 Pattern H – Collaborative & Infrastructure Utilities (Messaging & Queues Focus)

| Question | Messaging / Queue Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------------|-------------------|----------------------------|
| **Google Docs** | **Redis Pub/Sub** for real‑time operation broadcast; **No persistent queue** | CRDT operations need low‑latency broadcast; persistence not required. | When user types, operation sent to WebSocket server, which publishes to Redis Pub/Sub channel per document. Other servers subscribe and forward to clients. |
| **Figma / Collaborative Whiteboard** | **Redis Pub/Sub** for real‑time updates; **Kafka** for operation history (optional) | Real‑time needs Redis; history can go to Kafka for replay. | Operation broadcast via Redis Pub/Sub. For version history, operations also written to Kafka (topic `figma-operations`) for replay and analytics. |
| **Distributed Locking Service** | **No message queue** (locks are synchronous) | Locks are acquired via Raft, not async queues. | etcd/ZooKeeper provide linearizable lock acquisition. No queue needed. |
| **Unique ID Generator (Snowflake)** | **No queue** (local generation) | No messaging needed; IDs are generated locally. | N/A |
| **Web Crawler** | **Redis queue** for URL frontier (list or sorted set); **Kafka** for crawled content | Frontier needs priority queue (politeness). Kafka for downstream processing. | Frontier: Redis sorted set (score = priority). Workers `ZPOPMIN` to get URL. Crawled content → Kafka (topic `crawled-pages`) for indexing and storage. |

---

## 9. Real-World Messaging Architectures

### 9.1 Uber-Like Systems

#### 9.1.1 Driver Location Streams
Every 3 seconds, drivers send GPS updates to Kafka. Stream processors update geospatial indexes.

#### 9.1.2 Ride Event Pipelines
Ride lifecycle events (requested, accepted, arrived, completed) flow through Kafka. Multiple services consume them independently.

#### 9.1.3 Dispatch Messaging
The dispatch service publishes ride requests to a topic; driver matching services consume and send offers.

### 9.2 E-Commerce Systems

#### 9.2.1 Order Processing Pipelines
Order placed → message to Kafka. Consumers: payment, inventory, shipping, notification.

#### 9.2.2 Inventory Updates
Inventory changes are published as events; multiple services consume to update caches and search indexes.

#### 9.2.3 Payment Workflows
Payment service uses idempotent Kafka producer and saga pattern with compensating transactions.

### 9.3 Social Media Systems

#### 9.3.1 Notification Pipelines
Tweet created → Kafka → notification service sends push to followers.

#### 9.3.2 Feed Generation
Activity stream is consumed by fan‑out service that updates timeline caches.

#### 9.3.3 Analytics Events
All user interactions are streamed to Kafka and processed for metrics and A/B testing.

---

## 10. Interview-Oriented HLD Notes

### 10.1 Key Tradeoffs

#### 10.1.1 Throughput vs Ordering
More partitions = more throughput, but global ordering is lost. Balance based on requirements.

#### 10.1.2 Durability vs Latency
Synchronous replication (`acks=all`) increases latency but ensures durability. Asynchronous is faster but risks loss.

#### 10.1.3 Consistency vs Availability
In a partition, a message broker must choose: remain available (accept writes, may lose consistency) or preserve consistency (refuse writes). Kafka typically chooses consistency (CP).

#### 10.1.4 Simplicity vs Scalability
Queue systems (SQS, RabbitMQ) are simpler. Kafka is more scalable but operationally complex.

### 10.2 Common Interview Questions

#### 10.2.1 Kafka vs RabbitMQ?
**Kafka:** High throughput, replay, stream processing. **RabbitMQ:** Low latency, flexible routing, simpler.

#### 10.2.2 How to Achieve Exactly-Once?
Combine idempotent producer, transactional broker, idempotent consumer, and deduplication.

#### 10.2.3 How to Handle Duplicate Messages?
Use idempotency keys, deduplication tables, or natural idempotent operations.

#### 10.2.4 How to Scale Consumers?
Add more consumers within the consumer group (up to the number of partitions). Use auto‑scaling based on lag.

#### 10.2.5 How to Maintain Ordering?
Use a partition key that groups related messages. Kafka guarantees ordering within a partition.

### 10.3 Quick Revision Material

#### 10.3.1 Queue vs Stream Cheat Sheet
| | Queue | Stream |
|---|---|---|
| Delivery | One consumer | Multiple consumers |
| Retention | Deleted after ack | Retained for days/weeks |
| Replay | No | Yes |
| Example | RabbitMQ, SQS | Kafka, Kinesis |

#### 10.3.2 Delivery Guarantees Summary
| Semantic | Loss? | Duplicates? | Use Case |
|---|---|---|---|
| At‑most‑once | Yes | No | Metrics, logs |
| At‑least‑once | No | Yes | Most systems (with idempotency) |
| Exactly‑once | No | No | Payments, critical updates |

#### 10.3.3 Kafka Terminology Cheat Sheet
- **Topic** → Category
- **Partition** → Shard of a topic
- **Offset** → Position in partition
- **Broker** → Kafka server
- **Consumer Group** → Cooperative consumers
- **ISR** → In‑sync replicas

#### 10.3.4 Key Interview Sentences
> *“I’d use Kafka for the notification fan‑out. When a tweet is posted, a single message to Kafka triggers the notification, search indexer, and analytics services independently.”*

> *“For payment processing, at‑least‑once delivery with idempotency keys. The payment service checks the key before charging, preventing duplicate charges.”*

> *“The queue acts as a buffer during traffic spikes. On New Year’s Eve, Kafka absorbs the spike; consumers process at a steady rate – lag temporarily increases but drains afterward.”*

#### 10.3.5 Self-Test Questions
1. Twitter has 300M users. A celebrity with 50M followers tweets. What happens to the fan‑out without a queue? How does a queue help?
2. A payment service uses at‑least‑once delivery. How do you prevent double charges?
3. You have 1 Kafka topic with 3 partitions and 5 consumers in the same group. How many are actively processing? Why?
4. What’s the difference between Kafka and RabbitMQ? Give a scenario for each.
5. Your consumer is at 1,000 msg/s; producer jumps to 10,000 msg/s. What are three strategies to handle this?

*End of consolidated notes.*
