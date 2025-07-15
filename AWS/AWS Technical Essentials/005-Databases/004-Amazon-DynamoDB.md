# Amazon DynamoDB – Exam-Ready Study Guide (SAA-C03)

A comprehensive, **SAA-C03-aligned** reference for mastering Amazon DynamoDB.  
This file merges instructional video notes, AWS documentation, and targeted exam insights into one unified guide.

---

## Table of Contents
1. [Introduction](#introduction)  
2. [Core Components](#core-components)  
3. [Key Capabilities](#key-capabilities)  
4. [Step-by-Step Setup (Console & CLI)](#step-by-step-setup-console--cli)  
5. [Performance & Scaling Design](#performance--scaling-design)  
6. [Availability, Backup & DR](#availability-backup--dr)  
7. [Security & Compliance](#security--compliance)  
8. [Cost Optimization](#cost-optimization)  
9. [Typical SAA-C03 Exam Patterns](#typical-saa-c03-exam-patterns)  
10. [Critical Analysis vs. SAA-C03 Blueprint](#critical-analysis-vs-saa-c03-blueprint)  
11. [Best Practices Checklist](#best-practices-checklist)  
12. [References](#references)  
13. [Changelog](#changelog)

---

## Introduction
Amazon DynamoDB is a **fully managed, serverless, key-value and document NoSQL database** that delivers single-digit-millisecond latency at any scale. AWS handles infrastructure provisioning, patching, replication, and auto-scaling so architects can focus on schema design, access patterns, and cost controls.

### Why DynamoDB for Architects?
* Eliminates undifferentiated heavy lifting (no servers or OS to manage).  
* Scales seamlessly from “one item” to **tens of terabytes** per table.  
* Built-in multi-AZ durability and encryption make it a secure default.  
* Purpose-built for **OLTP, low-latency, and high-concurrency** workloads.

---

## Core Components
| Component | Analogy (RDBMS) | Notes |
|-----------|-----------------|-------|
| **Table** | Database table | Unlimited items; billing & capacity set here. |
| **Item**  | Row / Record   | JSON-like; size ≤ 400 KB. |
| **Attribute** | Column / Field | Scalar, set, or document. |
| **Primary Key** | PK / PK + SK | – Partition (hash) key — mandatory  <br>– Sort (range) key — optional |
| **Secondary Indexes** | Non-PK indexes | – Local Secondary Index (LSI)  <br>– Global Secondary Index (GSI) |

---

## Key Capabilities
* **Read/Write Capacity Modes**  
  * _Provisioned_ (with Auto Scaling)  
  * _On-Demand_ (pay-per-request; zero capacity planning)  
* **Global Tables v2** – Multi-Region active-active replication.  
* **Streams** – Change-data-capture for Lambda, Kinesis, EventBridge.  
* **DAX** – Managed, write-through, in-memory cache for sub-millisecond reads.  
* **Point-in-Time Recovery (PITR)** – 35-day continuous backup window.  
* **S3 Import/Export** – Bulk data movement without custom scripts.

---

## Step-by-Step Setup (Console & CLI)

### 1 – Create a Table in the Console
1. Navigate to **DynamoDB → Tables → Create table**.  
2. Enter **Table name**: `Employees`.  
3. Define **Partition key**: `EmployeeID` (String).  
4. Accept default settings (on-demand, encryption at rest).  
5. Create.

### 2 – Insert an Item (Console)
```json
{
  "EmployeeID": "E-0001",
  "FirstName" : "Peterson",
  "LastName"  : "Santos",
  "Dept"      : "Engineering"
}
```

### 3 – Same Flow via AWS CLI
```bash
aws dynamodb create-table \
  --table-name Employees \
  --attribute-definitions \
      AttributeName=EmployeeID,AttributeType=S \
  --key-schema AttributeName=EmployeeID,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
  --table-name Employees \
  --item '{
      "EmployeeID": {"S":"E-0001"},
      "FirstName":  {"S":"Peterson"},
      "LastName":   {"S":"Santos"},
      "Dept":       {"S":"Engineering"}
  }'
```

---

## Performance & Scaling Design
* **Partition Math**: 1 partition ≈ 10 GB or 3 000 RCUs / 1 000 WCUs.  
* **Adaptive Capacity** reallocates hot partitions automatically.  
* **Hot-Key Avoidance**: use high-cardinality partition keys or sharding suffixes.  
* **Auto Scaling Target**: default 70 % utilization; adjust for cost vs. latency.  
* **DAX**: micro-second response for read-heavy, eventually consistent patterns.

---

## Availability, Backup & DR
| Feature | Purpose | Notes |
|---------|---------|-------|
| **Multi-AZ storage** | Fault tolerance | Automatic; no extra cost. |
| **PITR** | 35-day restore window | Granular to seconds. |
| **On-Demand Backup / Restore** | Long-term archiving | Cross-account possible. |
| **Global Tables v2** | Multi-Region HA & locality | Active-active; last-writer-wins conflict resolution. |

---

## Security & Compliance
1. **Encryption at Rest** – AES-256 with AWS-managed (default) or customer-managed CMKs.  
2. **IAM** – Fine-grained access (`dynamodb:LeadingKeys`, IAM condition keys).  
3. **VPC Endpoints** – Gateway (data plane) or Interface (control plane) to keep traffic on AWS backbone.  
4. **CloudTrail** – Records all API calls; enable for compliance audits.  
5. **Conditional Writes & Optimistic Locking** – Prevent over-writes (e.g., shopping cart).

---

## Cost Optimization
* **On-Demand vs. Provisioned**:  
  * Spiky / unpredictable → _On-Demand_.  
  * Predictable steady rate → _Provisioned_ (+ Auto Scaling + Reserved Capacity ≈ 50 % savings).  
* **Storage**: Region-dependent (~$0.25 – $0.30/GB-month); use **TTL** to purge stale items.  
* **Streams & DAX** are optional metered features—enable only when architecture warrants.  

---

## Typical SAA-C03 Exam Patterns
| Scenario Type | DynamoDB Feature Commonly Tested |
|---------------|----------------------------------|
| **Serverless Session Store** | TTL, On-Demand mode |
| **Shopping Cart** | Conditional writes, parallel scan avoidance |
| **IoT Telemetry** | Write sharding, Kinesis → Streams processing |
| **Multi-Region Active-Active** | Global Tables, last-writer-wins |
| **Cost-Sensitive Burst Traffic** | Switching capacity modes |

---

## Critical Analysis vs. SAA-C03 Blueprint
| Domain (SAA-C03) | Topics Required | Status | Gap Resolution |
|------------------|-----------------|--------|----------------|
| **Design Resilient Architectures** | Multi-AZ durability, Global Tables, PITR, backup & restore | ✅ Covered | – |
| **Design High-Performing Architectures** | On-Demand vs. Provisioned, Auto Scaling, DAX, partition best-practices | ⚠ Partial | Added partition math, auto-scaling, DAX notes |
| **Design Secure Architectures** | IAM fine-grained access, KMS options, VPC endpoints, Streams auditing | ⚠ Partial | Added VPC endpoints, CMKs, Streams + CloudTrail |
| **Design Cost-Optimized Architectures** | TTL, Reserved Capacity, on-demand pricing comparisons | ⚠ Partial | Added TTL, reserved capacity, cost levers |

---

## Best Practices Checklist
- [x] Use high-cardinality **partition keys** to avoid hot partitions.  
- [x] Enable **Auto Scaling** or pick **On-Demand** for unpredictable workloads.  
- [x] Turn on **PITR** and schedule **on-demand backups** for compliance.  
- [x] Encrypt with **customer-managed CMKs** where regulatory control is required.  
- [x] Access DynamoDB via **VPC endpoints** to keep traffic inside AWS