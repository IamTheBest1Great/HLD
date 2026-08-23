In any HLD interview, beyond **scalability** and **availability**, you should explicitly define and address the following **non-functional requirements** (often called "system qualities"):

| **Aspect** | **What It Means** | **Typical Target / Example** |
|------------|------------------|------------------------------|
| **Low Latency** | Time to serve a request (e.g., redirect). | < 50 ms for reads; < 200 ms for writes. |
| **High Availability** | System uptime despite failures. | 99.99% (four nines) or 99.999% (five nines) availability. |
| **Scalability** | Ability to handle growth in traffic / data. | Horizontal scaling; support millions of new URLs and billions of redirects. |
| **Durability** | Data persistence without loss. | No loss of stored mappings; replication and backups. |
| **Security** | Protection against abuse, spam, malicious URLs. | Rate limiting, malicious URL detection, authentication. |
| **Consistency** | Data correctness across replicas (if using distributed databases). | Often eventual consistency is acceptable for reads, but strong consistency may be needed for unique short code generation. |
| **Fault Tolerance** | System continues operating when components fail. | Redundancy, failover, graceful degradation. |
| **Reliability** | System performs its intended function correctly over time. | Mean time between failures (MTBF) high; minimal errors. |
| **Maintainability** | Ease of making changes, adding features, fixing bugs. | Modular architecture, clear APIs, monitoring, logging. |
| **Cost Efficiency** | Optimizing resource usage (compute, storage, network) to keep costs reasonable. | Use caching to reduce DB load; auto-scaling to match demand. |

These qualities guide every architectural decision—database choice, caching strategy, load balancing, replication, and failure handling. Always **explicitly state** your targets for each relevant aspect early in the interview (usually in the "Non-Functional Requirements" section). This shows you think like a senior engineer.

That is a fantastic study list! To answer your question directly: **almost every single topic on that list is asked in High-Level Design interviews**, but they are tested in different ways. 

In an HLD interview, interviewers rarely ask you to define these terms in isolation (e.g., "Define the CAP theorem"). Instead, they test your ability to apply these concepts to solve a specific problem. 

Here is how the topics from your list break down in terms of interview priority and how they are typically used:

### 1. The Core Architectural Trade-offs (Top Priority)
These are the concepts you use to justify *why* you are designing the system a certain way. Interviewers will actively push you on these.
* **04. CAP theorem and consistency** & **20. Consistency-2:** You'll need to choose between strong consistency (e.g., banking apps) and eventual consistency (e.g., social media likes).
* **03. Scaling Concepts** & **16. Scalability-2:** The core of the interview. You must know when to scale horizontally vs. vertically.
* **17. Availability:** How do you ensure the system stays up (e.g., deploying across multiple Availability Zones)?
* **14. Estimation Skills:** Usually the very first thing you do (Back-of-the-envelope math). You need this to estimate QPS (Queries Per Second), storage, and bandwidth to determine the scale of the system.

### 2. The Building Blocks (Top Priority)
These are the actual components you will draw on the whiteboard. You need to know when to use which tool.
* **05. Database SQL, 06. Database NoSQL, 07. Database deep dive:** You will almost always have to choose a database and justify it (e.g., Postgres vs. Cassandra vs. MongoDB).
* **08. Caching fundamentals & 09. Distributed Caching:** Critical for reducing latency and database load (e.g., using Redis or Memcached).
* **10. Messaging and Queues:** Essential for decoupling services and handling asynchronous background tasks (e.g., Kafka, RabbitMQ).
* **13. API design:** Often asked early on. You'll need to design the endpoints clients will use to interact with your system.
* **11. Storage System:** Knowing when to use block storage vs. object storage (like AWS S3 for images/videos).

### 3. System Characteristics (The "Ilities")
These are often follow-up questions during the "Deep Dive" phase of the interview. The interviewer will try to break your system, and you use these concepts to fix it.
* **12. Reliability patterns, 22. Reliability, 21. Fault Tolerance:** "What happens if this server crashes? What if the database goes down?" (You counter with load balancers, replicas, and retries).
* **15. Reduce latency:** "How can we make this load faster for users in India vs. the US?" (You counter with CDNs and Edge caching).
* **18. Durability:** "How do we ensure we never lose a processed order?" 

### 4. Foundational Knowledge (Lower Priority for HLD)
These are fundamental to being a good software engineer, but they are rarely the main focus of a standard HLD round.
* **01. Network Fundamentals, 01. OSI model, 02. Advanced networking:** You might briefly touch on L4 vs L7 load balancing, or TCP vs. UDP (for video streaming), but you generally won't be grilled on the OSI layers.
* **19. Security:** Unless the prompt is specifically about a highly secure system, security is usually assumed or covered very briefly (e.g., "We'll use HTTPS and standard OAuth"). 

---

### Ready for the Mock Interview?

Since you've got a great grasp of the curriculum, let's jump right into the mock interview we were setting up! 

**Here is your prompt:**
> **"Design a highly scalable, photo-centric social media platform where users can upload photos, follow other users, and view a personalized feed of photos from people they follow."**

To kick us off, what are the **Functional** and **Non-Functional** requirements you want to establish for this system?
