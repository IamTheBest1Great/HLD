# 07 — Message Queues & Async Processing
> **Why this matters:** Message queues are the backbone of every scalable system. They decouple producers from consumers, absorb traffic spikes, and enable async processing. Every complex system design uses them — notifications, payments, ride matching, feed generation.

---

## 1. The Core Problem: Tight Coupling

```
WITHOUT QUEUES (Tight Coupling):

User posts tweet
→ App server directly calls:
    ├─ Notification Service (push to 10M followers)
    ├─ Search Indexer (index the tweet)
    ├─ Analytics Service (record event)
    ├─ Fan-out Service (update 10M feed caches)
    └─ Anti-spam Service (check for spam)

PROBLEMS:
1. One service is slow → User waits (tweet post takes 5 seconds) ❌
2. One service crashes → Tweet post fails entirely ❌
3. 100K tweets/second → All downstream services get 100K QPS simultaneously ❌
4. Traffic spike at New Year → All services get the spike → all crash ❌
```

```
WITH QUEUES (Decoupled):

User posts tweet → App server writes to DB
→ App server puts message in QUEUE → Returns "Posted!" immediately ✅ (fast)

Queue delivers to consumers (at their own pace):
    ├─ Notification Service (reads from queue, processes)
    ├─ Search Indexer     (reads from queue, processes)
    ├─ Analytics Service  (reads from queue, processes)
    ├─ Fan-out Service    (reads from queue, processes)
    └─ Anti-spam Service  (reads from queue, processes)

BENEFITS:
1. User gets instant response, processing happens async ✅
2. One service crashes → Messages queue up → Retry when service recovers ✅
3. Traffic spike → Queue absorbs the spike → Services process at steady rate ✅
4. Easy to add new consumers without changing the producer ✅
```

---

## 2. Queue vs Stream

These are fundamentally different models.

```
QUEUE (Point-to-Point):
────────────────────────────────────────────────────────────────
Producer → [M1][M2][M3][M4][M5] → Consumer

- One consumer reads each message
- Message is DELETED after consumption
- Like a to-do list: check it off, it's gone
- Examples: RabbitMQ, Amazon SQS

STREAM (Log-Based / Pub-Sub):
────────────────────────────────────────────────────────────────
                  Consumer A (offset: 3)
                  Consumer B (offset: 5) ← different consumers, different positions
                         │
Producer → [M1][M2][M3][M4][M5][M6]... ← messages PERSIST

- Multiple consumers can read the SAME messages independently
- Each consumer tracks its own "offset" (position)
- Messages are NOT deleted after consumption (retained for days/weeks)
- Like a newspaper: each reader reads at their own pace; paper doesn't disappear
- Examples: Apache Kafka, AWS Kinesis, Google Pub/Sub

KEY QUESTION: "Can multiple services need to consume the same message?"
  YES → Stream (Kafka)
  NO  → Queue (RabbitMQ, SQS)
```

---

## 3. Apache Kafka — Deep Dive

Kafka is the most important messaging system to know for HLD interviews.

### Core Concepts

```
KAFKA ARCHITECTURE:
────────────────────────────────────────────────────────────────
               Kafka Cluster
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│   Topic: "tweets"                                            │
│   ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │
│   │ Partition 0 │ │ Partition 1 │ │ Partition 2 │          │
│   │ [T1][T4][T7]│ │ [T2][T5][T8]│ │ [T3][T6][T9]│          │
│   └─────────────┘ └─────────────┘ └─────────────┘          │
│        Broker 1        Broker 2        Broker 3              │
│                                                              │
└──────────────────────────────────────────────────────────────┘
       │                                       │
   Producer                              Consumer Groups
   (writes tweets)                            │
                                    ┌──────────┴──────────┐
                                    │                     │
                              Group "fanout"         Group "search"
                              Consumer A (P0)        Consumer X (P0)
                              Consumer B (P1)        Consumer Y (P1)
                              Consumer C (P2)        Consumer Z (P2)
```

### Key Terms

```
TOPIC: A named category of messages. Like a table in a DB.
  "tweets", "payments", "user-events"

PARTITION: A topic is split into N partitions for parallelism.
  A partition is an ordered, immutable sequence of messages.
  More partitions = more parallelism (more consumers can work in parallel)

OFFSET: Position of a message within a partition. Monotonically increasing.
  Consumer tracks its offset → knows where to resume after restart.

BROKER: A Kafka server. A cluster has multiple brokers.

CONSUMER GROUP: A set of consumers that together consume a topic.
  Each partition is consumed by exactly ONE consumer in the group.
  (No two consumers in the same group read the same partition simultaneously)
  → This gives parallelism + ordering guarantees within a partition.

REPLICATION FACTOR: How many copies of each partition exist.
  Replication=3 → Each partition on 3 brokers.
  Leader handles reads/writes. Followers are hot standby.
```

