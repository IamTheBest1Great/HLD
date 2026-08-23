Here is the content organized by sections and subsections, with timestamps removed.

---

### INTRODUCTION & REQUIREMENTS
- Welcome & Course Overview
- Why This Framework Works
- Interview Framework: Start with Requirements
- Functional Requirements
- Non-Functional Requirements
- Scale Requirements (500M DAU)
- Out-of-Scope Features
- Requirements Summary

### CAPACITY ESTIMATION & ARCHITECTURE
- Why Capacity Estimation Matters
- Traffic Calculations (QPS)
- Read Traffic: 100K QPS
- Write Traffic: 1K QPS
- Read:Write Ratio (100:1)
- Storage Estimation (73 PB/Year)
- Bandwidth Estimation (231 GB/sec)
- High-Level Architecture Overview
- Microservices Architecture
- Data Flow Walkthrough
- Cache Hit vs Cache Miss Paths
- Architecture Summary

### DATABASE DESIGN
- Database Design Overview
- Users Table Schema & Indexes
- Posts Table Schema
- Follows Table (Social Graph)
- Likes & Comments Tables
- SQL vs NoSQL Decision
- Database Sharding (User ID, 1000 Shards)
- Cross-Shard Queries
- Resharding Strategies

### API DESIGN
- API Design Principles (RESTful)
- User Management APIs
- Post APIs
- Feed API
- Social Interaction APIs
- Cursor-Based Pagination
- JWT Authentication & Rate Limiting
- Error Handling

### FEED GENERATION ALGORITHM
- The Feed Generation Challenge
- Why Naive Approaches Fail
- Fan-Out on Write (Push Model)
- Fan-Out on Read (Pull Model)
- Hybrid Approach (Instagram's Solution)
- Celebrity Problem (10K Follower Threshold)
- Ranking Algorithm
- Complete Feed Generation Flow
- Latency Breakdown & Optimization

### MEDIA STORAGE & CDN
- Media Storage Challenge (73 PB/Year)
- Why Database Storage Fails
- Object Storage Solution (S3/Azure Blob)
- Storage Hierarchy & Organization
- Async Image Processing Pipeline
- Thumbnail Generation (3 Sizes)
- CDN Strategy (80% Cache Hit)
- Lifecycle Policies (Hot → Warm → Cold)
- Cost Optimization

### SCALING PATTERNS
- Scaling Overview
- Application Tier: Stateless Servers
- Load Balancing & Health Checks
- Auto-Scaling Configuration
- Database Tier Scaling
- Cache Tier: Redis Clustering
- Async Processing with Queues
- Rate Limiting (Token Bucket)
- Sliding Window Algorithm

### TRADE-OFFS & COST ANALYSIS
- Key Trade-Offs Overview
- Consistency vs Availability (CAP)
- SQL vs NoSQL Trade-Offs
- Fan-Out Strategies Comparison
- Sync vs Async Processing
- Denormalization for Performance
- Cost Analysis (~$57M/Month)
- Cost Optimization Strategies
- Infrastructure Savings ($40-50M/Year)
