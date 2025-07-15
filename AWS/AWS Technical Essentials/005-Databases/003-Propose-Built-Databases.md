# Purpose-Built Databases on AWS  
*Pick the engine that fits the job rather than forcing the job into one engine.*

---

## Table of Contents
1. [Introduction](#introduction)  
2. [Why Purpose-Built?](#why-purpose-built)  
3. [AWS Portfolio Overview](#aws-portfolio-overview)  
4. [Deep-Dive on Key Services](#deep-dive-on-key-services)  
5. [Decision Heuristics](#decision-heuristics)  
6. [Serverless & Scalability Patterns](#serverless--scalability-patterns)  
7. [Security & Cost Optimization Quick-Hits](#security--cost-optimization-quick-hits)  
8. [Best Practices](#best-practices)  
9. [SAA-C03 Critical Analysis](#saa-c03-critical-analysis)  
10. [Further Study & Labs](#further-study--labs)  
11. [References](#references)  
12. [Changelog](#changelog)  

---

## Introduction
For decades a single relational engine looked like the Swiss-army knife of data. Today, one-size-fits-all databases crumble under micro-latency, petabyte analytics, immutable audit trails, or massive graph traversals. AWS responded by building **15+ purpose-built engines** that let architects match data model, access pattern, and operational model to each workload.

---

## Why Purpose-Built?
* Relational engines shine at ACID joins but struggle with horizontally partitioned graphs or millisecond IoT ingestion.  
* NoSQL families drop rigid schemas and scale linearly, but you trade SQL joins for denormalization.  
* Specialized engines (time series, ledger, vector search) collapse whole stacks of bespoke code into single managed services.  

Outcome: **lower latency, higher throughput, simpler operations, and often dramatic cost drops** when the engine aligns with the data model.

---

## AWS Portfolio Overview
| Data Model | AWS Service | One-Line Superpower | Typical Workloads |
|------------|-------------|---------------------|-------------------|
| **Relational OLTP** | Amazon Aurora (MySQL & PostgreSQL-compatible) | 3-5× open-source throughput, global DB, Serverless v2 | E-commerce, SaaS, micro-services needing joins |
| **Managed Relational** | Amazon RDS (MySQL, PostgreSQL, MariaDB, SQL Server, Oracle) | Lift-and-shift SQL with Multi-AZ & read replicas | Legacy apps, 3-tier web stacks |
| **Key-Value / Document** | Amazon DynamoDB | Single-digit-ms latency, unlimited scale, pay-per-request | Gaming state, IoT device registry, e-commerce carts |
| **In-Memory Cache** | Amazon ElastiCache (Redis & Memcached) | Sub-millisecond cache, managed failover & backups | Session stores, leaderboard, API throttle counters |
| **Durable In-Memory DB** | Amazon MemoryDB for Redis | Micro-second reads **plus** Multi-AZ durability | Financial tick data, high-speed micro-services |
| **Document DB** | Amazon DocumentDB (MongoDB-compatible) | JSON-centric API, elastic storage, change streams | CMS, user profiles, product catalogs |
| **Wide-Column** | Amazon Keyspaces (Apache Cassandra) | Serverless Cassandra, per-request billing | High-volume IoT, time-bucketed logs |
| **Graph** | Amazon Neptune | Gremlin/SPARQL traversal at scale | Social networks, fraud graphs, knowledge graphs |
| **Time Series** | Amazon Timestream | Serverless ingest & tiered storage, SQL-like queries | IoT sensor metrics, DevOps telemetry |
| **Ledger** | Amazon QLDB | Cryptographically verifiable immutable history | Compliance, supply-chain, finance audit |
| **Analytics Warehouse** | Amazon Redshift (+ Serverless) | Columnar OLAP, RA3 & Spectrum | BI dashboards, clickstream analytics |
| **Search / Vector** | Amazon OpenSearch Service | Text + k-NN vector search, log analytics | Chatbots (RAG), log explorer, product search |

> **Edge accelerator**: **Amazon DAX** adds a write-through cache layer to DynamoDB, dropping read latency to microseconds while preserving API calls.

---

## Deep-Dive on Key Services

### Amazon DynamoDB
* **Fully managed NoSQL**, millisecond latency, virtually unlimited throughput.  
* Billing modes: _Provisioned_ (WCU/RCU) or _On-Demand_.  
* Advanced features: Global Tables (multi-Region active/active), Streams + Lambda for event-driven patterns, DAX for caching, point-in-time recovery (PITR).

### Amazon ElastiCache
* Managed **Redis** and **Memcached** clusters.  
* Handles node replacement, patching, backups, Multi-AZ failover.  
* Choose Redis for persistence, pub/sub, and rich data types; Memcached for simple sharding and ephemeral caching.

### Amazon MemoryDB for Redis
* **Redis-compatible primary database** — micro-second reads **and** Multi-AZ durability.  
* Eliminates dual-layer (cache + durable store) complexity in latency-sensitive apps.

### Amazon DocumentDB
* MongoDB 3.6/4.0/5.0 API compatibility.  
* Elastic storage up to 64 TB per cluster, six-way replicated.  
* Ideal for semi-structured JSON, content management, and quick migrations from on-prem Mongo.

### Amazon Keyspaces
* Serverless Cassandra with the same **CQL** drivers.  
* Per-request billing, zero-ops scaling, PITR restore.  
* Great for write-heavy telemetry with predictable partition keys.

### Amazon Neptune
* Purpose-built **graph** database, supporting Gremlin, openCypher and SPARQL.  
* ~1-ms traversal latency, ACID compliance, optional Serverless mode.  

### Amazon Timestream
* Uses **magnetic + memory** tiers, automatic data lifecycle.  
* Up to 1,000× faster and 1/10 the cost of relational alternatives for time-series queries.  

### Amazon QLDB
* Centralized, immutable ledger (not blockchain).  
* Each commit produces a cryptographic hash linked in a Merkle tree—built-in `getDigest()` and `verify()` APIs.  

---

## Decision Heuristics
1. **Key-value lookups at any scale?** → DynamoDB.  
2. **Need SQL joins & strong consistency?** → Aurora or RDS.  
3. **Highly connected data?** → Neptune.  
4. **Immutable audit trail?** → QLDB (centralized) or Managed Blockchain (decentralized).  
5. **Millisecond writes + microsecond reads?** → MemoryDB.  
6. **Analytics over TB-PB of columnar data?** → Redshift.  

---

## Serverless & Scalability Patterns
* **DynamoDB On-Demand**, **Aurora Serverless v2**, **Redshift Serverless**, **Neptune Serverless**, and **Keyspaces on-demand I/O** all scale to zero—perfect for spiky or dev/test workloads.  
* Pair **DynamoDB Streams → Lambda** to create event-driven pipelines without polling.  
* **DAX** or **ElastiCache** close the micro-latency gap if sub-millisecond reads are required.  

---

## Security & Cost Optimization Quick-Hits
* Default encryption at rest via **AWS KMS** across all engines; choose **customer-managed keys (CMKs)** for stricter audit.  
* Restrict traffic with **VPC endpoints** (Gateway for DynamoDB/S3, Interface for others) to keep data off the public internet.  
* Lower steady-state cost via **Reserved Instances** (RDS/ElastiCache) or **Savings Plans** (Aurora).  
* Use **DynamoDB Auto-Scaling** alarms and on-demand mode to avoid over-provisioning WCU/RCU.  

---

## Best Practices
* **Model access patterns first**, then design the schema (especially for DynamoDB and Keyspaces).  
* Deploy **Multi-AZ** or **Global Database** for HA; test failovers.  
* Enable **backups/PITR** and regularly rehearse restores.  
* Encrypt data in transit with TLS, authenticate via IAM or SigV4 where supported.  
* Monitor with **CloudWatch**—look at `ThrottledRequests`, `ReplicationLatency`, or `Evictions` depending on engine.  

---

## SAA-C03 Critical Analysis
**Strengths in this guide**  
* Covers every purpose-built engine listed in the official exam blueprint.  
* Emphasizes “pick the right tool” decisions—core of scenario-based questions.  
* Highlights DynamoDB billing vs. RDS instance-hour contrast, a common cost-optimization angle.

**Gaps you must still master**  
| Domain | Missing Depth |
|--------|---------------|
| Relational HA | Aurora Global DB RPO/RTO, RDS Blue/Green, automated backups |
| DynamoDB | GSI vs. LSI design, Adaptive Capacity math, Streams filtering |
| Redshift | RA3 vs. DC node types, Concurrency Scaling, Spectrum pricing |
| Security | IAM auth vs. DB native users, Encrypt-in-transit options per engine |
| Cost | RI vs. Savings Plans vs. Serverless thresholds across engines |
| Migration | DMS task modes, SCT conversion, snapshot import/export |

**Exam-day Checklist**  
1. Know **Global Tables** replication lag caveats.  
2. Recall **ElastiCache Redis vs. Memcached** differences (persistence, clustering, eviction).  
3. Distinguish **QLDB vs. DynamoDB Streams** for audit trails.  
4. Identify when **Aurora Serverless v2** saves cost for dev/test or spiky prod.  
5. Recognize **Timestream** vs. **DynamoDB TTL + S3 export** for time-series retention.

---

## Further Study & Labs
* **Hands-on**:  
  * Create a DynamoDB Global Table, simulate failover.  
  * Spin up Aurora Serverless v2, run `sysbench` to watch auto-scaling.  
  * Ingest IoT data into Timestream and visualize in QuickSight.  
* **AWS Digital Courses**: “Exam Readiness: Solutions Architect – Associate.”  
* **Whitepapers / Guides**:  
  * “Choosing the Right Database on AWS”  
  * “AWS Well-Architected Framework – Data Management Lens”  
* **Challenge Architecture**: Combine DynamoDB, Timestream, and Neptune for a fleet-management app; sketch DR and cost controls.

---

## References
- AWS Cloud Databases product page: https://aws.amazon.com/products/databases/  
- AWS Documentation linked inline per service.  

---

## Changelog
| Date (YYYY-MM-DD) | Version | Change Description | Source |
|-------------------|---------|--------------------|--------|
| 2025-06-11 | 1.1 | Added portfolio table, serverless patterns, security/cost section, SAA-C03 critical analysis | Internal notes + AWS docs |
| 2025-06-04 | 1.0 | Initial purpose-built databases content & service summaries | Employee Directory lesson |