### Message Ordering in Kafka

```
KEY INSIGHT: Kafka guarantees ordering WITHIN a partition, not across partitions.

Tweets from user A → always go to the same partition (keyed by user_id)
→ All of user A's tweets are in order within that partition

If you need all messages globally ordered → use 1 partition (but you lose parallelism)

PRODUCER ROUTING:
  If message has a key (e.g., user_id):
    partition = hash(key) % num_partitions
    → Same key always goes to same partition → ordering preserved
  
  If no key: round-robin across partitions
```

### Kafka vs RabbitMQ

```
                    KAFKA                    RABBITMQ
─────────────────────────────────────────────────────────────────
Model               Log-based stream         Message queue
Message retention   Days/weeks (configurable) Deleted after consumption
Multiple consumers  YES (each tracks offset)  Complex (need exchanges/fanout)
Throughput          Very high (millions/sec)  High (thousands/sec)
Ordering            Per-partition             Per-queue
Replay messages     YES (rewind offset)       NO (consumed = gone)
Routing             Topic + partition         Complex exchange routing
Use case            Event streaming           Task queues, RPC
Best for            Event sourcing, analytics Job queues, microservice RPC
Examples            Twitter, Netflix, Uber    Celery, microservices RPC
```

### When to Choose Which

```
Choose KAFKA when:
  ✅ Multiple services need the same events (fan-out)
  ✅ You need to replay events (debugging, new service catch-up)
  ✅ High throughput (millions of events/sec)
  ✅ Event sourcing / audit log
  ✅ Real-time analytics pipelines

Choose RABBITMQ / SQS when:
  ✅ Simple task queue (workers pick up jobs)
  ✅ Complex routing rules (route by content)
  ✅ Request-Reply pattern (RPC)
  ✅ Exactly-once delivery with simpler setup
  ✅ Team already uses it and scale isn't extreme
```

---

## 4. Delivery Guarantees

Understanding delivery semantics is critical for payment, notification, and messaging systems.

### At-Most-Once Delivery

```
Producer sends message → Consumer receives it → processes it.
If processing fails: message is LOST (not retried).

FLOW:
  Producer: "Send tweet notification" → Queue → Consumer: processes
  Consumer crashes mid-processing → Message gone → Some users miss notification

WHEN TO USE: Acceptable data loss. E.g., analytics events where losing
             a few impressions is okay. Prioritize speed over completeness.
```

### At-Least-Once Delivery

```
Message is retried until confirmed consumed.
Risk: Consumer might process the same message MULTIPLE TIMES.

FLOW:
  Producer → Queue → Consumer: starts processing → crashes
  Queue: "Message not acknowledged" → Re-delivers message
  Consumer (or new consumer): processes again → ACKs

DUPLICATE RISK:
  User charges $100 → crashes → retried → charges $100 again!
  SOLUTION: Make consumers IDEMPOTENT (see below)

WHEN TO USE: Most systems. Notifications, feed updates, analytics.
             Combined with idempotency = safe for most use cases.
```

### Exactly-Once Delivery

```
Hardest to achieve. Each message processed exactly once.

IMPLEMENTATION:
  1. Deduplication ID: Producer assigns unique ID to each message.
     Consumer stores "processed message IDs" in DB.
     Before processing: check if ID already processed → skip if yes.
  
  2. Kafka Transactions: Kafka + idempotent producer + transactional consumer.
     Atomically write to Kafka + update offset.

PERFORMANCE COST: Significant overhead (DB checks, distributed transactions)
WHEN TO USE: Financial transactions. Payment processing. Order creation.
```

---

## 5. Idempotency — The Critical Concept

```
IDEMPOTENT OPERATION: Calling it multiple times has the same effect as once.

EXAMPLE:
  NOT idempotent:
    POST /payment → charges $100 → call twice → charged $200 ❌

  Idempotent:
    POST /payment {idempotency_key: "pay_abc123"} → charges $100
    Same request again → server checks: "pay_abc123 already processed"
                       → Returns same response, no second charge ✅

IMPLEMENTATION:
  Client generates unique ID (UUID) for each operation.
  Server stores: {idempotency_key → result} with TTL.
  Before processing: check if key exists.
  After processing: store result.

WHERE IDEMPOTENCY KEYS ARE USED:
  Payment APIs (Stripe uses them)
  Order creation
  Message sending
  Any write operation that might be retried
```

