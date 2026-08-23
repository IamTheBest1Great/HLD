I'll deliver the entire Database — Consolidated HLD Notes as plain formatted text (Markdown rendered naturally), exactly like the previous Availability document. No code blocks wrapping the content.

# Database — Consolidated HLD Notes

*(Table of Contents is included at the start, just like in the Availability notes. All sections and sub-sections follow, with diagrams, code snippets, and content preserved.)*

---

## Table of Contents

- [1. Database Fundamentals](#1-database-fundamentals)
  - [1.1 Why Databases Matter in Distributed Systems](#11-why-databases-matter-in-distributed-systems)
    - [1.1.1 Durability](#111-durability)
    - [1.1.2 Consistency](#112-consistency)
    - [1.1.3 Scalability](#113-scalability)
    - [1.1.4 High Availability](#114-high-availability)
    - [1.1.5 Low Latency Access](#115-low-latency-access)
  - [1.2 SQL vs NoSQL Overview](#12-sql-vs-nosql-overview)
    - [1.2.1 Relational Model](#121-relational-model)
    - [1.2.2 Schema Flexibility](#122-schema-flexibility)
    - [1.2.3 Horizontal Scalability](#123-horizontal-scalability)
    - [1.2.4 Consistency Tradeoffs](#124-consistency-tradeoffs)
    - [1.2.5 Polyglot Persistence](#125-polyglot-persistence)
- [2. SQL Databases](#2-sql-databases)
  - [2.1 SQL Database Fundamentals](#21-sql-database-fundamentals)
    - [2.1.1 Tables, Rows, Columns](#211-tables-rows-columns)
    - [2.1.2 Primary Keys](#212-primary-keys)
    - [2.1.3 Foreign Keys](#213-foreign-keys)
    - [2.1.4 Joins](#214-joins)
    - [2.1.5 Transactions](#215-transactions)
  - [2.2 ACID Properties](#22-acid-properties)
    - [2.2.1 Atomicity](#221-atomicity)
    - [2.2.2 Consistency](#222-consistency)
    - [2.2.3 Isolation](#223-isolation)
    - [2.2.4 Durability](#224-durability)
    - [2.2.5 Isolation Levels (ANSI SQL)](#225-isolation-levels-ansi-sql)
  - [2.3 SQL Database Advantages](#23-sql-database-advantages)
    - [2.3.1 Strong Consistency](#231-strong-consistency)
    - [2.3.2 Complex Queries](#232-complex-queries)
    - [2.3.3 Mature Ecosystem](#233-mature-ecosystem)
    - [2.3.4 Transaction Support](#234-transaction-support)
  - [2.4 SQL Database Limitations](#24-sql-database-limitations)
    - [2.4.1 Horizontal Scaling Challenges](#241-horizontal-scaling-challenges)
    - [2.4.2 Schema Rigidity](#242-schema-rigidity)
    - [2.4.3 Join Performance at Scale](#243-join-performance-at-scale)
  - [2.5 Popular SQL Databases](#25-popular-sql-databases)
    - [2.5.1 PostgreSQL](#251-postgresql)
    - [2.5.2 MySQL](#252-mysql)
    - [2.5.3 Oracle](#253-oracle)
    - [2.5.4 SQL Server](#254-sql-server)
- [3. NoSQL Databases](#3-nosql-databases)
  - [3.1 Why NoSQL?](#31-why-nosql)
    - [3.1.1 Massive Scale](#311-massive-scale)
    - [3.1.2 Flexible Schema](#312-flexible-schema)
    - [3.1.3 High Write Throughput](#313-high-write-throughput)
    - [3.1.4 Distributed Architectures](#314-distributed-architectures)
  - [3.2 Key-Value Stores](#32-key-value-stores)
    - [3.2.1 Characteristics](#321-characteristics)
    - [3.2.2 Use Cases](#322-use-cases)
    - [3.2.3 Tradeoffs](#323-tradeoffs)
    - [3.2.4 Examples (Redis, DynamoDB)](#324-examples-redis-dynamodb)
  - [3.3 Document Stores](#33-document-stores)
    - [3.3.1 Characteristics](#331-characteristics)
    - [3.3.2 Use Cases](#332-use-cases)
    - [3.3.3 Tradeoffs](#333-tradeoffs)
    - [3.3.4 Examples (MongoDB)](#334-examples-mongodb)
  - [3.4 Wide-Column Stores](#34-wide-column-stores)
    - [3.4.1 Characteristics](#341-characteristics)
    - [3.4.2 Use Cases](#342-use-cases)
    - [3.4.3 Tradeoffs](#343-tradeoffs)
    - [3.4.4 Examples (Cassandra, HBase)](#344-examples-cassandra-hbase)
  - [3.5 Graph Databases](#35-graph-databases)
    - [3.5.1 Characteristics](#351-characteristics)
    - [3.5.2 Use Cases](#352-use-cases)
    - [3.5.3 Tradeoffs](#353-tradeoffs)
    - [3.5.4 Examples (Neo4j)](#354-examples-neo4j)
  - [3.6 BASE Properties](#36-base-properties)
    - [3.6.1 Basically Available](#361-basically-available)
    - [3.6.2 Soft State](#362-soft-state)
    - [3.6.3 Eventual Consistency](#363-eventual-consistency)
  - [3.7 SQL vs NoSQL Decision Framework](#37-sql-vs-nosql-decision-framework)
    - [3.7.1 Consistency Requirements](#371-consistency-requirements)
    - [3.7.2 Query Patterns](#372-query-patterns)
    - [3.7.3 Scaling Requirements](#373-scaling-requirements)
    - [3.7.4 Data Relationships](#374-data-relationships)
    - [3.7.5 Flexibility Requirements](#375-flexibility-requirements)
- [4. Replication & High Availability](#4-replication--high-availability)
  - [4.1 Replication Fundamentals](#41-replication-fundamentals)
    - [4.1.1 Why Replication Matters](#411-why-replication-matters)
    - [4.1.2 High Availability](#412-high-availability)
    - [4.1.3 Read Scaling](#413-read-scaling)
    - [4.1.4 Disaster Recovery](#414-disaster-recovery)
  - [4.2 Leader-Follower Replication](#42-leader-follower-replication)
    - [4.2.1 Write Flow](#421-write-flow)
    - [4.2.2 Read Replicas](#422-read-replicas)
    - [4.2.3 Replication Lag](#423-replication-lag)
    - [4.2.4 Failover Handling](#424-failover-handling)
  - [4.3 Synchronous vs Asynchronous Replication](#43-synchronous-vs-asynchronous-replication)
    - [4.3.1 Synchronous Replication](#431-synchronous-replication)
    - [4.3.2 Asynchronous Replication](#432-asynchronous-replication)
    - [4.3.3 Latency vs Durability Tradeoff](#433-latency-vs-durability-tradeoff)
  - [4.4 Multi-Leader Replication](#44-multi-leader-replication)
    - [4.4.1 Active-Active Writes](#441-active-active-writes)
    - [4.4.2 Conflict Resolution](#442-conflict-resolution)
    - [4.4.3 Geo-Distributed Systems](#443-geo-distributed-systems)
  - [4.5 Read Replicas](#45-read-replicas)
    - [4.5.1 Scaling Reads](#451-scaling-reads)
    - [4.5.2 Read-After-Write Problems](#452-read-after-write-problems)
    - [4.5.3 Leader Reads](#453-leader-reads)
    - [4.5.4 Replica Routing](#454-replica-routing)
- [5. Database Partitioning & Sharding](#5-database-partitioning--sharding)
  - [5.1 Partitioning Fundamentals](#51-partitioning-fundamentals)
    - [5.1.1 Horizontal Partitioning](#511-horizontal-partitioning-sharding)
    - [5.1.2 Vertical Partitioning](#512-vertical-partitioning)
    - [5.1.3 Functional Partitioning](#513-functional-partitioning)
  - [5.2 Sharding Fundamentals](#52-sharding-fundamentals)
    - [5.2.1 Why Sharding Is Needed](#521-why-sharding-is-needed)
    - [5.2.2 Shard Key Selection](#522-shard-key-selection)
    - [5.2.3 Rebalancing](#523-rebalancing)
    - [5.2.4 Cross-Shard Queries](#524-cross-shard-queries)
    - [5.2.5 Hotspot Problems](#525-hotspot-problems)
  - [5.3 Sharding Strategies](#53-sharding-strategies)
    - [5.3.1 Range-Based Sharding](#531-range-based-sharding)
    - [5.3.2 Hash-Based Sharding](#532-hash-based-sharding)
    - [5.3.3 Consistent Hashing](#533-consistent-hashing)
    - [5.3.4 Directory-Based Sharding](#534-directory-based-sharding)
    - [5.3.5 Geographic Sharding](#535-geographic-sharding)
  - [5.4 Consistent Hashing](#54-consistent-hashing)
    - [5.4.1 Why Naive Hashing Fails](#541-why-naive-hashing-fails)
    - [5.4.2 Hash Ring](#542-hash-ring)
    - [5.4.3 Adding Nodes](#543-adding-nodes)
    - [5.4.4 Removing Nodes](#544-removing-nodes)
    - [5.4.5 Virtual Nodes (vnodes)](#545-virtual-nodes-vnodes)
    - [5.4.6 Real-World Examples](#546-real-world-examples)
  - [5.5 Sharding Challenges](#55-sharding-challenges)
    - [5.5.1 Data Rebalancing](#551-data-rebalancing)
    - [5.5.2 Uneven Distribution](#552-uneven-distribution)
    - [5.5.3 Cross-Shard Transactions](#553-cross-shard-transactions)
    - [5.5.4 Secondary Index Challenges](#554-secondary-index-challenges)
- [6. Database Indexing](#6-database-indexing)
  - [6.1 Why Indexes Matter](#61-why-indexes-matter)
    - [6.1.1 Faster Reads](#611-faster-reads)
    - [6.1.2 Query Optimization](#612-query-optimization)
    - [6.1.3 Reduced Full Table Scans](#613-reduced-full-table-scans)
  - [6.2 B-Tree Index](#62-b-tree-index)
    - [6.2.1 Structure](#621-structure)
    - [6.2.2 Range Queries](#622-range-queries)
    - [6.2.3 Time Complexity](#623-time-complexity)
    - [6.2.4 Real-World Usage](#624-real-world-usage)
  - [6.3 Hash Index](#63-hash-index)
    - [6.3.1 Structure](#631-structure)
    - [6.3.2 Exact Match Queries](#632-exact-match-queries)
    - [6.3.3 Limitations](#633-limitations)
  - [6.4 LSM Trees](#64-lsm-trees)
    - [6.4.1 Write Optimization](#641-write-optimization)
    - [6.4.2 SSTables](#642-sstables)
    - [6.4.3 Compaction](#643-compaction)
    - [6.4.4 Cassandra / RocksDB Usage](#644-cassandra--rocksdb-usage)
  - [6.5 Advanced Indexing](#65-advanced-indexing)
    - [6.5.1 Composite Index](#651-composite-index)
    - [6.5.2 Covering Index](#652-covering-index)
    - [6.5.3 Secondary Index](#653-secondary-index)
    - [6.5.4 Full-Text Index](#654-full-text-index)
    - [6.5.5 Geospatial Index](#655-geospatial-index)
  - [6.6 Index Tradeoffs](#66-index-tradeoffs)
    - [6.6.1 Faster Reads vs Slower Writes](#661-faster-reads-vs-slower-writes)
    - [6.6.2 Storage Overhead](#662-storage-overhead)
    - [6.6.3 Index Maintenance Cost](#663-index-maintenance-cost)
- [7. Database Performance Optimization](#7-database-performance-optimization)
  - [7.1 Query Optimization](#71-query-optimization)
    - [7.1.1 Query Plans](#711-query-plans)
    - [7.1.2 Avoiding N+1 Queries](#712-avoiding-n1-queries)
    - [7.1.3 Join Optimization](#713-join-optimization)
    - [7.1.4 Pagination Strategies](#714-pagination-strategies)
  - [7.2 Database Caching](#72-database-caching)
    - [7.2.1 Query Cache](#721-query-cache)
    - [7.2.2 Redis Cache](#722-redis-cache)
    - [7.2.3 Write-Through Cache](#723-write-through-cache)
    - [7.2.4 Cache Invalidation](#724-cache-invalidation)
  - [7.3 Denormalization](#73-denormalization)
    - [7.3.1 Why Denormalize](#731-why-denormalize)
    - [7.3.2 Read Optimization](#732-read-optimization)
    - [7.3.3 Duplication Tradeoffs](#733-duplication-tradeoffs)
  - [7.4 Materialized Views](#74-materialized-views)
    - [7.4.1 Precomputed Queries](#741-precomputed-queries)
    - [7.4.2 Refresh Strategies](#742-refresh-strategies)
    - [7.4.3 Reporting Systems](#743-reporting-systems)
  - [7.5 Connection Pooling](#75-connection-pooling)
    - [7.5.1 Why Pool Connections](#751-why-pool-connections)
    - [7.5.2 Pool Sizing](#752-pool-sizing)
    - [7.5.3 Exhaustion Problems](#753-exhaustion-problems)
- [8. Scaling Databases](#8-scaling-databases)
  - [8.1 Vertical Scaling](#81-vertical-scaling)
    - [8.1.1 More CPU / RAM](#811-more-cpu--ram)
    - [8.1.2 Hardware Limits](#812-hardware-limits)
    - [8.1.3 Simplicity Benefits](#813-simplicity-benefits)
  - [8.2 Horizontal Scaling](#82-horizontal-scaling)
    - [8.2.1 Read Scaling](#821-read-scaling)
    - [8.2.2 Write Scaling](#822-write-scaling)
    - [8.2.3 Distributed Challenges](#823-distributed-challenges)
  - [8.3 Read Replicas vs Sharding](#83-read-replicas-vs-sharding)
    - [8.3.1 When to Use Read Replicas](#831-when-to-use-read-replicas)
    - [8.3.2 When to Use Sharding](#832-when-to-use-sharding)
    - [8.3.3 Combining Both](#833-combining-both)
- [9. Database Patterns by System Type](#9-database-patterns-by-system-type)
  - [9.1 Summary: Database Techniques by Pattern](#91-summary-database-techniques-by-pattern)
  - [9.2 Pattern A – CRUD & Storage Systems (Database Focus)](#92-pattern-a---crud--storage-systems-database-focus)
  - [9.3 Pattern B – Feed / Timeline Systems (Database Focus)](#93-pattern-b---feed--timeline-systems-database-focus)
  - [9.4 Pattern C – Real-Time Systems (Database Focus)](#94-pattern-c---real-time-systems-database-focus)
  - [9.5 Pattern D – Search Systems (Database Focus)](#95-pattern-d---search-systems-database-focus)
  - [9.6 Pattern E – Geospatial & Location-Based Systems (Database Focus)](#96-pattern-e---geospatial--location-based-systems-database-focus)
  - [9.7 Pattern F – Financial & Critical Consistency Systems (Database Focus)](#97-pattern-f---financial--critical-consistency-systems-database-focus)
  - [9.8 Pattern G – Large-Scale Processing & Infrastructure (Database Focus)](#98-pattern-g---large-scale-processing--infrastructure-database-focus)
  - [9.9 Pattern H – Collaborative & Infrastructure Utilities (Database Focus)](#99-pattern-h---collaborative--infrastructure-utilities-database-focus)
- [10. Interview-Oriented HLD Notes](#10-interview-oriented-hld-notes)
  - [10.1 Key Database Tradeoffs](#101-key-database-tradeoffs)
    - [10.1.1 SQL vs NoSQL](#1011-sql-vs-nosql)
    - [10.1.2 Consistency vs Availability](#1012-consistency-vs-availability)
    - [10.1.3 Reads vs Writes](#1013-reads-vs-writes)
    - [10.1.4 Latency vs Durability](#1014-latency-vs-durability)
  - [10.2 Common Interview Questions](#102-common-interview-questions)
    - [10.2.1 Choosing a Database](#1021-choosing-a-database)
    - [10.2.2 Designing for Scale](#1022-designing-for-scale)
    - [10.2.3 Handling Hotspots](#1023-handling-hotspots)
    - [10.2.4 Multi-Region Challenges](#1024-multi-region-challenges)
  - [10.3 Quick Revision Material](#103-quick-revision-material)
    - [10.3.1 Decision Frameworks](#1031-decision-frameworks)
    - [10.3.2 Architecture Cheat Sheet](#1032-architecture-cheat-sheet)
    - [10.3.3 Key Interview Sentences](#1033-key-interview-sentences)
    - [10.3.4 Self-Test Questions](#1034-self-test-questions)

---

## 1. Database Fundamentals

### 1.1 Why Databases Matter in Distributed Systems

Databases are the backbone of nearly every application. In distributed systems, they must handle larger volumes of data, higher traffic, and remain available even when parts of the system fail.

#### 1.1.1 Durability
Durability ensures that once a transaction is committed, its changes survive system crashes, power outages, and hardware failures. This is achieved by persisting data to non‑volatile storage (disks, SSDs) and using techniques like write‑ahead logs (WAL).

#### 1.1.2 Consistency
Consistency in databases means that data satisfies all defined rules, constraints, and invariants. After any transaction, the database moves from one valid state to another. In distributed contexts, consistency often refers to how soon all nodes see the same data (see CAP theorem and consistency models).

#### 1.1.3 Scalability
A database must scale to handle growing data volumes and request rates. Vertical scaling (bigger servers) works up to a point; beyond that, horizontal scaling via replication and sharding is required.

#### 1.1.4 High Availability
High availability means the database remains operational even when individual nodes fail. This is accomplished through redundancy, automatic failover, and replication.

#### 1.1.5 Low Latency Access
Applications often require responses within milliseconds. Caching, in‑memory stores, and careful indexing help achieve low latency by reducing disk I/O and network round trips.

### 1.2 SQL vs NoSQL Overview

#### 1.2.1 Relational Model
SQL databases store data in structured tables with rows and columns. Relationships are defined via foreign keys. The schema is fixed and enforced.

#### 1.2.2 Schema Flexibility
NoSQL databases offer flexible schemas – you can store documents with varying fields, add columns dynamically, or use key‑value pairs without a predefined structure.

#### 1.2.3 Horizontal Scalability
NoSQL databases are typically designed from the ground up for horizontal scaling (adding more commodity servers). SQL databases can also scale horizontally, but it is more complex and often requires application‑level sharding.

#### 1.2.4 Consistency Tradeoffs
SQL databases provide strong consistency (ACID) by default. NoSQL systems often favor availability and partition tolerance (BASE), offering eventual consistency with tunable levels.

#### 1.2.5 Polyglot Persistence
Modern systems rarely use a single database. Instead, they employ multiple database types (SQL, key‑value, document, graph) each optimized for a specific part of the workload – a concept known as polyglot persistence.

## 2. SQL Databases

### 2.1 SQL Database Fundamentals

#### 2.1.1 Tables, Rows, Columns
Data is organized into tables (relations). Each table consists of rows (records) and columns (attributes). The structure is defined by a schema.

Example:
```sql
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  username VARCHAR(50),
  email VARCHAR(100)
);
```

#### 2.1.2 Primary Keys
A primary key uniquely identifies each row in a table. It enforces entity integrity and is usually indexed. Common choices are auto‑incrementing integers, UUIDs, or natural keys.

#### 2.1.3 Foreign Keys
A foreign key is a column that references the primary key of another table. It ensures referential integrity – you cannot have an order for a non‑existent user.

#### 2.1.4 Joins
Joins combine rows from two or more tables based on a related column. SQL supports INNER JOIN, LEFT JOIN, RIGHT JOIN, and more. Joins are powerful but can become expensive at scale if not properly indexed.

#### 2.1.5 Transactions
A transaction is a sequence of operations performed as a single logical unit of work. Transactions guarantee atomicity, consistency, isolation, and durability (ACID).

### 2.2 ACID Properties

ACID is a set of properties that guarantee reliable processing of database transactions.

#### 2.2.1 Atomicity
A transaction is an indivisible unit. Either all its operations succeed, or none do. If a failure occurs mid‑transaction, all changes are rolled back.

Example: Transferring $100 from A to B – both debit and credit must happen, or neither.

#### 2.2.2 Consistency
A transaction brings the database from one valid state to another, preserving all constraints (primary keys, foreign keys, uniqueness). No transaction can leave the database in an inconsistent state.

#### 2.2.3 Isolation
Concurrent transactions execute as if they were the only ones running. Intermediate states of one transaction are invisible to others until committed.

#### 2.2.4 Durability
Once a transaction is committed, its effects are permanent, even if the system crashes immediately after. Data is written to non‑volatile storage (disk) before acknowledging the commit.

#### 2.2.5 Isolation Levels (ANSI SQL)
SQL defines isolation levels to trade off consistency vs performance:

| Level | Dirty Reads | Non‑repeatable Reads | Phantom Reads |
|-------|-------------|----------------------|---------------|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed | Prevented | Possible | Possible |
| Repeatable Read | Prevented | Prevented | Possible |
| Serializable | Prevented | Prevented | Prevented |

- **Dirty read:** Reading uncommitted data from another transaction.
- **Non‑repeatable read:** Re‑reading a row and getting a different value because another transaction updated it.
- **Phantom read:** A query returns a different set of rows when re‑executed because another transaction inserted or deleted rows.

### 2.3 SQL Database Advantages

#### 2.3.1 Strong Consistency
SQL databases provide strong, ACID consistency by default. You can rely on the database to enforce constraints and maintain data integrity.

#### 2.3.2 Complex Queries
SQL offers a rich, declarative query language with support for complex joins, aggregations, subqueries, and window functions. It is unmatched for ad‑hoc analytical queries.

#### 2.3.3 Mature Ecosystem
Decades of development have produced robust tools for backup, monitoring, migration, and performance tuning. A large community and extensive documentation are available.

#### 2.3.4 Transaction Support
Full ACID transactions enable complex business operations (e.g., order processing, fund transfers) to be implemented safely and simply.

### 2.4 SQL Database Limitations

#### 2.4.1 Horizontal Scaling Challenges
SQL databases are traditionally scaled vertically. Horizontal scaling (sharding) is possible but requires application‑level logic for routing and brings complexity with cross‑shard joins and transactions.

#### 2.4.2 Schema Rigidity
The fixed schema makes it harder to accommodate rapid, frequent changes. Schema migrations can be time‑consuming and may require downtime.

#### 2.4.3 Join Performance at Scale
Joins across massive tables can become performance bottlenecks. Denormalization is often needed, trading some consistency for read speed.

### 2.5 Popular SQL Databases

#### 2.5.1 PostgreSQL
A powerful, open‑source object‑relational database known for extensibility, standards compliance, and advanced features (PostGIS, full‑text search, JSON support). Excellent for complex queries and ACID workloads.

#### 2.5.2 MySQL
Widely used for web applications. Known for simplicity and fast reads. Default storage engine InnoDB supports ACID transactions. Replication is straightforward.

#### 2.5.3 Oracle
Enterprise‑grade commercial database with extreme performance, advanced security, and comprehensive management tools. Common in large financial and governmental systems.

#### 2.5.4 SQL Server
Microsoft’s relational database, deeply integrated with the .NET ecosystem and Windows Server. Offers strong analytics and reporting capabilities.

## 3. NoSQL Databases

### 3.1 Why NoSQL?

#### 3.1.1 Massive Scale
NoSQL databases are engineered for horizontal scaling across hundreds of commodity servers, handling petabytes of data and millions of requests per second.

#### 3.1.2 Flexible Schema
They allow dynamic schemas – you can store heterogeneous data without migrations. This enables rapid iteration in agile development.

#### 3.1.3 High Write Throughput
Many NoSQL systems are optimized for high write volumes, using log‑structured storage (LSM trees) and append‑only designs.

#### 3.1.4 Distributed Architectures
Built with distributed systems principles: automatic sharding, replication, partition tolerance, and tunable consistency.

### 3.2 Key-Value Stores

#### 3.2.1 Characteristics
Simplest NoSQL model: a hash map where keys map to values (binary, JSON, etc.). Operations: GET, SET, DELETE. Often in‑memory for extreme speed.

#### 3.2.2 Use Cases
- Caching (session data, HTML fragments)
- Real‑time leaderboards
- Shopping carts
- Feature flags and rate limiting

#### 3.2.3 Tradeoffs
- Extremely fast, but only simple lookups by key
- No queries over values, no joins
- Data size limited by memory (for in‑memory stores)

#### 3.2.4 Examples (Redis, DynamoDB)
- **Redis:** In‑memory data structure store, supports strings, hashes, lists, sets, sorted sets. Used for caching, sessions, pub/sub.
- **DynamoDB:** Fully managed serverless key‑value and document database with single‑digit millisecond latency at any scale. Supports ACID transactions.

### 3.3 Document Stores

#### 3.3.1 Characteristics
Store data as JSON‑like documents. Flexible schema: fields can vary across documents. Support rich queries and secondary indexes.

#### 3.3.2 Use Cases
- Product catalogs (items with varying attributes)
- User profiles (optional fields)
- Content management systems
- Event logging

#### 3.3.3 Tradeoffs
- Flexibility and rich querying, but joins are limited
- Eventual consistency is common (though many offer strong consistency options)
- Document size limits (e.g., MongoDB 16 MB)

#### 3.3.4 Examples (MongoDB)
- **MongoDB:** Most popular document database. Supports sharding, replication, aggregations. Uses BSON format. ACID transactions within a single shard (and multi‑document transactions since 4.0).

### 3.4 Wide-Column Stores

#### 3.4.1 Characteristics
Data is organized into tables with rows and dynamic columns grouped into column families. Optimized for high write throughput and time‑series data.

#### 3.4.2 Use Cases
- Time‑series data (IoT, monitoring)
- Message history (chat applications)
- Activity tracking (clicks, logs)
- Recommendation engines

#### 3.4.3 Tradeoffs
- Extremely scalable writes, but complex data modeling
- Limited multi‑record ACID (Cassandra has lightweight transactions)
- Query patterns must align with the partition key

#### 3.4.4 Examples (Cassandra, HBase)
- **Cassandra:** Peer‑to‑peer, masterless, tunable consistency. Uses CQL (similar to SQL). Excellent for write‑heavy, always‑available workloads.
- **HBase:** Runs on Hadoop/HDFS. Strong consistency, good for batch processing and large‑scale analytics.

### 3.5 Graph Databases

#### 3.5.1 Characteristics
Data is stored as nodes (entities) and edges (relationships). Optimized for traversing relationships efficiently (index‑free adjacency).

#### 3.5.2 Use Cases
- Social networks (friend‑of‑friend queries)
- Fraud detection (complex transaction patterns)
- Recommendation engines
- Knowledge graphs

#### 3.5.3 Tradeoffs
- Extremely fast relationship traversals, but not ideal for large‑scale aggregation or simple key‑value lookups
- Sharding a graph is challenging; many graph databases scale vertically or use specialized techniques
- Smaller ecosystem compared to other NoSQL types

#### 3.5.4 Examples (Neo4j)
- **Neo4j:** ACID‑compliant, uses Cypher query language. Popular for social graphs and knowledge graphs.

### 3.6 BASE Properties

NoSQL's alternative to ACID, prioritizing availability and partition tolerance.

#### 3.6.1 Basically Available
The system guarantees availability – all requests receive a response, though that data may be stale or inconsistent during a partition.

#### 3.6.2 Soft State
The state of the system can change over time, even without input, due to eventual consistency updates.

#### 3.6.3 Eventual Consistency
Given enough time without new writes, all replicas will converge to the same value. Temporary inconsistencies are acceptable.

### 3.7 SQL vs NoSQL Decision Framework

#### 3.7.1 Consistency Requirements
If the application demands strong, immediate consistency (e.g., financial transactions), choose SQL. If eventual consistency is acceptable (e.g., social feeds), NoSQL may be a better fit.

#### 3.7.2 Query Patterns
- Complex ad‑hoc queries, joins, aggregations → SQL
- Simple point lookups, key‑based access, or predefined query patterns → NoSQL

#### 3.7.3 Scaling Requirements
- If you need to scale writes to millions per second and can partition by a known key → NoSQL (Cassandra, DynamoDB)
- If reads need to scale massively, SQL read replicas can serve many workloads

#### 3.7.4 Data Relationships
- Highly interconnected data (many‑to‑many relationships) → SQL or Graph
- Isolated documents or loosely coupled entities → Document store

#### 3.7.5 Flexibility Requirements
- Rapidly changing schema, agile development → Document database (MongoDB) or key‑value
- Stable, well‑defined schema with business logic enforced by constraints → SQL

## 4. Replication & High Availability

### 4.1 Replication Fundamentals

#### 4.1.1 Why Replication Matters
Replication provides copies of data across multiple nodes, improving availability, read throughput, and disaster recovery capabilities.

#### 4.1.2 High Availability
If a primary node fails, a replica can be promoted to take over, minimizing downtime.

#### 4.1.3 Read Scaling
Read requests can be distributed across multiple replicas, reducing load on the primary and improving query latency.

#### 4.1.4 Disaster Recovery
Geographically distributed replicas protect against data center outages. In case of a regional failure, data remains accessible from another location.

### 4.2 Leader-Follower Replication

*(Diagram: application writes to leader, reads from followers; replication from leader to followers)*

#### 4.2.1 Write Flow
All writes go to a designated leader (master). The leader appends the write to its log and then sends the changes to followers (slaves).

#### 4.2.2 Read Replicas
Followers can serve read‑only queries. The application typically directs reads to followers and writes to the leader.

#### 4.2.3 Replication Lag
In asynchronous replication, followers may lag behind the leader. Reads from a follower can return stale data.

Mitigation strategies:
- **Read‑your‑writes:** After a user performs a write, route their reads to the leader for a short period.
- **Monotonic reads:** Always read from the same follower to avoid time‑travel.
- **Leader reads for critical data:** Route sensitive operations to the leader.

#### 4.2.4 Failover Handling
If the leader fails, a follower must be promoted to leader. This requires detecting the failure, selecting a new leader, and reconfiguring clients. Systems like Patroni (PostgreSQL) or built‑in Raft consensus automate this.

### 4.3 Synchronous vs Asynchronous Replication

#### 4.3.1 Synchronous Replication
The leader waits for at least one follower to acknowledge the write before returning success to the client. This ensures zero data loss but increases write latency and can reduce availability if the follower is down.

#### 4.3.2 Asynchronous Replication
The leader commits immediately and sends changes to followers later. This provides low write latency and high availability, but there is a risk of data loss if the leader crashes before replication completes.

#### 4.3.3 Latency vs Durability Tradeoff
- Synchronous: higher latency, stronger durability
- Asynchronous: lower latency, potential data loss window
The choice depends on business requirements (e.g., banking vs social media).

### 4.4 Multi-Leader Replication

#### 4.4.1 Active-Active Writes
Multiple nodes accept writes, and each replicates its changes to the others. Suitable for multi‑data center deployments where low write latency is needed globally.

#### 4.4.2 Conflict Resolution
Conflicts arise when the same record is modified concurrently on two leaders. Resolution strategies include:
- Last write wins (LWW) – using timestamps (risk of data loss due to clock skew)
- Vector clocks – detect conflicts, let application merge
- CRDTs – automatically mergeable data structures
- Custom application logic

#### 4.4.3 Geo-Distributed Systems
Multi‑leader replication is often used in CDNs, collaborative editors, and global SaaS products to provide local write latency and comply with data residency laws.

### 4.5 Read Replicas

#### 4.5.1 Scaling Reads
Add read replicas in different availability zones or regions to distribute the read load. The primary handles writes, replicas handle reads.

#### 4.5.2 Read-After-Write Problems
A user writes data and immediately reads it back. If the read goes to a replica that hasn't yet received the update, the user sees stale data.

#### 4.5.3 Leader Reads
For operations that require immediate consistency, direct those reads to the leader. The rest of the traffic can go to replicas.

#### 4.5.4 Replica Routing
Middleware or the database driver can route queries based on the consistency requirements. Examples: RDS Proxy, ProxySQL for MySQL, or application‑level routing using a flag "read_from_leader".

## 5. Database Partitioning & Sharding

### 5.1 Partitioning Fundamentals

#### 5.1.1 Horizontal Partitioning (Sharding)
Splits rows of a table across multiple database servers. Each shard has the same schema but holds a subset of data. This is the primary method for scaling writes and large datasets.

#### 5.1.2 Vertical Partitioning
Splits a table by columns. Frequently accessed columns stay on fast storage; rarely accessed or large columns (BLOBs, JSON) are moved to a separate table or server.

#### 5.1.3 Functional Partitioning
Each microservice owns its own database. Data is partitioned by business domain (e.g., orders service has its own DB, users service another). Not a database‑level feature but an architectural pattern.

### 5.2 Sharding Fundamentals

#### 5.2.1 Why Sharding Is Needed
When a single database server cannot handle the write throughput or the dataset exceeds the storage capacity of one machine, sharding allows horizontal scaling across multiple machines.

#### 5.2.2 Shard Key Selection
The shard key determines how data is distributed. A good shard key:
- Has high cardinality (many distinct values)
- Ensures even distribution of writes and reads
- Is present in most queries (to avoid scatter‑gather)
- Is immutable or rarely changed

#### 5.2.3 Rebalancing
When shards are added or removed, data must be redistributed. This can cause performance degradation and downtime if not handled carefully. Consistent hashing minimizes data movement.

#### 5.2.4 Cross-Shard Queries
Queries that need data from multiple shards require scatter‑gather: query all shards and merge results in the application. This is slow and complex. Design the data model to avoid cross‑shard queries where possible.

#### 5.2.5 Hotspot Problems
If a shard key causes uneven distribution (e.g., celebrity users), one shard becomes overloaded. Mitigations include using a more granular shard key, splitting hot shards, or caching.

### 5.3 Sharding Strategies

#### 5.3.1 Range-Based Sharding
Data is partitioned by contiguous ranges of the shard key (e.g., user IDs 1‑1M, 1M‑2M). Supports range queries, but can cause hotspots if data is not evenly distributed.

#### 5.3.2 Hash-Based Sharding
A hash function is applied to the shard key, and the hash modulo the number of shards determines the shard. Provides even distribution but breaks range queries. Rebalancing is difficult because changing the number of shards requires rehashing almost all data.

#### 5.3.3 Consistent Hashing
A special hashing technique that uses a ring. Each node is assigned a range on the ring, and keys are mapped clockwise. When a node is added/removed, only a fraction of keys moves (~1/N). Used by Cassandra and DynamoDB.

#### 5.3.4 Directory-Based Sharding
A lookup table maps each key to its shard. Offers maximum flexibility and easy rebalancing, but the directory can become a bottleneck and single point of failure.

#### 5.3.5 Geographic Sharding
Data is sharded by physical location (e.g., continent, country). Reduces latency and helps with data residency laws, but cross‑region queries are slow.

### 5.4 Consistent Hashing

#### 5.4.1 Why Naive Hashing Fails
Using `hash(key) % N` causes nearly all keys to move when `N` changes (e.g., adding a node). This destroys cache hit rates and causes massive reshuffling.

#### 5.4.2 Hash Ring
Imagine a circle from 0 to 2³²–1. Each node is placed at one or more points on the ring via its hash. A key is assigned to the first node encountered moving clockwise from the key's hash position.

#### 5.4.3 Adding Nodes
When a new node is added, only the keys that map to the new node's range move. All other keys are unaffected. The fraction of keys that move is roughly 1/N (where N is new number of nodes).

#### 5.4.4 Removing Nodes
When a node is removed, its keys are inherited by the next clockwise node. Only those keys are reassigned.

#### 5.4.5 Virtual Nodes (vnodes)
To avoid uneven distribution when only a few physical nodes are present, each physical node is assigned many virtual nodes (e.g., 150‑256 vnodes) spread across the ring. This statistically evens out the load.

#### 5.4.6 Real-World Examples
- **Cassandra:** uses consistent hashing with 256 vnodes per node.
- **Redis Cluster:** uses hash slots (16384 slots), a variant that works similarly.
- **CDNs:** route requests to edge caches using consistent hashing to minimize re‑routing when edge servers are added/removed.

### 5.5 Sharding Challenges

#### 5.5.1 Data Rebalancing
Moving data between shards is expensive. Tools and databases like Vitess, Citus, and Cassandra provide mechanisms to automate rebalancing.

#### 5.5.2 Uneven Distribution
Some shard keys lead to skewed data. For example, sharding by location may cause one region to have much more data. Mitigate with hash of the key, or use composite keys.

#### 5.5.3 Cross-Shard Transactions
ACID transactions across shards are extremely difficult. Solutions include two‑phase commit (2PC) for critical cases, or designing the data model so that all data for a transaction lives on one shard.

#### 5.5.4 Secondary Index Challenges
Maintaining a global secondary index across shards is complex and often requires scatter‑gather or specialized distributed indexing (like Elasticsearch). Many NoSQL databases limit secondary index support.

## 6. Database Indexing

### 6.1 Why Indexes Matter

#### 6.1.1 Faster Reads
Indexes reduce the amount of data scanned for a query, often turning an O(n) scan into an O(log n) lookup.

#### 6.1.2 Query Optimization
The query optimizer uses indexes to choose efficient query plans, speeding up JOINs, WHERE clauses, and ORDER BY.

#### 6.1.3 Reduced Full Table Scans
Without indexes, databases must perform full table scans, which are prohibitively slow on large tables.

### 6.2 B-Tree Index

#### 6.2.1 Structure
A balanced tree where each node contains keys and pointers. Leaf nodes hold data or row IDs. Maintains sorted order, allowing efficient traversal.

#### 6.2.2 Range Queries
B‑Trees excel at range queries (`BETWEEN`, `>`, `<`) because leaf nodes are linked sequentially.

#### 6.2.3 Time Complexity
Search, insert, delete: O(log n). This is the default index type in most SQL databases.

#### 6.2.4 Real-World Usage
```sql
CREATE INDEX idx_users_email ON users(email);
```

### 6.3 Hash Index

#### 6.3.1 Structure
A hash table mapping keys to data locations. Provides O(1) lookups on average.

#### 6.3.2 Exact Match Queries
Excellent for equality comparisons (`WHERE email = 'alice@example.com'`). Not suitable for range queries.

#### 6.3.3 Limitations
- No range scans or sorting
- Hash collisions require handling
- Not durable in some engines (e.g., MySQL MEMORY)

### 6.4 LSM Trees

#### 6.4.1 Write Optimization
Log‑Structured Merge‑trees are designed for high write throughput. Writes are appended to an in‑memory memtable and periodically flushed to immutable SSTables on disk.

#### 6.4.2 SSTables
Sorted String Tables (SSTables) are immutable and sorted files. Multiple SSTables can exist; reads must check each.

#### 6.4.3 Compaction
Background compaction merges SSTables, removes deleted/overwritten data, and keeps read performance acceptable.

#### 6.4.4 Cassandra / RocksDB Usage
Cassandra and RocksDB use LSM trees. They are ideal for write‑heavy workloads like time‑series data, messaging, and logging.

### 6.5 Advanced Indexing

#### 6.5.1 Composite Index
An index on multiple columns (e.g., `(last_name, first_name)`). Good for queries that filter on the leading columns. Follows left‑prefix rule.

#### 6.5.2 Covering Index
An index that includes all columns needed by a query. The query can be answered entirely from the index, avoiding the table lookup.

#### 6.5.3 Secondary Index
An index on non‑primary key columns. In distributed databases, secondary indexes may be local (per shard) or global (across all shards), with performance tradeoffs.

#### 6.5.4 Full-Text Index
Specialized index for text search, supporting stemming, relevance ranking, and phrase queries. Examples: PostgreSQL GIN, MySQL FULLTEXT, Elasticsearch.

#### 6.5.5 Geospatial Index
Indexes for location data (lat/lon). B‑Tree can be extended with spatial data types (PostGIS). Redis has geospatial commands using Geohash.

### 6.6 Index Tradeoffs

#### 6.6.1 Faster Reads vs Slower Writes
Every index must be updated on INSERT/UPDATE/DELETE. More indexes improve read speed but reduce write throughput.

#### 6.6.2 Storage Overhead
Indexes consume additional disk space and memory (buffer cache). Over‑indexing can bloat storage.

#### 6.6.3 Index Maintenance Cost
Indexes need periodic maintenance (reindexing, updating statistics) to remain efficient.

## 7. Database Performance Optimization

### 7.1 Query Optimization

#### 7.1.1 Query Plans
Use `EXPLAIN` or `EXPLAIN ANALYZE` to see how the database executes a query. Identify full table scans, missing indexes, and inefficient joins.

#### 7.1.2 Avoiding N+1 Queries
Fetch related data in bulk using JOINs or `IN` clauses instead of looping through results and issuing separate queries for each row.

#### 7.1.3 Join Optimization
- Ensure join columns are indexed.
- Reduce the dataset before joining (filter early).
- Consider denormalization for performance‑critical paths.

#### 7.1.4 Pagination Strategies
- **Offset‑based:** `LIMIT offset, count` – becomes slow for large offsets.
- **Keyset pagination:** Use `WHERE id > last_seen_id ORDER BY id LIMIT n` – faster and stable.

### 7.2 Database Caching

#### 7.2.1 Query Cache
Some databases (MySQL) can cache the exact result of a query. Invalidation occurs when underlying tables change. Often deprecated in favor of external caching.

#### 7.2.2 Redis Cache
Store hot data in Redis to avoid hitting the database. Use write‑through, write‑behind, or cache‑aside patterns. Reduces database load dramatically.

#### 7.2.3 Write-Through Cache
Writes go to the cache first, then synchronously or asynchronously to the database. Ensures cache is always consistent.

#### 7.2.4 Cache Invalidation
Strategies: TTL (time‑to‑live), event‑driven invalidation (on database update), or write‑through updates. Avoid serving stale data unintentionally.

### 7.3 Denormalization

#### 7.3.1 Why Denormalize
To reduce the number of JOINs and improve read performance. Adds redundant data, trading write complexity for faster reads.

#### 7.3.2 Read Optimization
Store frequently accessed data together, e.g., embedding a user's name in an order record, so the order query does not need to join the users table.

#### 7.3.3 Duplication Tradeoffs
Increases storage and requires keeping duplicated data in sync. Suitable when reads vastly outnumber writes.

### 7.4 Materialized Views

#### 7.4.1 Precomputed Queries
A materialized view is a database object that stores the result of a query physically. It is like a cache that can be refreshed.

#### 7.4.2 Refresh Strategies
- **Complete refresh:** re‑compute entirely.
- **Incremental refresh:** only apply changes (if supported).
- **Scheduled:** periodic refresh (e.g., nightly).

#### 7.4.3 Reporting Systems
Materialized views are excellent for dashboards and reports that aggregate large datasets and tolerate slightly stale data.

### 7.5 Connection Pooling

#### 7.5.1 Why Pool Connections
Establishing a database connection (TCP handshake, authentication) is expensive. A connection pool reuses a set of connections across requests.

#### 7.5.2 Pool Sizing
Pool size should be tuned to the number of concurrent requests and the database's connection limits. Too large can overwhelm the database; too small causes queuing.

#### 7.5.3 Exhaustion Problems
If all connections in the pool are busy, new requests wait or timeout. Monitor pool usage and set appropriate timeouts.

## 8. Scaling Databases

### 8.1 Vertical Scaling

#### 8.1.1 More CPU / RAM
Add more power to the existing database server (scale‑up). Simple and effective until hardware limits are reached.

#### 8.1.2 Hardware Limits
There is a maximum CPU core count, RAM capacity, and disk throughput. Cloud instances have upper limits (e.g., 24 TB RAM for the largest).

#### 8.1.3 Simplicity Benefits
No application changes needed; no distributed complexity. Works well for moderate, predictable growth.

### 8.2 Horizontal Scaling

#### 8.2.1 Read Scaling
Add read replicas to distribute read traffic. Works well for read‑heavy workloads (e.g., product catalog, social feed).

#### 8.2.2 Write Scaling
Shard the database to distribute writes across multiple servers. Each shard handles a subset of the data, increasing total write capacity.

#### 8.2.3 Distributed Challenges
Horizontal scaling introduces complexities: data partitioning, cross‑shard queries, distributed transactions, and rebalancing.

### 8.3 Read Replicas vs Sharding

#### 8.3.1 When to Use Read Replicas
When the primary bottleneck is read throughput and the dataset fits on a single machine. Replicas are simpler to set up and manage.

#### 8.3.2 When to Use Sharding
When write throughput or data volume exceeds a single server's capacity, or when you need to isolate tenants for performance or compliance.

#### 8.3.3 Combining Both
A common pattern: shard the database for write scaling, and add read replicas for each shard to further boost read performance.

## 9. Database Patterns by System Type

### 9.1 Summary: Database Techniques by Pattern

| Pattern | System Type | Primary Database | Scaling Strategy | Replication | Why |
|---------|-------------|------------------|------------------|-------------|-----|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | **Key‑value / NoSQL** (DynamoDB, Cassandra) for lookups; **SQL** for inventory/booking | Sharding by key (short code, user_id, event_id); read replicas for catalog | Asynchronous (read replicas) for queries; synchronous for critical writes | High read/write volume; simple key‑value lookups; strong consistency needed only for inventory |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | **Cassandra / DynamoDB** for tweets/posts; **Redis** for timeline cache | Sharding by user_id; time‑series partitioning | Asynchronous replication; eventual consistency | Write‑heavy (tweets); need high availability and linear scalability |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | **Cassandra** for message history; **Redis** for presence; **PostgreSQL** (sharded) for channels | Sharding by conversation/channel_id; time‑based partitioning for old messages | Asynchronous; read replicas for history | Messages are append‑only; presence is ephemeral; channel history is immutable |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | **Elasticsearch / Solr** (inverted index); **Redis** for caching; **Bigtable / Cassandra** for metadata | Sharding by term/document id; time‑based index rollover | Index replication (primary/backup) | Index is too large for single node; need parallel query execution |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | **PostgreSQL + PostGIS** for place data; **Redis Geo** for real‑time locations; **Cassandra** for trip history | Geohash sharding; spatial partitioning (quadtree) | Asynchronous for location updates; synchronous for ride assignment | Geospatial queries require spatial indexes; real‑time needs in‑memory Redis |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | **PostgreSQL** (or **CockroachDB**) with strong consistency; **Redis** for idempotency cache | Sharding by merchant_id / user_id; optimistic locking | Synchronous replication (Raft / Paxos) | Strong consistency and ACID are mandatory; financial transactions cannot lose data |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | **Kafka** (log), **BigQuery / Druid** (analytics), **Elasticsearch** (logs), **Prometheus TSDB** (metrics) | Partitioning by time / customer_id; columnar storage | Replication for durability; query layer replication | Append‑only, time‑series data; high write throughput; analytical queries |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | **Redis** (presence, session); **etcd / ZooKeeper** (locks); **Cassandra** (document state); **Bloom filter** (dedup) | Sharding by document_id; consistent hashing | Raft consensus for locks; asynchronous for document state | CRDTs avoid database coordination; locks need consensus; crawler needs fast dedup |

---

### 9.2 Pattern A – CRUD & Storage Systems (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **URL Shortener** | **DynamoDB** or **Cassandra** for short code → long URL; **Redis** for cache | Key‑value lookup is perfect; high read throughput (100:1 read/write). | DynamoDB with `short_code` as partition key. Enable DAX (cache) or Redis. For custom aliases, use conditional writes. |
| **Pastebin** | **S3** (object storage) for paste content; **DynamoDB** for metadata (paste_id, user, expiry) | Paste content is large and immutable; metadata is small and queryable. | S3 key = `paste_id`. DynamoDB table with `paste_id` PK, TTL for expiry. Background worker deletes from S3 on expiry. |
| **Online Bookstore** | **PostgreSQL** for inventory (strong consistency); **Elasticsearch** for product search; **Redis** for catalog cache | Inventory needs ACID; search needs full‑text; catalog can be cached. | Inventory: `product_id` PK, `quantity` with row‑level locking. Search: Elasticsearch index. Catalog: Redis cache with TTL 5 min. |
| **Ticket Booking** | **PostgreSQL** (sharded by `event_id`) or **CockroachDB**; **Redis** for seat lock cache | Seat reservation needs strong consistency per event. Sharding reduces contention. | Shard by `event_id`. Use `SELECT FOR UPDATE` or optimistic locking (`version` column). Redis for short‑term locks (hold seat for 5 min). |
| **Dropbox / Google Drive** | **Cassandra** for metadata (file tree); **S3** for file chunks; **Redis** for user session | Metadata is write‑heavy (rename, move). Cassandra scales linearly. File chunks are immutable. | Cassandra table: `(user_id, file_path)` → file_id. S3 chunks with content‑addressed storage. Redis for session tokens. |

---

### 9.3 Pattern B – Feed / Timeline Systems (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **Twitter / X** | **Cassandra** for tweets (sharded by `user_id`); **Redis** for timeline cache; **Kafka** for ingestion | Tweets are time‑ordered per user. Cassandra handles high write throughput. | Tweets table: `(user_id, tweet_id, timestamp)` with clustering order DESC. Timeline cache: Redis sorted set per user. |
| **Instagram Feed** | **Cassandra** for posts; **S3** for images; **Redis** for feed cache | Similar to Twitter, but media stored separately. | Posts table: `(user_id, post_id, timestamp)`. Images in S3 with CDN. Feed cache: Redis sorted set (post IDs). |
| **Facebook News Feed** | **Cassandra** / **HBase** for posts; **Memcached** for feed cache; **Graph database** for social graph | News feed requires ranking and social graph traversal. | Posts stored in time‑ordered tables. Social graph in **TAO** (Facebook’s custom graph DB) or Neo4j. Pre‑computed feed stored in Memcached. |
| **LinkedIn Feed** | **Cassandra** for posts; **Redis** for profile cache; **Kafka** for activity stream | Lower volume than Twitter; pull model uses profile cache. | Posts: `(user_id, post_id, timestamp)`. On read, query last N posts from followed users (capped). Cache profiles in Redis. |

---

### 9.4 Pattern C – Real-Time Systems (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | **Cassandra** for message history; **Redis** for presence; **Kafka** for message delivery | Messages are append‑only; presence is ephemeral; history needs to be durable and scalable. | Messages table: `(conversation_id, message_id, timestamp)` with TTL (e.g., 30 days). Redis: `presence:{user_id}` with TTL 30s. |
| **Slack / Discord** | **PostgreSQL** (sharded by `channel_id`) for channel history; **Redis** for presence and unread counts | Channel history needs strong ordering and consistency. Sharding isolates channels. | Shard by `channel_id` across many PostgreSQL instances. Use serial primary key for message order. Redis hash for unread counts per user per channel. |
| **Live Comments (YouTube Live)** | **Redis** sorted set for recent comments; **Kafka** for ingestion; **S3** for archival | Comments are high velocity; only recent comments need fast access. | Redis sorted set per video: score = timestamp. Old comments moved to S3 via background job. Kafka partitions by `video_id`. |
| **Multiplayer Game Backend** | **Redis** for game state (in‑memory); **DynamoDB** for periodic checkpoints; **Leaderboards** via Redis sorted sets | Game state needs microsecond latency. Checkpoints provide durability. | Redis: store game state as hash. Every 5s, checkpoint to DynamoDB. Leaderboard: `ZADD` player scores. |

---

### 9.5 Pattern D – Search Systems (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **Google Search** | **Bigtable** / **Cassandra** for inverted index; **Elasticsearch** for serving; **PageRank** stored separately | Inverted index is huge; sharding by term allows parallel query. | Index shards: each shard stores a subset of terms. Query router fans out to all shards, merges results. PageRank stored in a separate column family. |
| **YouTube Search** | **Elasticsearch** for video metadata; **Redis** for cache; **Bigtable** for index | Video metadata (title, description, tags) is indexed. | Elasticsearch index partitioned by date (daily). Redis cache for popular queries. Bigtable for large‑scale index (if > petabytes). |
| **Amazon Product Search** | **Elasticsearch** for product search; **Redis** for inventory cache; **DynamoDB** for product details | Search needs full‑text and faceting. Inventory needs real‑time updates. | Elasticsearch index with product fields. Redis cache: `inventory:{product_id}` updated synchronously on purchase. Search results enriched with inventory from cache. |
| **Search Autocomplete** | **Redis** for trie (sorted set or custom module); **CDN** for static suggestions; **Kafka** for streaming updates | Trie must be low‑latency and updated asynchronously. | Build trie from query logs every hour, push to CDN as JSON. For real‑time, use Redis with prefix search (`ZRANK` + `ZRANGE`). |

---

### 9.6 Pattern E – Geospatial & Location-Based Systems (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **Uber / Lyft** | **Redis Geo** for driver locations; **PostgreSQL + PostGIS** for trip history; **Cassandra** for ride requests | Driver locations need real‑time (Redis). Trip history is append‑only. | Redis Geo: `GEOADD drivers:city` with lon/lat. `GEORADIUS` to find nearby drivers. Trip history in PostgreSQL with PostGIS (spatial indexing). |
| **Google Maps / Nearby Places** | **PostgreSQL + PostGIS** for place data; **Redis** for cache; **S3** for map tiles | Places are read‑heavy; map tiles are static. | Place table with `geography` column, GiST index. Queries use `ST_DWithin`. Map tiles pre‑generated and stored in S3 + CDN. |
| **Food Delivery (DoorDash)** | **Redis Geo** for drivers; **PostgreSQL** for restaurants; **Elasticsearch** for restaurant search | Similar to Uber, plus restaurant search. | Driver location: same as Uber. Restaurant DB with geospatial index. Elasticsearch for text search on restaurant names/cuisines. |
| **Nearby Friends** | **Redis Geo** for user locations; **WebSocket** for real‑time pushes; **Cassandra** for location history | Real‑time location sharing needs Redis. History is optional. | `GEOADD` user locations. Clients subscribe to location updates via WebSocket. History stored in Cassandra with TTL (e.g., 24 hours). |

---

### 9.7 Pattern F – Financial & Critical Consistency Systems (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | **PostgreSQL** with synchronous replication; **Redis** for idempotency cache; **Ledger DB** (immutable) | ACID is mandatory. Idempotency keys need fast lookups. | Payments table with `idempotency_key` unique constraint. Ledger as append‑only table. Redis for hot idempotency cache (TTL 24h). |
| **Wallet System** | **PostgreSQL** or **CockroachDB** for balance; **Redis** for balance cache; **Kafka** for transaction log | Balance updates must be atomic and consistent. | Balance table with `version` column for optimistic locking. Transaction log in Kafka for audit. Redis cache with CAS (compare‑and‑swap). |
| **UPI-like System** | **CockroachDB** (multi‑region, strong consistency) or **Spanner**; **Kafka** for reconciliation | Cross‑bank transactions need distributed ACID or saga. | CockroachDB with `Follower Reads` for low latency. Saga state stored in database. Kafka for compensating transactions. |
| **Distributed Job Scheduler** | **etcd** for leader election and job leases; **PostgreSQL** for job definitions; **Redis** for job queue | etcd provides strong consistency for leases. Job definitions are persistent. | etcd: store job leases with TTL. PostgreSQL: job metadata. Redis: job queue (list or sorted set by schedule time). |

---

### 9.8 Pattern G – Large-Scale Processing & Infrastructure (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **Google Analytics** | **BigQuery** (columnar storage) for analytics; **Kafka** for ingestion; **Redis** for real‑time dashboards | Columnar storage is efficient for aggregations. BigQuery scales to petabytes. | Events → Kafka → Stream processor → BigQuery partitioned by date. Real‑time counts in Redis. |
| **Clickstream Processing** | **Kafka** for raw events; **Flink** state in RocksDB; **Druid** for OLAP; **Redis** for real‑time | Sessionisation requires stateful stream processing. | Flink keyed by `user_id` with session window. Output to Druid for sub‑second queries. Redis for current session counts. |
| **Distributed Logging (ELK)** | **Elasticsearch** for indexing; **Kafka** buffer; **S3** for cold storage | Elasticsearch is optimised for log search. Time‑based partitioning. | Logs → Kafka → Logstash → Elasticsearch index per day (e.g., `logs-2025-01-01`). Hot nodes (SSD) for recent 7 days, warm nodes for older. |
| **Metrics Monitoring (Prometheus)** | **Prometheus TSDB** (local); **Thanos** or **VictoriaMetrics** for long‑term; **S3** for archival | Prometheus is optimised for time‑series metrics. Local storage is fast. | Prometheus scrapes metrics every 15s, stores in local TSDB. Thanos sidecar uploads to S3. Queries federate across Prometheus and S3. |
| **Design Kafka-like System** | **Raft** for metadata (brokers, partitions); **segmented log** on disk; **in‑memory index** for offsets | Kafka’s core is a replicated log. Metadata needs consensus. | Each partition is a directory of segments (`.log`, `.index`). Leader handles writes, followers replicate. Metadata in Raft (e.g., etcd). |

---

### 9.9 Pattern H – Collaborative & Infrastructure Utilities (Database Focus)

| Question | Database Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|-------------------|----------------------------|
| **Google Docs** | **Redis** for document state (CRDT); **Cassandra** for document history; **WebSocket** for real‑time sync | CRDT state can be stored in memory (Redis). History is immutable. | Redis: document state as CRDT (e.g., Yjs). Cassandra: document history as append‑only log of operations. |
| **Figma / Collaborative Whiteboard** | **Redis** for object state; **S3** for assets; **CRDT** library (yjs or automerge) | Similar to Google Docs, but with more complex objects (vectors). | Redis hash per object ID. CRDT operations broadcast via WebSocket. Assets (images) stored in S3. |
| **Distributed Locking Service** | **etcd** / **ZooKeeper** (Raft / Zab); **Redis Redlock** (optional) | etcd provides linearizable locks with TTL and fencing tokens. | etcd: `clientv3.NewSession()` with TTL. Lock = `mutex.Lock()`. Fencing token stored with lock. |
| **Unique ID Generator (Snowflake)** | **etcd** for worker ID assignment; **no database for ID generation** | Worker IDs are assigned via consensus. ID generation is local. | etcd: `atomic increment` on `/worker_ids/`. Each worker generates IDs: `timestamp << 22 | worker_id << 12 | sequence`. |
| **Web Crawler** | **Redis** for URL frontier (sorted set); **Bloom filter** (Redis module or custom) for dedup; **S3** for crawled content | Frontier needs fast priority queue. Bloom filter saves memory. | Frontier: `ZADD` with priority. Workers `ZPOPMIN`. Bloom filter: `BF.ADD` to check visited URLs. Crawled content in S3. |

---

## 10. Interview-Oriented HLD Notes

### 10.1 Key Database Tradeoffs

#### 10.1.1 SQL vs NoSQL
SQL: strong consistency, complex queries, stable schema. NoSQL: scale, flexibility, specialized access patterns.

#### 10.1.2 Consistency vs Availability
In distributed databases, choose CP (e.g., PostgreSQL synchronous) or AP (e.g., Cassandra) based on whether correctness or uptime is more critical.

#### 10.1.3 Reads vs Writes
Read‑heavy → cache + read replicas. Write‑heavy → sharding + write‑optimized engines (LSM).

#### 10.1.4 Latency vs Durability
Synchronous replication = higher latency, higher durability. Asynchronous = lower latency, potential data loss.

### 10.2 Common Interview Questions

#### 10.2.1 Choosing a Database
Be prepared to justify the database type based on data model, consistency, scale, and query patterns.

#### 10.2.2 Designing for Scale
Explain when to introduce read replicas, caching, and sharding.

#### 10.2.3 Handling Hotspots
Describe how shard key selection, consistent hashing, and caching prevent hotspots.

#### 10.2.4 Multi-Region Challenges
Discuss replication lag, conflict resolution, and geographic sharding.

### 10.3 Quick Revision Material

#### 10.3.1 Decision Frameworks
- ACID needed? → SQL
- Massive writes + simple access? → Cassandra
- Flexible schema + rich queries? → MongoDB
- Low‑latency caching? → Redis
- Relationships traversal? → Neo4j

#### 10.3.2 Architecture Cheat Sheet
| System Type | Primary DB | Secondary DB / Cache |
|------------|------------|---------------------|
| E‑commerce | PostgreSQL (orders) | Elasticsearch, Redis |
| Social Feed | Cassandra | Redis, CDN |
| Messaging | Cassandra (history), Redis (presence) | Kafka |
| Search | Elasticsearch | Redis |
| Ride Sharing | Redis Geo (real‑time), PostgreSQL (history) | Kafka |

#### 10.3.3 Key Interview Sentences
> *"For the payment ledger, I'd use PostgreSQL with synchronous replication and strong consistency – ACID is non‑negotiable."*

> *"The feed is write‑heavy and needs massive scale; I'd use Cassandra sharded by user_id with clustering by timestamp to serve timeline reads efficiently."*

> *"Session data is purely key‑value with TTL; Redis is perfect – sub‑millisecond latency and automatic expiry."*

#### 10.3.4 Self-Test Questions
1. When would you choose a wide‑column store over a document store?
2. How do you avoid replication lag problems in a read‑your‑writes scenario?
3. What's the difference between range‑based and hash‑based sharding? What are their tradeoffs?
4. Why does consistent hashing use virtual nodes?
5. Design the database layer for a ride‑sharing app: which DB for driver locations, trip history, and user accounts?

*End of consolidated notes.*
