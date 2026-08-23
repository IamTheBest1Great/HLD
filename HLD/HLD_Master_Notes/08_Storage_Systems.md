# Storage Systems — Consolidated HLD Notes

## Table of Contents

- [1. Storage System Fundamentals](#1-storage-system-fundamentals)
  - [1.1 Why Storage Systems Matter](#11-why-storage-systems-matter)
    - [1.1.1 Durability](#111-durability)
    - [1.1.2 Scalability](#112-scalability)
    - [1.1.3 Availability](#113-availability)
    - [1.1.4 Low Latency Access](#114-low-latency-access)
    - [1.1.5 Cost Efficiency](#115-cost-efficiency)
  - [1.2 Core Storage Concepts](#12-core-storage-concepts)
    - [1.2.1 Persistence](#121-persistence)
    - [1.2.2 Replication](#122-replication)
    - [1.2.3 Redundancy](#123-redundancy)
    - [1.2.4 Compression](#124-compression)
    - [1.2.5 Encryption](#125-encryption)
    - [1.2.6 Tiered Storage](#126-tiered-storage)
  - [1.3 Types of Storage Systems](#13-types-of-storage-systems)
    - [1.3.1 Block Storage](#131-block-storage)
    - [1.3.2 File Storage](#132-file-storage)
    - [1.3.3 Object Storage](#133-object-storage)
    - [1.3.4 Blob Storage](#134-blob-storage)
- [2. Block Storage](#2-block-storage)
  - [2.1 What Is Block Storage?](#21-what-is-block-storage)
    - [2.1.1 Fixed-Size Blocks](#211-fixed-size-blocks)
    - [2.1.2 Raw Storage Volumes](#212-raw-storage-volumes)
    - [2.1.3 Low-Level Disk Access](#213-low-level-disk-access)
  - [2.2 How Block Storage Works](#22-how-block-storage-works)
    - [2.2.1 Block Addressing](#221-block-addressing)
    - [2.2.2 File System Layer](#222-file-system-layer)
    - [2.2.3 Mounting Volumes](#223-mounting-volumes)
  - [2.3 Advantages of Block Storage](#23-advantages-of-block-storage)
    - [2.3.1 Low Latency](#231-low-latency)
    - [2.3.2 High Performance](#232-high-performance)
    - [2.3.3 Database Optimization](#233-database-optimization)
  - [2.4 Limitations of Block Storage](#24-limitations-of-block-storage)
    - [2.4.1 Scalability Challenges](#241-scalability-challenges)
    - [2.4.2 Management Complexity](#242-management-complexity)
    - [2.4.3 Cost](#243-cost)
  - [2.5 Block Storage Use Cases](#25-block-storage-use-cases)
    - [2.5.1 Databases](#251-databases)
    - [2.5.2 VM Disks](#252-vm-disks)
    - [2.5.3 High-Performance Applications](#253-high-performance-applications)
  - [2.6 Real-World Examples](#26-real-world-examples)
    - [2.6.1 AWS EBS](#261-aws-ebs)
    - [2.6.2 SAN Storage](#262-san-storage)
    - [2.6.3 Azure Managed Disks](#263-azure-managed-disks)
- [3. File Storage](#3-file-storage)
  - [3.1 What Is File Storage?](#31-what-is-file-storage)
    - [3.1.1 Hierarchical File Structure](#311-hierarchical-file-structure)
    - [3.1.2 Shared File Systems](#312-shared-file-systems)
    - [3.1.3 Directory-Based Organization](#313-directory-based-organization)
  - [3.2 How File Storage Works](#32-how-file-storage-works)
    - [3.2.1 File Paths](#321-file-paths)
    - [3.2.2 Network File Access](#322-network-file-access)
    - [3.2.3 Metadata Management](#323-metadata-management)
  - [3.3 Advantages of File Storage](#33-advantages-of-file-storage)
    - [3.3.1 Human Readability](#331-human-readability)
    - [3.3.2 Shared Access](#332-shared-access)
    - [3.3.3 Simplicity](#333-simplicity)
  - [3.4 Limitations of File Storage](#34-limitations-of-file-storage)
    - [3.4.1 Scaling Limits](#341-scaling-limits)
    - [3.4.2 Metadata Bottlenecks](#342-metadata-bottlenecks)
    - [3.4.3 Performance Constraints](#343-performance-constraints)
  - [3.5 File Storage Use Cases](#35-file-storage-use-cases)
    - [3.5.1 Shared Drives](#351-shared-drives)
    - [3.5.2 Team Collaboration](#352-team-collaboration)
    - [3.5.3 Enterprise File Servers](#353-enterprise-file-servers)
  - [3.6 Network File Systems](#36-network-file-systems)
    - [3.6.1 NFS](#361-nfs)
    - [3.6.2 SMB](#362-smb)
    - [3.6.3 Distributed File Systems](#363-distributed-file-systems)
  - [3.7 Real-World Examples](#37-real-world-examples)
    - [3.7.1 AWS EFS](#371-aws-efs)
    - [3.7.2 Google Filestore](#372-google-filestore)
    - [3.7.3 NAS Systems](#373-nas-systems)
- [4. Object & Blob Storage](#4-object--blob-storage)
  - [4.1 What Is Object Storage?](#41-what-is-object-storage)
    - [4.1.1 Objects & Metadata](#411-objects--metadata)
    - [4.1.2 Flat Namespace](#412-flat-namespace)
    - [4.1.3 Unique Object IDs](#413-unique-object-ids)
  - [4.2 How Object Storage Works](#42-how-object-storage-works)
    - [4.2.1 HTTP-Based Access](#421-http-based-access)
    - [4.2.2 Distributed Storage Nodes](#422-distributed-storage-nodes)
    - [4.2.3 Metadata Indexing](#423-metadata-indexing)
  - [4.3 Blob Storage Clarification](#43-blob-storage-clarification)
    - [4.3.1 Binary Large Objects](#431-binary-large-objects)
    - [4.3.2 Blob vs Object Storage](#432-blob-vs-object-storage)
    - [4.3.3 Cloud Blob Storage Services](#433-cloud-blob-storage-services)
  - [4.4 Advantages of Object Storage](#44-advantages-of-object-storage)
    - [4.4.1 Massive Scalability](#441-massive-scalability)
    - [4.4.2 High Durability](#442-high-durability)
    - [4.4.3 Cost Efficiency](#443-cost-efficiency)
    - [4.4.4 Global Distribution](#444-global-distribution)
  - [4.5 Limitations of Object Storage](#45-limitations-of-object-storage)
    - [4.5.1 Higher Latency](#451-higher-latency)
    - [4.5.2 No Random Disk Access](#452-no-random-disk-access)
    - [4.5.3 Eventual Consistency Tradeoffs](#453-eventual-consistency-tradeoffs)
  - [4.6 Object Storage Use Cases](#46-object-storage-use-cases)
    - [4.6.1 Media Storage](#461-media-storage)
    - [4.6.2 Backups & Archives](#462-backups--archives)
    - [4.6.3 Logs & Analytics](#463-logs--analytics)
    - [4.6.4 Static Website Hosting](#464-static-website-hosting)
  - [4.7 Real-World Examples](#47-real-world-examples)
    - [4.7.1 Amazon S3](#471-amazon-s3)
    - [4.7.2 Google Cloud Storage](#472-google-cloud-storage)
    - [4.7.3 Azure Blob Storage](#473-azure-blob-storage)
    - [4.7.4 MinIO](#474-minio)
- [5. CDN & Media Delivery](#5-cdn--media-delivery)
  - [5.1 Content Delivery Network (CDN)](#51-content-delivery-network-cdn)
    - [5.1.1 What Is a CDN?](#511-what-is-a-cdn)
    - [5.1.2 Edge Servers](#512-edge-servers)
    - [5.1.3 Origin Servers](#513-origin-servers)
    - [5.1.4 Global Caching](#514-global-caching)
  - [5.2 How CDN Works](#52-how-cdn-works)
    - [5.2.1 Request Routing](#521-request-routing)
    - [5.2.2 Edge Caching](#522-edge-caching)
    - [5.2.3 Cache Miss Flow](#523-cache-miss-flow)
  - [5.3 CDN Models](#53-cdn-models)
    - [5.3.1 Pull CDN](#531-pull-cdn)
    - [5.3.2 Push CDN](#532-push-cdn)
    - [5.3.3 Hybrid CDN](#533-hybrid-cdn)
  - [5.4 CDN Challenges](#54-cdn-challenges)
    - [5.4.1 Cache Invalidation](#541-cache-invalidation)
    - [5.4.2 Stale Content](#542-stale-content)
    - [5.4.3 Cache Poisoning](#543-cache-poisoning)
    - [5.4.4 Regional Consistency](#544-regional-consistency)
  - [5.5 CDN + Object Storage](#55-cdn--object-storage)
    - [5.5.1 Media Delivery](#551-media-delivery)
    - [5.5.2 Video Streaming](#552-video-streaming)
    - [5.5.3 Download Optimization](#553-download-optimization)
- [6. Large File Handling](#6-large-file-handling)
  - [6.1 Chunking Large Files](#61-chunking-large-files)
    - [6.1.1 Why Chunk Files?](#611-why-chunk-files)
    - [6.1.2 Multipart Uploads](#612-multipart-uploads)
    - [6.1.3 Resume Uploads](#613-resume-uploads)
    - [6.1.4 Parallel Uploads](#614-parallel-uploads)
  - [6.2 Chunk Processing Flow](#62-chunk-processing-flow)
    - [6.2.1 Upload Coordination](#621-upload-coordination)
    - [6.2.2 Chunk Verification](#622-chunk-verification)
    - [6.2.3 File Assembly](#623-file-assembly)
  - [6.3 Video & Media Storage](#63-video--media-storage)
    - [6.3.1 Adaptive Streaming](#631-adaptive-streaming)
    - [6.3.2 HLS / DASH](#632-hls--dash)
    - [6.3.3 Thumbnail Generation](#633-thumbnail-generation)
  - [6.4 Deduplication](#64-deduplication)
    - [6.4.1 File Hashing](#641-file-hashing)
    - [6.4.2 Chunk-Level Deduplication](#642-chunk-level-deduplication)
    - [6.4.3 Storage Savings](#643-storage-savings)
    - [6.4.4 Tradeoffs](#644-tradeoffs)
- [7. Distributed Storage Concepts](#7-distributed-storage-concepts)
  - [7.1 Replication](#71-replication)
    - [7.1.1 Synchronous Replication](#711-synchronous-replication)
    - [7.1.2 Asynchronous Replication](#712-asynchronous-replication)
    - [7.1.3 Multi-Region Replication](#713-multi-region-replication)
  - [7.2 Consistency Models](#72-consistency-models)
    - [7.2.1 Strong Consistency](#721-strong-consistency)
    - [7.2.2 Eventual Consistency](#722-eventual-consistency)
    - [7.2.3 Read-After-Write Consistency](#723-read-after-write-consistency)
  - [7.3 Data Durability](#73-data-durability)
    - [7.3.1 Redundancy](#731-redundancy)
    - [7.3.2 Erasure Coding](#732-erasure-coding)
    - [7.3.3 Backup Strategies](#733-backup-strategies)
  - [7.4 Storage Tiering](#74-storage-tiering)
    - [7.4.1 Hot Storage](#741-hot-storage)
    - [7.4.2 Warm Storage](#742-warm-storage)
    - [7.4.3 Cold Storage](#743-cold-storage)
    - [7.4.4 Archival Storage](#744-archival-storage)
- [8. Storage Decision Framework](#8-storage-decision-framework)
  - [8.1 Choosing the Right Storage Type](#81-choosing-the-right-storage-type)
    - [8.1.1 When to Use Block Storage](#811-when-to-use-block-storage)
    - [8.1.2 When to Use File Storage](#812-when-to-use-file-storage)
    - [8.1.3 When to Use Object Storage](#813-when-to-use-object-storage)
    - [8.1.4 Hybrid Architectures](#814-hybrid-architectures)
  - [8.2 Key Tradeoffs](#82-key-tradeoffs)
    - [8.2.1 Performance vs Scalability](#821-performance-vs-scalability)
    - [8.2.2 Latency vs Cost](#822-latency-vs-cost)
    - [8.2.3 Durability vs Complexity](#823-durability-vs-complexity)
    - [8.2.4 Consistency vs Availability](#824-consistency-vs-availability)
  - [8.3 Storage Architecture Patterns](#83-storage-architecture-patterns)
    - [8.3.1 Media Platforms](#831-media-platforms)
    - [8.3.2 Backup Systems](#832-backup-systems)
    - [8.3.3 Cloud Drives](#833-cloud-drives)
    - [8.3.4 Streaming Platforms](#834-streaming-platforms)
- [9. Storage Patterns by System Type](#9-storage-patterns-by-system-type)
  - [9.1 Summary: Storage System Techniques by Pattern](#91-summary-storage-system-techniques-by-pattern)
  - [9.2 Pattern A – CRUD & Storage Systems (Storage Focus)](#92-pattern-a---crud--storage-systems-storage-focus)
  - [9.3 Pattern B – Feed / Timeline Systems (Storage Focus)](#93-pattern-b---feed--timeline-systems-storage-focus)
  - [9.4 Pattern C – Real-Time Systems (Storage Focus)](#94-pattern-c---real-time-systems-storage-focus)
  - [9.5 Pattern D – Search Systems (Storage Focus)](#95-pattern-d---search-systems-storage-focus)
  - [9.6 Pattern E – Geospatial & Location-Based Systems (Storage Focus)](#96-pattern-e---geospatial--location-based-systems-storage-focus)
  - [9.7 Pattern F – Financial & Critical Consistency Systems (Storage Focus)](#97-pattern-f---financial--critical-consistency-systems-storage-focus)
  - [9.8 Pattern G – Large-Scale Processing & Infrastructure (Storage Focus)](#98-pattern-g---large-scale-processing--infrastructure-storage-focus)
  - [9.9 Pattern H – Collaborative & Infrastructure Utilities (Storage Focus)](#99-pattern-h---collaborative--infrastructure-utilities-storage-focus)
- [10. Interview-Oriented HLD Notes](#10-interview-oriented-hld-notes)
  - [10.1 Common Interview Questions](#101-common-interview-questions)
    - [10.1.1 Block vs File vs Object Storage?](#1011-block-vs-file-vs-object-storage)
    - [10.1.2 Why Object Storage for Media?](#1012-why-object-storage-for-media)
    - [10.1.3 How Does CDN Improve Performance?](#1013-how-does-cdn-improve-performance)
    - [10.1.4 How to Store Large Files?](#1014-how-to-store-large-files)
  - [10.2 Key Tradeoffs](#102-key-tradeoffs)
    - [10.2.1 Latency vs Scalability](#1021-latency-vs-scalability)
    - [10.2.2 Cost vs Durability](#1022-cost-vs-durability)
    - [10.2.3 Simplicity vs Flexibility](#1023-simplicity-vs-flexibility)
  - [10.3 Quick Revision Material](#103-quick-revision-material)
    - [10.3.1 Storage Comparison Table](#1031-storage-comparison-table)
    - [10.3.2 CDN Cheat Sheet](#1032-cdn-cheat-sheet)
    - [10.3.3 Storage Decision Matrix](#1033-storage-decision-matrix)
    - [10.3.4 Key Interview Sentences](#1034-key-interview-sentences)
    - [10.3.5 Self-Test Questions](#1035-self-test-questions)

---

## 1. Storage System Fundamentals

### 1.1 Why Storage Systems Matter

In modern distributed systems, choosing the right storage type is critical for performance, scalability, and cost. Each storage model – **block**, **file**, and **object** – serves different use cases and has distinct characteristics.

#### 1.1.1 Durability
Durability ensures data survives hardware failures, power outages, and disasters. Storage systems achieve durability through replication, erasure coding, and regular backups.

#### 1.1.2 Scalability
The ability to store petabytes to exabytes of data without performance degradation. Object storage excels here; block and file storage have more limited scalability.

#### 1.1.3 Availability
Data must be accessible when needed. High availability is achieved via redundancy across multiple data centers, automatic failover, and geographic distribution.

#### 1.1.4 Low Latency Access
Some workloads (databases, real‑time applications) demand microsecond or millisecond access. Block storage and in‑memory caches provide this; object storage introduces higher latency due to HTTP overhead.

#### 1.1.5 Cost Efficiency
Storage costs vary wildly: hot block storage (SSD) is expensive per GB; cold object storage (archival tiers) is extremely cheap. Lifecycle policies help move data to cheaper tiers automatically.

### 1.2 Core Storage Concepts

#### 1.2.1 Persistence
Data remains intact after the power is turned off. Magnetic disks, SSDs, and object stores all provide persistent storage.

#### 1.2.2 Replication
Maintaining multiple copies of data on different physical devices or locations to ensure availability and durability.

#### 1.2.3 Redundancy
Extra capacity (disks, servers, network paths) to tolerate failures without data loss or downtime.

#### 1.2.4 Compression
Reduces storage size and cost, and can improve transfer speeds. Often applied transparently in object storage (e.g., S3 supports automatic compression for some formats).

#### 1.2.5 Encryption
Data at rest and in transit can be encrypted for security. Cloud storage services offer built‑in server‑side encryption (SSE) and client‑side encryption options.

#### 1.2.6 Tiered Storage
Data is moved between storage classes based on access frequency:
- **Hot:** Frequently accessed (SSD, in‑memory).
- **Warm:** Less frequent (HDD, lower cost).
- **Cold:** Rarely accessed (object archival).
- **Archival:** Compliance / long‑term retention (Glacier, Deep Archive).

### 1.3 Types of Storage Systems

#### 1.3.1 Block Storage
Presents raw storage volumes to servers, much like a physical hard drive. Data is divided into fixed‑size blocks, each with its own address. The OS or application manages the file system on top.

#### 1.3.2 File Storage
Provides a shared file system interface with a hierarchical directory structure. Multiple clients can access and modify files over a network using protocols like NFS or SMB.

#### 1.3.3 Object Storage
Manages data as objects, each containing the data itself, metadata, and a globally unique identifier. Objects are stored in a flat namespace (buckets). Accessed via HTTP APIs.

#### 1.3.4 Blob Storage
Blob (Binary Large Object) storage is essentially the same as object storage. The term is predominantly used in Microsoft Azure (Azure Blob Storage). It stores unstructured data as blobs (block blobs, append blobs, page blobs).

| Storage Type | Abstraction Level | Access Pattern | Typical Protocols | Examples |
|--------------|-------------------|----------------|-------------------|----------|
| **Block Storage** | Low‑level (raw disk) | Read/write in fixed‑size blocks | iSCSI, Fibre Channel | AWS EBS, Azure Disk, GCP Persistent Disk |
| **File Storage** | File and directory hierarchy | File operations (open, read, write) | NFS, SMB/CIFS | AWS EFS, Azure Files, GCP Filestore |
| **Object Storage** | Flat namespace of objects with metadata | HTTP/HTTPS (REST API) | S3 API, Swift | AWS S3, Azure Blob, GCP Cloud Storage |

---

## 2. Block Storage

### 2.1 What Is Block Storage?

Block storage presents raw storage volumes to servers. Data is divided into fixed‑sized blocks, each with its own address.

#### 2.1.1 Fixed-Size Blocks
Storage is split into uniformly sized chunks (e.g., 512 bytes, 4 KB). This allows efficient random access.

#### 2.1.2 Raw Storage Volumes
A block volume is a blank slate. The OS formats it with a file system (ext4, XFS, NTFS) before use.

#### 2.1.3 Low-Level Disk Access
Applications can bypass the file system and perform raw I/O, useful for databases that implement their own storage management.

### 2.2 How Block Storage Works

#### 2.2.1 Block Addressing
Each block has a unique address (LBA – Logical Block Address). Reads/writes specify an LBA and data size.

#### 2.2.2 File System Layer
The OS file system translates file operations (e.g., "read 100 bytes at offset 0") into block addresses and I/O commands.

#### 2.2.3 Mounting Volumes
A block volume is attached to a virtual machine (e.g., EC2 instance) via storage area network (SAN) protocols or as a local NVMe drive. It appears as a device (e.g., `/dev/xvdf`).

### 2.3 Advantages of Block Storage

#### 2.3.1 Low Latency
Direct disk access provides microsecond to single‑digit millisecond latency.

#### 2.3.2 High Performance
Achieves high IOPS (input/output operations per second) and throughput, especially with SSDs (e.g., AWS EBS io2 volumes).

#### 2.3.3 Database Optimization
Databases (MySQL, PostgreSQL, Cassandra) require low‑latency random I/O. Block storage delivers this, while object storage does not.

### 2.4 Limitations of Block Storage

#### 2.4.1 Scalability Challenges
Volumes have a maximum size (e.g., 16 TB for EBS). Scaling beyond requires RAID or spanning multiple volumes, which adds complexity.

#### 2.4.2 Management Complexity
Provisioning, snapshots, backup, and recovery are more manual than fully managed object storage.

#### 2.4.3 Cost
Block storage, especially high‑performance SSD, is more expensive per GB than object storage.

### 2.5 Block Storage Use Cases

#### 2.5.1 Databases
Relational (PostgreSQL) and NoSQL (Cassandra) databases run on block storage for random I/O performance and ACID guarantees.

#### 2.5.2 VM Disks
Operating system boot volumes and application data disks for cloud VMs.

#### 2.5.3 High-Performance Applications
Workloads that require high IOPS and low latency, such as real‑time analytics and transaction processing.

### 2.6 Real-World Examples

#### 2.6.1 AWS EBS
Elastic Block Store: persistent block storage for EC2 instances. Volume types: gp3 (general purpose SSD), io2 (high‑performance SSD), st1 (throughput optimized HDD).

#### 2.6.2 SAN Storage
Storage Area Network: traditional enterprise block storage accessed via Fibre Channel or iSCSI.

#### 2.6.3 Azure Managed Disks
Block storage for Azure VMs; similarly, GCP Persistent Disks.

---

## 3. File Storage

### 3.1 What Is File Storage?

File storage provides a shared file system interface with a hierarchical directory structure. Multiple clients can mount and access the same file system concurrently over a network.

#### 3.1.1 Hierarchical File Structure
Organized into directories and files, just like a local disk.

#### 3.1.2 Shared File Systems
Multiple servers can read/write the same files, which is essential for content management systems, web server farms, and shared development environments.

#### 3.1.3 Directory-Based Organization
Users navigate with paths like `/shared/project/docs/report.pdf`.

### 3.2 How File Storage Works

#### 3.2.1 File Paths
Each file has a unique path. The file server maps the path to physical storage blocks.

#### 3.2.2 Network File Access
Clients use protocols like NFS (Network File System) or SMB (Server Message Block) to perform operations (open, read, write, close) over the network.

#### 3.2.3 Metadata Management
File servers maintain metadata: file size, permissions, timestamps, and extended attributes. Metadata operations can become a bottleneck at scale.

### 3.3 Advantages of File Storage

#### 3.3.1 Human Readability
Directory structures are intuitive for users and administrators.

#### 3.3.2 Shared Access
Native multi‑client concurrency with file locking, making it easier to collaborate.

#### 3.3.3 Simplicity
Works with existing applications that expect POSIX file systems, enabling lift‑and‑shift migrations.

### 3.4 Limitations of File Storage

#### 3.4.1 Scaling Limits
Traditional NAS devices have capacity and performance ceilings. Cloud‑native file services like AWS EFS scale automatically, but still have limits on throughput and IOPS per file system.

#### 3.4.2 Metadata Bottlenecks
Many small files can stress the metadata server, slowing down directory listings and lookups.

#### 3.4.3 Performance Constraints
Network overhead and locking protocols can reduce performance compared to local block storage.

### 3.5 File Storage Use Cases

#### 3.5.1 Shared Drives
Corporate shared folders for team documents.

#### 3.5.2 Team Collaboration
Multiple servers in a web farm sharing uploaded content, configuration files, or media.

#### 3.5.3 Enterprise File Servers
Centralized storage for user home directories and departmental shares.

### 3.6 Network File Systems

#### 3.6.1 NFS
Network File System – widely used in Linux/Unix environments. Provides stateless or stateful access.

#### 3.6.2 SMB
Server Message Block – used in Windows environments, also accessible from Linux via Samba.

#### 3.6.3 Distributed File Systems
Examples: HDFS (Hadoop Distributed File System), GlusterFS, CephFS. Designed for massive scale across hundreds of servers, often used in big data processing.

### 3.7 Real-World Examples

#### 3.7.1 AWS EFS
Elastic File System: fully managed NFS that scales automatically. Good for shared web content, home directories.

#### 3.7.2 Google Filestore
Managed NFS for Google Cloud.

#### 3.7.3 NAS Systems
Traditional Network Attached Storage appliances (Synology, QNAP, NetApp) used on‑premises.

---

## 4. Object & Blob Storage

### 4.1 What Is Object Storage?

Object storage manages data as **objects**. Each object consists of:
- The data itself (unstructured, any format)
- Metadata (key‑value pairs describing the object)
- A globally unique identifier (a URL).

Objects are stored in a flat namespace (buckets). There is no true directory hierarchy; “folders” are simulated by using prefixes (e.g., `photos/2025/vacation.jpg`).

#### 4.1.1 Objects & Metadata
You can attach custom metadata (e.g., `content-type`, `x-amz-meta-author`) to each object for search and lifecycle management.

#### 4.1.2 Flat Namespace
A bucket contains a list of keys. Listing objects with a common prefix is fast, but there are no true directories.

#### 4.1.3 Unique Object IDs
Each object is addressed via a unique URL, e.g., `https://bucket.s3.amazonaws.com/key`. The combination of bucket name and key is globally unique within the storage service.

### 4.2 How Object Storage Works

#### 4.2.1 HTTP-Based Access
Access is via RESTful APIs (PUT, GET, DELETE, HEAD) over HTTP/HTTPS. This allows any client that can speak HTTP to interact with storage.

#### 4.2.2 Distributed Storage Nodes
Data is spread across many servers and disks. Erasure coding or replication ensures durability.

#### 4.2.3 Metadata Indexing
A distributed metadata service tracks the location of each object, allowing fast lookup by key.

### 4.3 Blob Storage Clarification

#### 4.3.1 Binary Large Objects
“Blob” stands for Binary Large Object. It’s the same concept as an object in object storage – unstructured binary data with metadata.

#### 4.3.2 Blob vs Object Storage
There is no functional difference. Azure Blob Storage is Microsoft’s object storage service, just as Amazon S3 is AWS’s. In common usage, “blob storage” = “object storage”.

#### 4.3.3 Cloud Blob Storage Services
Azure Blob Storage, Google Cloud Storage, and AWS S3 all provide object storage.

### 4.4 Advantages of Object Storage

#### 4.4.1 Massive Scalability
Designed to store exabytes of data. A single S3 bucket can hold trillions of objects.

#### 4.4.2 High Durability
99.999999999% (11 nines) durability by storing data redundantly across multiple availability zones. Even if two data centers fail, data is safe.

#### 4.4.3 Cost Efficiency
Object storage is significantly cheaper per GB than block or file storage, especially with infrequent access and archival tiers.

#### 4.4.4 Global Distribution
Objects can be replicated across regions for low‑latency access worldwide. Integrated with CDNs for even faster delivery.

### 4.5 Limitations of Object Storage

#### 4.5.1 Higher Latency
HTTP overhead adds latency (typically 10–100ms for small objects). Not suitable for sub‑millisecond database operations.

#### 4.5.2 No Random Disk Access
You cannot modify part of an object in place. You must upload a new version of the entire object (though some services like Azure Page Blobs support random writes for specific use cases like VHDs).

#### 4.5.3 Eventual Consistency Tradeoffs
While modern object stores like S3 now offer strong read‑after‑write consistency for new objects, legacy eventual consistency models can still apply to some operations (e.g., listing after deletes).

### 4.6 Object Storage Use Cases

#### 4.6.1 Media Storage
Images, videos, audio, and other user‑generated content. Object storage scales to handle massive media libraries.

#### 4.6.2 Backups & Archives
Durable, low‑cost storage for database dumps, snapshots, and long‑term archives. Lifecycle policies automatically move data to colder tiers.

#### 4.6.3 Logs & Analytics
Store raw log files, then process them with Spark, Presto, or Athena directly from object storage (data lake pattern).

#### 4.6.4 Static Website Hosting
Host HTML, CSS, JS, and assets directly from a bucket, optionally fronted by a CDN.

### 4.7 Real-World Examples

#### 4.7.1 Amazon S3
The most popular object storage. Buckets, objects, versioning, lifecycle policies, encryption, and strong consistency.

#### 4.7.2 Google Cloud Storage
Similar capabilities, with storage classes (Standard, Nearline, Coldline, Archive).

#### 4.7.3 Azure Blob Storage
Azure’s object storage with block blobs, append blobs, and page blobs.

#### 4.7.4 MinIO
Open‑source, self‑hosted S3‑compatible object storage for on‑premises or hybrid clouds.

---

## 5. CDN & Media Delivery

### 5.1 Content Delivery Network (CDN)

#### 5.1.1 What Is a CDN?
A geographically distributed network of edge servers that cache content close to end users. It reduces latency and offloads origin servers.

#### 5.1.2 Edge Servers
Servers located in Points of Presence (PoPs) around the world. They cache content and serve local requests.

#### 5.1.3 Origin Servers
The original source of content – typically an object storage bucket (S3) or a web server. Edge servers fetch from the origin on cache misses.

#### 5.1.4 Global Caching
CDN caches static and even dynamic content at the edge, bringing data within tens of milliseconds of any user.

### 5.2 How CDN Works

#### 5.2.1 Request Routing
DNS routes the user to the nearest CDN edge server (using geo‑DNS or anycast IP).

#### 5.2.2 Edge Caching
If the content is cached (cache hit), it is returned immediately from the edge.

#### 5.2.3 Cache Miss Flow
On a cache miss, the edge server fetches the content from the origin, stores a copy locally, and returns it to the user. Subsequent requests are served from the edge.

```
User in Mumbai requests an image:
Without CDN → US-East S3 → 200ms latency.
With CDN → Mumbai PoP cache hit → 5ms.
On first miss → Mumbai PoP fetches from US-East S3 (210ms) → caches → next time 5ms.
```

### 5.3 CDN Models

#### 5.3.1 Pull CDN
The CDN automatically pulls content from the origin when first requested. No pre‑uploading. Most common model. Cache‑Control headers control TTL.

#### 5.3.2 Push CDN
You proactively upload content to CDN edges. Used for predictable large releases (e.g., a new movie). More complex to manage.

#### 5.3.3 Hybrid CDN
Combines pull and push: most content pulled on demand; critical assets pushed proactively.

### 5.4 CDN Challenges

#### 5.4.1 Cache Invalidation
Updating content that is cached at edges requires purging or waiting for TTL expiry. Solutions: versioned URLs, purge API, short TTL.

**Versioned URLs (best practice):**
```
Old: cdn.example.com/avatars/user123.jpg
New: cdn.example.com/avatars/user123.jpg?v=2   → different URL, cache miss → fetches fresh.
```

#### 5.4.2 Stale Content
Long TTLs can cause users to see outdated content until the cache expires. Mitigate with appropriate TTLs and purging.

#### 5.4.3 Cache Poisoning
Attackers may try to inject malicious content into the CDN cache. Mitigations: input validation, signed URLs, authentication of purge requests.

#### 5.4.4 Regional Consistency
Edges in different regions may serve different versions after a purge due to propagation delays. Use global purge and short TTLs if needed.

### 5.5 CDN + Object Storage

#### 5.5.1 Media Delivery
Images, videos, and audio stored in S3 are served via CDN. The CDN pulls from S3 as the origin. This combination provides scalable, low‑latency global delivery.

#### 5.5.2 Video Streaming
Video chunks (e.g., HLS segments) are stored in object storage and delivered through CDN edges. CDNs are critical for handling millions of concurrent viewers.

#### 5.5.3 Download Optimization
Large file downloads (software, game patches) are accelerated by CDNs that can resume partial downloads and use multiple parallel connections.

---

## 6. Large File Handling

### 6.1 Chunking Large Files

#### 6.1.1 Why Chunk Files?
Uploading large files (video, backup) in a single request risks failure from network interruption and can’t be parallelized. Chunking solves these problems.

#### 6.1.2 Multipart Uploads
Cloud storage services support multipart uploads: the client splits the file into parts (e.g., 5 MB each), uploads them in parallel, and then asks the server to assemble the final object.

**S3 Multipart Upload flow:**
1. Initiate upload → get Upload ID.
2. Upload parts (each has a part number and ETag).
3. Complete upload: S3 assembles parts into the final object.

#### 6.1.3 Resume Uploads
If a part upload fails, only that part needs to be retried. The client can resume from where it left off.

#### 6.1.4 Parallel Uploads
Multiple parts can be uploaded simultaneously, greatly increasing throughput, especially for large files.

### 6.2 Chunk Processing Flow

#### 6.2.1 Upload Coordination
The client (or an upload service) manages the list of parts, tracks which are completed, and handles retries.

#### 6.2.2 Chunk Verification
Each part is typically validated with an MD5 or SHA256 checksum (ETag) to ensure integrity.

#### 6.2.3 File Assembly
Once all parts are uploaded, the client sends a complete multipart upload request. The storage service concatenates the parts.

### 6.3 Video & Media Storage

#### 6.3.1 Adaptive Streaming
Video is encoded at multiple bitrates and resolutions. Each version is chunked into small segments (2‑10 seconds). The player adapts based on network conditions.

#### 6.3.2 HLS / DASH
HLS (HTTP Live Streaming) and DASH (Dynamic Adaptive Streaming over HTTP) are the standard protocols. They use manifest files (`.m3u8`, `.mpd`) pointing to segment URLs, all served from CDN/object storage.

#### 6.3.3 Thumbnail Generation
On video upload, a background job extracts a frame and generates a thumbnail image, stored in object storage and cached by CDN.

### 6.4 Deduplication

#### 6.4.1 File Hashing
Compute a hash (SHA‑256) of the entire file content. If the hash already exists in the storage system, skip the upload and just add a reference.

#### 6.4.2 Chunk-Level Deduplication
Split files into chunks (e.g., 4 MB). Hash each chunk. Store unique chunks only. Two similar files (e.g., a video with a minor edit) share most chunks, saving significant space.

**Dropbox deduplication example:**
- User A uploads `cat.jpg`. Hash = `abc`. Store file as `abc`. Add DB entry `{userA, cat.jpg, hash=abc}`.
- User B uploads the same file. Hash = `abc`. Hash exists → skip upload. Add DB entry `{userB, cat.jpg, hash=abc}`.
- Only one copy stored.

#### 6.4.3 Storage Savings
Block‑level dedup can reduce storage consumption by 10–20× for backup workloads where many files share common data blocks.

#### 6.4.4 Tradeoffs
Deduplication adds CPU overhead for hashing and requires maintaining a hash index. The benefit depends on data redundancy.

---

## 7. Distributed Storage Concepts

### 7.1 Replication

#### 7.1.1 Synchronous Replication
A write is acknowledged to the client only after it has been persisted on all (or a quorum of) replicas. Guarantees zero data loss but adds latency. Used for critical financial databases.

#### 7.1.2 Asynchronous Replication
The primary acknowledges the write immediately, then propagates changes to replicas in the background. Lower latency but risk of data loss if the primary fails before replication. Common for distributed file and object stores.

#### 7.1.3 Multi-Region Replication
Data is asynchronously or synchronously replicated between geographically distant regions. Provides disaster recovery and local read access. Examples: S3 Cross‑Region Replication, Cassandra multi‑DC replication.

### 7.2 Consistency Models

#### 7.2.1 Strong Consistency
After a write completes, all subsequent reads will see the new data. Achieved via synchronous replication or quorum. S3 now provides strong read‑after‑write consistency for all operations.

#### 7.2.2 Eventual Consistency
If no new writes, eventually all accesses will return the last updated value. Replicas may temporarily diverge, but converge over time. Used in many distributed systems (Cassandra, DynamoDB).

#### 7.2.3 Read-After-Write Consistency
A weaker guarantee: after a client writes data, that same client will see its own write immediately, even if the system is eventually consistent for others. Often implemented via sticky sessions or reading from the primary.

### 7.3 Data Durability

#### 7.3.1 Redundancy
Storing multiple copies (replicas) of data. If one disk or server fails, the data is still available from another copy.

#### 7.3.2 Erasure Coding
An alternative to full replication. Data is split into `k` data fragments and `m` parity fragments. The original can be reconstructed from any `k` fragments. More storage‑efficient than replication (e.g., 1.5× overhead vs 3× for 3‑way replication). Used by S3, HDFS, MinIO.

#### 7.3.3 Backup Strategies
Regular snapshots and backups (full, incremental) stored in a separate, durable location (often object storage). Provides protection against logical errors (accidental deletions) and ransomware.

### 7.4 Storage Tiering

#### 7.4.1 Hot Storage
Frequently accessed data requiring low latency. Stored on SSDs or in‑memory caches. Expensive.

#### 7.4.2 Warm Storage
Less frequently accessed data; can tolerate slightly higher latency. Stored on HDDs or object storage with infrequent access pricing.

#### 7.4.3 Cold Storage
Rarely accessed data (backups, archives) with retrieval times of minutes to hours. Very cheap per GB. Examples: S3 Glacier, Azure Archive.

#### 7.4.4 Archival Storage
Long‑term retention for compliance and legal requirements. Data is offline and retrieval may take 12–48 hours. Extremely low cost.

---

## 8. Storage Decision Framework

### 8.1 Choosing the Right Storage Type

#### 8.1.1 When to Use Block Storage
- Low‑latency random I/O required (databases).
- Application expects a raw disk (runs its own file system).
- High IOPS for a single server.
- Example: MySQL database on EC2 with EBS io2 volumes.

#### 8.1.2 When to Use File Storage
- Multiple servers need shared access to the same files.
- Legacy application requires POSIX file system.
- Shared workspace for user uploads processed by multiple workers.
- Example: Web application with multiple instances serving user‑uploaded images from a shared EFS volume.

#### 8.1.3 When to Use Object Storage
- Large amounts of unstructured data (images, videos, logs, backups).
- Need for scalability (exabytes) and durability (11 nines).
- Data accessed via HTTP (web/mobile apps).
- Cost‑effective storage for infrequently accessed data (with lifecycle policies).
- Building a data lake for analytics.
- Example: Storing user profile pictures in S3, served via CloudFront.

#### 8.1.4 Hybrid Architectures
Most real‑world systems use a mix:
- **Database:** Block storage for transaction logs and data files; nightly backups to object storage.
- **Media platform:** Object storage for uploaded files; CDN for delivery; file storage for shared configuration.
- **Big data:** Raw data lands in object storage (data lake); processing uses ephemeral block storage; results back to object storage.

```
WHAT ARE YOU STORING?
        ├── User files, images, videos, backups → Object Storage (S3)
        ├── Database data, OS volumes → Block Storage (EBS)
        ├── Shared config, logs, ML datasets → File Storage (EFS/NFS)
        └── Sessions, counters, hot data → In-Memory (Redis)

HOW IS IT ACCESSED?
        ├── By URL globally → Object Storage + CDN
        ├── Sequential (stream bytes) → Object Storage
        ├── Random I/O at byte offsets → Block Storage
        └── Filesystem semantics (ls, mkdir) → File Storage

HOW BIG IS IT?
        ├── Petabytes+ → Object Storage (unlimited, cheap)
        ├── Gigabytes, high IOPS → Block Storage (fast, expensive)
        └── Terabytes, shared → File Storage
```

### 8.2 Key Tradeoffs

#### 8.2.1 Performance vs Scalability
Block storage delivers the best performance but is limited in size. Object storage scales to exabytes but has higher latency.

#### 8.2.2 Latency vs Cost
SSD block storage has the lowest latency but highest cost. Cold object storage has minutes‑to‑hours latency but extremely low cost.

#### 8.2.3 Durability vs Complexity
Erasure coding provides high durability with less storage overhead than replication but is more complex to implement and compute.

#### 8.2.4 Consistency vs Availability
Synchronous replication gives strong consistency at the cost of availability during partitions. Asynchronous replication favors availability.

### 8.3 Storage Architecture Patterns

#### 8.3.1 Media Platforms
Upload: client → pre‑signed URL → object storage. Transcoding: events trigger workers that read from object storage, process, and write back. Delivery: CDN in front of object storage.

#### 8.3.2 Backup Systems
Database snapshots → uploaded to object storage. Lifecycle policies move to colder tiers after 30 days. Encryption at rest.

#### 8.3.3 Cloud Drives
Metadata in a relational database or Cassandra. File chunks stored in object storage (content‑addressed). Block‑level dedup for efficiency.

#### 8.3.4 Streaming Platforms
Videos chunked and stored in object storage. Manifest files (HLS/DASH) also in object storage. CDN distributes chunks globally.

---

## 9. Storage Patterns by System Type

### 9.1 Summary: Storage System Techniques by Pattern

| Pattern | System Type | Primary Storage Technology | Storage Pattern | Durability Mechanism | Why |
|---------|-------------|----------------------------|-----------------|----------------------|-----|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | **Key‑value** (DynamoDB/Cassandra) for lookups; **Object** (S3) for blobs; **SQL** for inventory | Key‑value (hash), object (content‑addressed), relational (ACID) | Replication (3+ nodes), erasure coding for blobs | Simple key lookups; large immutable objects; transactional inventory |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | **Wide‑column** (Cassandra) for posts; **Object** (S3) for media; **Time‑series** for activity | Time‑partitioned (by user_id, timestamp) | Replication factor 3, quorum writes | Write‑heavy, time‑ordered data; needs linear scalability |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | **Wide‑column** (Cassandra) for message history; **In‑memory** (Redis) for state; **Kafka** for log | Append‑only log per conversation; time‑based TTL | Replication + retention policies | Messages are immutable; need high write throughput |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | **Inverted index** (Elasticsearch); **Bigtable** for crawl storage; **Key‑value** for metadata | Sharded by term/document ID; time‑based index rollover | Index replication, snapshot backups | Index is huge; needs parallel query; updates are batch/stream |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | **Spatial** (PostGIS, Redis Geo); **Time‑series** for trip history; **Object** for map tiles | Geohash / quadtree partitioning; time‑based for history | Replication + periodic snapshots | Geospatial queries need spatial indexes; history is append‑only |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | **SQL** (PostgreSQL, CockroachDB) with ACID; **Immutable ledger** (Kafka + DB) | Row‑based with indexes; append‑only transaction log | Synchronous replication (Raft/Paxos) | Strong consistency and ACID are mandatory |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | **Columnar** (BigQuery, Parquet); **Time‑series** (Druid, Prometheus TSDB); **Log** (Kafka) | Partitioned by time, customer_id; columnar for analytics | Erasure coding, replication, retention policies | High write throughput; analytical queries; append‑only |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | **In‑memory** (Redis) for state; **Consensus log** (etcd) for locks; **Object** for assets; **Bloom filter** for dedup | CRDT state per document; sequential log for consensus | Replication (Raft), periodic snapshots | Low latency; consensus needed for locks; dedup for crawling |

---

### 9.2 Pattern A – CRUD & Storage Systems (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **URL Shortener** | **Key‑value store** (DynamoDB, Cassandra) for mapping short_code → long_url; **Redis** for hot cache; **S3** for analytics exports | Simple lookup by key; high read throughput; no complex queries. | DynamoDB table with `short_code` as partition key. Enable DAX or Redis for caching. For analytics, export click logs to S3 as Parquet. |
| **Pastebin** | **Object storage** (S3) for paste content; **Key‑value** (DynamoDB) for metadata (paste_id, user, expiry, syntax) | Paste content is large (up to MBs) and immutable. Metadata is small and queryable. | S3 key = `pastes/{paste_id}`. DynamoDB table with TTL on expiry. Background worker deletes from S3 when TTL expires. |
| **Online Bookstore** | **SQL** (PostgreSQL) for inventory (ACID); **Elasticsearch** for product search; **Redis** for catalog cache; **S3** for product images | Inventory needs transactions. Search needs full‑text. Images are static. | Inventory: PostgreSQL with `product_id` PK, row‑level locks. Search: Elasticsearch index. Images: S3 + CDN. |
| **Ticket Booking** | **SQL** (PostgreSQL sharded by `event_id`) for reservations; **Redis** for seat locks (short‑term); **S3** for archived bookings | Seat reservations need ACID per event. Sharding reduces contention. | Shard PostgreSQL by `event_id`. Use `SELECT FOR UPDATE` for seat locking. Redis: `SETEX seat:event:row:num 300 "user_id"`. |
| **Dropbox / Google Drive** | **Object storage** (S3) for file chunks; **Wide‑column** (Cassandra) for metadata (file tree); **Redis** for user sessions | File chunks are immutable and large. Metadata is write‑heavy (renames, moves). | S3: content‑addressed storage (hash of chunk). Cassandra: table `(user_id, file_path) → file_id`. Redis: session tokens. |

---

### 9.3 Pattern B – Feed / Timeline Systems (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Twitter / X** | **Wide‑column** (Cassandra) for tweets: `(user_id, tweet_id, timestamp)`; **Redis** for timeline cache; **Kafka** for ingestion | Tweets are time‑ordered per user. Cassandra handles high write throughput. | Tweets table with clustering order DESC by timestamp. Partition by `user_id`. Timeline cache: Redis sorted set (tweet IDs). |
| **Instagram Feed** | **Cassandra** for posts; **S3** for images; **Redis** for feed cache; **Time‑series** for activity | Similar to Twitter, plus media storage. | Posts table: `(user_id, post_id, timestamp)`. Images in S3 with CDN. Feed cache: Redis sorted set. |
| **Facebook News Feed** | **TAO** (custom graph DB) for social graph; **Cassandra** for posts; **Memcached** for feed cache; **HBase** for activity logs | Graph traversal for feed ranking; high write volume. | TAO stores edges `(user1, user2, type)`. Posts in Cassandra. Pre‑computed feed in Memcached. |
| **LinkedIn Feed** | **Cassandra** for posts; **Redis** for profile cache; **SQL** for professional profiles | Lower volume; pull model needs fast profile lookups. | Posts: `(user_id, post_id, timestamp)`. Profiles: Redis hash. SQL for structured profile data (experience, education). |

---

### 9.4 Pattern C – Real-Time Systems (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | **Cassandra** for message history: `(conversation_id, message_id, timestamp)`; **Redis** for presence; **Kafka** for delivery log | Messages are append‑only per conversation. TTL for old messages. | Cassandra table with TTL (e.g., 30 days). Partition by `conversation_id`, cluster by `timestamp`. Redis: `presence:{user_id}` TTL=30s. |
| **Slack / Discord** | **PostgreSQL** (sharded by `channel_id`) for channel history; **Redis** for presence and unread counts; **S3** for file uploads | Channel history needs strong ordering. Sharding isolates channels. | PostgreSQL: `channel_id` as partition key, serial PK for message order. Redis: unread counts as hash. Files in S3. |
| **Live Comments (YouTube Live)** | **Redis** sorted set for recent comments (per video); **Kafka** for ingestion; **S3** for archival | Comments are high velocity; only recent comments need fast access. | Redis: `comments:video_id` sorted set (score=timestamp), limit to 500. Kafka retains 1 hour. S3 archives all comments. |
| **Multiplayer Game Backend** | **Redis** for game state (in‑memory); **DynamoDB** for periodic checkpoints; **S3** for replay logs | Game state needs microsecond latency. Checkpoints provide durability. | Redis hash: `game:{room_id}`. Checkpoint to DynamoDB every 5s. Replay logs to S3 (JSON lines). |

---

### 9.5 Pattern D – Search Systems (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Search** | **Bigtable** / **Cassandra** for inverted index; **Elasticsearch** for serving; **GFS / HDFS** for crawl storage | Inverted index is huge; sharding by term allows parallel query. | Index shards: each shard stores a subset of terms on HDFS. Query router fans out. Crawled pages stored in GFS. |
| **YouTube Search** | **Elasticsearch** for video metadata; **Bigtable** for index; **S3** for thumbnails | Video metadata (title, description) is indexed. Thumbnails are static. | Elasticsearch index partitioned by date. Bigtable for large‑scale index (if > petabytes). Thumbnails in S3 + CDN. |
| **Amazon Product Search** | **Elasticsearch** for product search; **DynamoDB** for inventory (real‑time); **S3** for product images | Search needs full‑text and faceting. Inventory needs real‑time updates. | Elasticsearch index with product fields. DynamoDB: `inventory:{product_id}`. Images in S3. |
| **Search Autocomplete** | **Redis** for trie (sorted set); **S3** for static trie backup; **Kafka** for streaming updates | Trie must be low‑latency and updated asynchronously. | Build trie hourly, store in S3 as JSON. Redis loads from S3 on startup. Real‑time updates via Kafka. |

---

### 9.6 Pattern E – Geospatial & Location-Based Systems (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Uber / Lyft** | **Redis Geo** for driver locations; **PostgreSQL + PostGIS** for trip history; **Cassandra** for ride requests | Driver locations need real‑time (Redis). Trip history is append‑only. | Redis Geo: `GEOADD drivers:city`. Trip history: PostgreSQL with spatial index. Ride requests: Cassandra `(ride_id, status)`. |
| **Google Maps / Nearby Places** | **PostgreSQL + PostGIS** for place data; **S3** for map tiles; **Redis** for cache | Places are read‑heavy; map tiles are static. | Place table with `geography` column, GiST index. Map tiles pre‑generated to S3 + CDN. Redis cache for popular place searches. |
| **Food Delivery (DoorDash)** | **Redis Geo** for drivers; **PostgreSQL** for restaurants; **Cassandra** for order history | Same as Uber, plus restaurant metadata. | Driver location: Redis Geo. Restaurants: PostgreSQL with geospatial index. Order history: Cassandra time‑partitioned. |
| **Nearby Friends** | **Redis Geo** for user locations; **Cassandra** for location history (optional); **WebSocket** for real‑time push | Real‑time location sharing needs Redis. History is optional. | Redis Geo: `GEOADD users:online`. Cassandra: `(user_id, timestamp, lat, lon)` with TTL=24h. |

---

### 9.7 Pattern F – Financial & Critical Consistency Systems (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | **PostgreSQL** (synchronous replication) for payments; **Redis** for idempotency cache; **Kafka** for transaction log | ACID mandatory. Idempotency needs fast lookup. Transaction log for audit. | Payments table with `idempotency_key` unique index. Redis: `SETEX idempotency:{key}`. Kafka: append‑only payment events. |
| **Wallet System** | **CockroachDB** (multi‑region, strong consistency) for balance; **Redis** for balance cache; **Kafka** for transaction log | Balance updates must be atomic and consistent across regions. | CockroachDB with `version` column for optimistic locking. Redis: balance cache with CAS. Kafka: all transactions. |
| **UPI-like System** | **Spanner** or **CockroachDB** for cross‑bank ledger; **Kafka** for inter‑bank messaging; **Redis** for idempotency | Distributed ACID needed for cross‑bank transactions. | Spanner with TrueTime for external consistency. Kafka for saga steps. Redis for idempotency keys. |
| **Distributed Job Scheduler** | **etcd** for leader election and job leases (strong consistency); **PostgreSQL** for job definitions; **Redis** for job queue | etcd provides linearizable leases. Job definitions are persistent. | etcd: store job leases with TTL. PostgreSQL: job metadata. Redis: job queue (list or sorted set). |

---

### 9.8 Pattern G – Large-Scale Processing & Infrastructure (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Analytics** | **BigQuery** (columnar) for analytics; **Kafka** for ingestion; **Redis** for real‑time; **S3** for raw logs | Columnar storage is efficient for aggregations. BigQuery scales to petabytes. | Raw logs → Kafka → Flink → BigQuery partitioned by date. Real‑time counts in Redis. Raw logs archived to S3. |
| **Clickstream Processing** | **Kafka** for raw events; **RocksDB** (Flink state) for sessionisation; **Druid** for OLAP; **S3** for checkpoints | Sessionisation requires stateful stream processing. | Flink keyed by `user_id` with RocksDB state backend. Checkpoints to S3. Output to Druid for sub‑second queries. |
| **Distributed Logging (ELK)** | **Kafka** as buffer; **Elasticsearch** for indexing; **S3** for cold storage | Elasticsearch optimised for log search. Time‑based partitioning. | Logs → Kafka → Logstash → Elasticsearch index per day. Hot nodes (SSD) for recent 7 days, warm nodes for older. S3 Glacier for >90 days. |
| **Metrics Monitoring (Prometheus)** | **Prometheus TSDB** (local) for recent metrics; **Thanos** (S3) for long‑term; **VictoriaMetrics** for high cardinality | Prometheus TSDB optimised for time‑series. S3 provides cheap long‑term storage. | Prometheus stores 2 hours locally. Thanos sidecar uploads to S3. Queries federate across local and S3. |
| **Design Kafka-like System** | **Segmented log** on disk (`.log` + `.index`); **Raft** for metadata; **Page cache** for performance | Kafka’s core is a replicated, partitioned log. | Each partition is directory of segments. OS page cache caches recent reads/writes. Metadata (brokers, partitions) in Raft log. |

---

### 9.9 Pattern H – Collaborative & Infrastructure Utilities (Storage Focus)

| Question | Storage Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Docs** | **Redis** for document CRDT state; **Cassandra** for document history (append‑only); **S3** for exports | CRDT state needs low latency. History is immutable. | Redis hash per character/position. Cassandra: `(doc_id, version, operation)`. S3: periodic exports (PDF, DOCX). |
| **Figma / Collaborative Whiteboard** | **Redis** for object state; **S3** for assets; **CRDT** library (yjs) for merge; **PostgreSQL** for project metadata | Objects are many; CRDT operations are small. Assets are static. | Redis hash per object ID. CRDT operations broadcast via WebSocket. Assets (images, fonts) in S3 + CDN. |
| **Distributed Locking Service** | **etcd** (Raft log) for lock state; **No separate storage** | Locks require linearizable storage. etcd provides it. | etcd stores locks as keys with lease (TTL). Fencing token stored with lock. |
| **Unique ID Generator (Snowflake)** | **etcd** for worker ID assignment; **No storage for IDs** | Worker IDs assigned via consensus. ID generation is local. | etcd: atomic increment on `/worker_ids/`. Each worker caches its ID. No ID storage needed. |
| **Web Crawler** | **Bloom filter** (Redis or in‑memory) for dedup; **Redis** for URL frontier (sorted set); **S3** for crawled pages | Bloom filter saves memory for dedup. Frontier needs priority queue. | RedisBloom: `BF.ADD visited:url`. Frontier: `ZADD frontier priority url`. Crawled content in S3. |

---

## 10. Interview-Oriented HLD Notes

### 10.1 Common Interview Questions

#### 10.1.1 Block vs File vs Object Storage?
Explain the abstraction level, access protocols, typical performance, scalability, and use cases. Use the comparison table from Section 1.3.4.

#### 10.1.2 Why Object Storage for Media?
Media files are large, unstructured, written once and read many times. Object storage provides massive scalability, high durability, low cost, and native HTTP access, making it ideal. A CDN in front solves latency.

#### 10.1.3 How Does CDN Improve Performance?
CDN caches content at edge locations close to users, reducing round‑trip time and origin load. For a global audience, latency drops from hundreds of milliseconds to single digits.

#### 10.1.4 How to Store Large Files?
Use chunking and multipart uploads. Split the file into parts, upload in parallel, resume on failure. Store assembled object in object storage. For video, transcode into adaptive streaming formats (HLS/DASH) and deliver via CDN.

### 10.2 Key Tradeoffs

#### 10.2.1 Latency vs Scalability
Block storage: low latency, limited scale. Object storage: higher latency, unlimited scale. Choose based on workload.

#### 10.2.2 Cost vs Durability
Higher durability requires replication or erasure coding, increasing cost. Object storage offers the best durability per dollar.

#### 10.2.3 Simplicity vs Flexibility
File storage is simple and POSIX‑compatible but lacks the flexibility and scalability of object storage for modern cloud applications.

### 10.3 Quick Revision Material

#### 10.3.1 Storage Comparison Table

| Feature | Block Storage | File Storage | Object Storage |
|---------|---------------|--------------|----------------|
| **Access** | Via OS (block device) | Via mounted file system | Via HTTP API |
| **Performance** | Very low latency, high IOPS | Good throughput, moderate IOPS | Higher latency (HTTP), high throughput for large objects |
| **Scalability** | Limited by volume size | Limited by file system size | Virtually unlimited (exabytes) |
| **Sharing** | Single server (or cluster FS) | Multiple servers (NFS/SMB) | Any number of clients via API |
| **Use case** | Databases, OS volumes | Shared files, home directories | Static assets, backups, data lakes |
| **Cost per GB** | High (SSD) / Medium (HDD) | Medium | Low (standard) to very low (archive) |
| **Metadata** | Limited (file system level) | File attributes, extended attributes | Rich custom metadata |
| **Example** | AWS EBS, Azure Disk | AWS EFS, Azure Files | AWS S3, Azure Blob, GCP Storage |

#### 10.3.2 CDN Cheat Sheet
- **What:** Geographically distributed cache for content delivery.
- **Benefits:** Low latency, high throughput, origin offloading, DDoS protection.
- **Models:** Pull (most common), Push (proactive), Hybrid.
- **Invalidation:** Versioned URLs (best), purge API, short TTL.
- **Cache‑Control header:** Controls browser and CDN caching behavior.

#### 10.3.3 Storage Decision Matrix

| If you need… | Use… |
|--------------|------|
| Low‑latency random I/O (database) | Block storage |
| Shared file access across multiple servers | File storage |
| Massive scale, unstructured data, HTTP access | Object storage |
| Global, low‑latency media delivery | Object storage + CDN |
| Cheap, durable long‑term backups | Object storage (cold/archive tiers) |
| Temporary, ultra‑fast data (sessions, counters) | In‑memory (Redis) |

#### 10.3.4 Key Interview Sentences

> *"User‑uploaded media — images and videos — goes to S3. The app server generates a pre‑signed URL and returns it to the client. The client uploads directly to S3, bypassing the app server entirely. That way our app servers don't become I/O bottlenecks. A CDN in front of S3 handles global delivery with low latency."*

> *"For video uploads, I'd chunk the file into 5MB pieces on the client side. Each chunk is uploaded in parallel using S3's multipart upload API. If a chunk fails, only that chunk needs to be retried, not the entire video. This also gives us a real upload progress indicator."*

> *"Deduplication saves significant storage in a Dropbox-like system. We hash each block of each file. If the hash already exists in our storage, we skip the upload and just add a reference. Users who upload the same file — like a company‑wide presentation — share the storage cost."*

#### 10.3.5 Self-Test Questions

1. You're designing YouTube. Where do you store: raw video files, video metadata (title, description), user watch history, video thumbnails? Give a storage type and reason for each.
2. Why can't you use object storage as the underlying storage for a PostgreSQL database?
3. A user updates their profile picture on Instagram. The old picture is cached in the CDN with a 7‑day TTL. How do you ensure they see the new picture immediately?
4. A user uploads a 4GB video. The connection drops at 3.5GB. How does chunking help? What does the client need to do?
5. Two Dropbox users upload the exact same 500MB file. How does deduplication work? What's stored on disk vs what's stored in the metadata DB?

*End of consolidated notes.*