---

## 6. Pub/Sub Pattern

Publish-Subscribe decouples producers and consumers completely.

```
PUB/SUB FLOW:
─────────────────────────────────────────────────────────────────
Publishers post to TOPICS.
Subscribers subscribe to TOPICS they care about.
Message broker delivers to all subscribers.

Example: User posts a tweet
  ─────────────────────────────────────────────────────────────
  Publisher: Tweet Service
  Topic: "tweet.created"
  Message: { tweet_id: 123, user_id: 456, content: "Hello!" }

  Subscribers:
    ├─ Notification Service  (subscribed to "tweet.created")
    ├─ Search Indexer        (subscribed to "tweet.created")
    ├─ Fan-out Service       (subscribed to "tweet.created")
    └─ Analytics Service     (subscribed to "tweet.created")

Each subscriber independently processes the event.
Adding a new subscriber = no code changes to Tweet Service.
```

---

## 7. Backpressure

When consumers can't keep up with producers, backpressure prevents system overload.

```
PROBLEM:
  Producer: 10,000 messages/second
  Consumer: 1,000 messages/second
  → Queue grows without bound → eventually crashes everything

BACKPRESSURE STRATEGIES:

1. BOUNDED QUEUE + BLOCKING:
   Queue has max size (e.g., 10,000 messages).
   When full: producer BLOCKS (waits) until space is available.
   → Producer automatically slows down to match consumer rate.
   Risk: Producer might time out.

2. BOUNDED QUEUE + DROP:
   Queue full → new messages DROPPED.
   Good for: non-critical data (analytics, metrics).
   Bad for: financial transactions, notifications.

3. SCALE UP CONSUMERS:
   Add more consumer instances.
   Auto-scaling based on queue depth.
   AWS SQS + Lambda: Lambda auto-scales based on queue length.

4. RATE LIMITING PRODUCER:
   Tell the producer "you're going too fast".
   Producer slows its production rate.
```

---

## 8. Real-World Example: Uber's Message Queue Design

```
UBER RIDE REQUEST FLOW:
────────────────────────────────────────────────────────────────
User: "I need a ride from A to B"
  │
  ▼
Ride Service → publish to "ride.requested" topic
  │
  │  ┌─────────────────────────────────────────────────────┐
  │  │             Kafka Topic: "ride.requested"            │
  │  │  [req1][req2][req3][req4][req5][req6]...            │
  │  └─────────────────────────────────────────────────────┘
  │         │                    │                    │
  ▼         ▼                    ▼                    ▼
Matching   Pricing             ETA                Analytics
Service    Service            Service             Service
  │
  ▼
Find nearby drivers (Redis geohash)
  │
  ▼
Send offers to drivers (WebSocket)
  │
  ▼
Driver accepts → publish to "ride.accepted"
  │
  ▼
Notification Service (subscribes to "ride.accepted")
→ Push to rider: "Driver is on the way!"
```

---

## 📝 Key Interview Sentences

> *"I'd use Kafka for the notification fan-out. When a tweet is posted, a single message to Kafka triggers the notification service, search indexer, and analytics service independently. Each service consumes at its own pace, and a spike in tweets doesn't overwhelm any single service."*

> *"For payment processing, at-least-once delivery with idempotency keys. The payment service checks if the idempotency key was already processed before charging the card. Retries are safe because duplicate charges are prevented."*

> *"The queue acts as a buffer for traffic spikes. On New Year's Eve when tweet volume spikes 10x, Kafka absorbs the messages. The fan-out service processes at its normal steady rate — the queue depth just grows temporarily and drains over minutes."*

---

## ✅ Self-Test Questions

1. Twitter has 300M users. When a celebrity with 50M followers tweets, what happens to the fan-out notification system without a queue? How does a queue help?

2. A payment service uses at-least-once delivery. A user is charged $100. The consumer crashes mid-processing and retries. How do you prevent the user from being charged twice?

3. You have 1 Kafka topic with 3 partitions and 5 consumers in the same consumer group. How many consumers are actively processing? Why?

4. What's the difference between Kafka and RabbitMQ? Give a scenario where you'd choose each.

5. Your message queue consumer is processing 1,000 messages/second. Your producer suddenly starts at 10,000 messages/second. What are three strategies to handle this?
