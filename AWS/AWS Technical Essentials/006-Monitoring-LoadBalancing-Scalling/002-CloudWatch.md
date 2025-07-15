# Monitoring and Observability on AWS with Amazon CloudWatch  
*A SAA-C03-focused study guide built around the Employee Directory application.*

---

## Table of Contents  
1. [Introduction](#introduction)  
2. [Prerequisites](#prerequisites)  
3. [How CloudWatch Works](#how-cloudwatch-works)  
4. [Core CloudWatch Concepts](#core-cloudwatch-concepts)  
5. [Custom Metrics](#custom-metrics)  
6. [Dashboards](#dashboards)  
7. [CloudWatch Logs](#cloudwatch-logs)  
8. [CloudWatch Alarms](#cloudwatch-alarms)  
9. [Prevent & Troubleshoot with Alarms + Events](#prevent--troubleshoot-with-alarms--events)  
10. [Advanced Features & Exam-Ready Nuggets](#advanced-features--examready-nuggets)  
11. [Cost Optimization Cheat-Sheet](#cost-optimization-cheat-sheet)  
12. [Security & IAM Considerations](#security--iam-considerations)  
13. [Critical Analysis vs. SAA-C03 Blueprint](#critical-analysis-vs-saa-c03-blueprint)  
14. [References](#references)  
15. [Changelog](#changelog)  

---

## Introduction  
Amazon CloudWatch is AWS’s managed monitoring and observability service. It collects metrics, logs and events from virtually every AWS service (and on-premises servers), stores them in a single, durable plane, and lets you visualize and act on anomalies within seconds. In the Employee Directory sample workload we will:  

* Build a dashboard that plots EC2 `CPUUtilization`.  
* Create an alarm that fires when CPU > 60 % for five minutes and emails **CPU_Utilization_Topic**.  
* Explore advanced features—EventBridge rules, composite alarms, Contributor Insights, and more—to match the **AWS Certified Solutions Architect – Associate (SAA-C03)** exam blueprint.

---

## Prerequisites  
| Requirement | Why It Matters | Quick Check |
|-------------|----------------|-------------|
| AWS account with CloudWatch & SNS access | All exercises run in the console or CLI | Able to reach `https://console.aws.amazon.com/cloudwatch/` |
| IAM user/role with least-privilege policies for CloudWatch, Logs, SNS | Exam questions test IAM boundaries | Policy snippets in [Security](#security--iam-considerations) |
| Deployed workload (Employee Directory EC2 instance) | Generates the metrics you’ll plot | Instance ID noted |

---

## How CloudWatch Works  
1. **Collect** – AWS services publish metrics and logs automatically (one datapoint / 5 min = *basic monitoring*).  
2. **Monitor** – Visualize in dashboards, correlate logs + metrics, set alarms.  
3. **Act** – Alarms and EventBridge rules trigger SNS, Lambda, Auto Scaling, EC2 actions, etc.  
4. **Analyze** – 1 s high-resolution metrics, 15-month retention, Metric Math for on-the-fly calculations.  

Basic monitoring is free; **detailed monitoring** (1-minute granularity) and high-resolution custom metrics (1 s) incur cost. Detailed monitoring is often enough for SAA-C03 performance scenarios; know when to pay for it.

---

## Core CloudWatch Concepts  
| Term | Description | Example |
|------|-------------|---------|
| **Metric** | Time-series representing a variable to monitor. | `CPUUtilization` on an EC2 instance |
| **Dimension** | Name/value pair that scopes a metric. | `InstanceId=i-0123456789abcdef` |
| **Timestamp** | When the datapoint occurred. | `2025-06-12T14:05:00Z` |
| **Log Event** | Single record in CloudWatch Logs (`{timestamp, message}`) | Stack trace line |
| **Log Stream** | Sequence of log events from one resource. | All `/var/log/nginx/access.log` lines from a single EC2 |
| **Log Group** | Collection of streams sharing retention & permissions. | `/aws/elasticbeanstalk/emp-directory` |

Most AWS services publish a *default* metric set for free; you can always extend visibility with **custom metrics**.

---

## Custom Metrics  
When the platform does **not** emit an application KPI—page views, error rate, queue depth—you can `PutMetricData` yourself.  

```bash
aws cloudwatch put-metric-data \
  --namespace "EmployeeDirectory" \
  --metric-data '[
      { "MetricName":"PageViews",
        "Timestamp":"2025-06-12T14:05:00Z",
        "Unit":"Count",
        "Value":1
      }
  ]'
```

*High-resolution* custom metrics (`--storage-resolution 1`) allow 1-second granularity—ideal for latency-sensitive apps but billable per million datapoints.

Typical custom metrics for SAA-C03 scenarios:  
* Web page load time (ms)  
* HTTP 5xx error rate  
* Threads in JVM  
* Items processed per batch job  

---

## Dashboards  
Dashboards are shareable, Region-agnostic “single panes of glass.”  

1. **Create Dashboard → “mydashboard”.**  
2. Add a **Line** widget → *Metrics / EC2 / Per-Instance Metrics / CPUUtilization*.  
3. Optionally toggle *Live Data* to see < 1-min metrics.  
4. Add widgets from multiple Regions to get a global view.  

Dashboards can display: metrics, logs queries, alarms, text, or even embedded CloudWatch Logs Insights graphs. Access control is IAM-policy driven.

> You are not locked in—`GetMetricData` allows third-party visualization tools (Grafana, Datadog) to ingest CloudWatch metrics.

---

## CloudWatch Logs  
CloudWatch Logs centralizes log ingestion across EC2, Lambda, on-prem servers, and other AWS services.

* **Lambda** – Logs ship automatically when the function role has `logs:CreateLogGroup/Stream` & `logs:PutLogEvents`.  
* **EC2** – Install the unified CloudWatch Agent or the older Logs agent and point it at `/var/log/…`.  

Once in CW Logs you can:  

* **Query** with Logs Insights (`fields @timestamp, @message | filter @message like /Exception/`).  
* **Filter → Metric** (`put-metric-filter`) to convert log patterns into real CloudWatch metrics.  
* **Contributor Insights** rules to surface “top N” contributors (e.g., top callers causing 5xx errors).

---

## CloudWatch Alarms  
Alarms watch a metric (or metric ‑ math expression) and evaluate:  

* **Threshold** – e.g., CPU > 60 %.  
* **Period** – number of evaluation periods (e.g., 5 datapoints of 1 minute each).  
* **Statistic** – `Average`, `p90`, `Sum`, etc.  

States: **OK → ALARM → INSUFFICIENT_DATA**. Actions fire on state transitions:  

* **SNS Notification** – email/SMS/on-call tooling (`CPU_Utilization_Topic`).  
* **EC2 Action** – reboot, recover, stop, or terminate.  
* **Auto Scaling** – add/remove instances.  
* **EventBridge** – event fan-out to Step Functions, Kinesis, multiple Lambdas.

---

## Prevent & Troubleshoot with Alarms + Events  
End-to-end pattern for HTTP 500 errors:

1. **Metric Filter** – scan `/aws/elasticbeanstalk/.../nginx/error.log` for `" 500 "` → increment `HTTP500Count`.  
2. **Alarm** – `HTTP500Count ≥ 5` for two consecutive 1-minute periods.  
3. **Action** – SNS → PagerDuty, or SNS → Lambda auto-heal script.

For hands-off remediation, SNS can trigger a Lambda function that runs `aws elasticbeanstalk restart-app-server` or scales an Auto Scaling group.

---

## Advanced Features & Exam-Ready Nuggets  

| Feature | Exam Angle | Mini-Example |
|---------|------------|--------------|
| **EventBridge vs. Alarms** | When you need pattern matching beyond thresholds. | Rule matching only the **ALARM** state change JSON. |
| **Composite Alarms** | Suppress noise; cost optimisation. | `HighCPU` **AND** `HTTP500s` must both fire. |
| **Metric Math** | Build on-the-fly ratios/aggregates. | `ErrorRate = (5XX / Requests) * 100`. Alarm on `ErrorRate > 5`. |
| **Contributor Insights** | Identify top N offenders quickly. | “Top 5 IPs by rejected connections in VPC Flow Logs.” |
| **Cross-Account Dashboards & Logs Subscriptions** | Organizations scenario. | Share prod metrics into a central Observability account. |
| **CloudWatch Agent & Container/Lambda Insights** | Memory & disk not emitted by default. | CloudWatch Agent config: `mem_used_percent`. |

---

## Cost Optimization Cheat-Sheet  

| Item | Free Tier | Beyond Free (us-east-1) | Save Money By… |
|------|-----------|-------------------------|----------------|
| Basic service metrics | Unlimited | \$0 | N/A |
| Detailed & custom metrics | 10 metrics | \$0.30 / metric-month | Only enable on prod, aggregate multiple dims |
| Hi-res metrics (≤ 1 s) | Counts in quota | + \$0.01 / 1 M datapoints | Downsample to 1-min when alerts allow |
| Logs ingestion | 5 GB | \$0.50 / GB | Filter at agent; ship less verbose logs |
| Logs retention | 5 GB-months | \$0.03 / GB-month | Export to S3 Glacier after 30 days |
| Standard alarms | 10 | \$0.10 / alarm-month | Combine via composite alarms |
| Composite alarms | – | \$0.50 / alarm-month | Only where suppression needed |
| GetMetricData API | 1 M calls | \$0.01 / 1 K calls | Cache dashboard queries |

---

## Security & IAM Considerations  

* **Encryption at rest** – CloudWatch Logs encrypts data automatically; attach a **customer-managed KMS key** for full control:  
  `aws logs associate-kms-key --log-group-name MyGroup --kms-key-id arn:aws:kms:…`  
* **Encryption in transit** – All CW APIs default to TLS; VPC Interface Endpoints keep traffic inside AWS.  
* **Least-Privilege Dashboard Viewer**  

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
        "cloudwatch:GetMetricData",
        "cloudwatch:ListDashboards",
        "cloudwatch:GetDashboard"
    ],
    "Resource": "*"
  }]
}
```  

* **Cross-Account Observability** – Enable in each account, then grant `cloudwatch:Describe*` to the monitoring account role.

---

## Critical Analysis vs. SAA-C03 Blueprint  

| Domain | Strengths in This Guide | Remaining Watch-outs |
|--------|------------------------|----------------------|
| **Design Resilient Architectures** | Cross-Region dashboards, EventBridge auto-healing patterns. | Practise Route 53 health checks → CW alarms for fail-over. |
| **Design High-Performing Architectures** | Hi-res metrics, Metric Math, anomaly mention. | Try **Anomaly Detection** band setup question. |
| **Secure Architectures** | IAM snippets, KMS encryption, VPC endpoints. | Add SCP & org-wide guardrails practice. |
| **Cost-Optimized Architectures** | Detailed pricing table, composite alarms for consolidation. | Quiz yourself on free vs. paid *Logs Insights* query costs. |

Verdict: the module is now “exam-tight”—only minor edge-case practice remains (e.g., X-Ray + ServiceLens questions).

---

## References  
* Amazon CloudWatch – Product Page <https://aws.amazon.com/cloudwatch/>  
* Amazon CloudWatch Pricing <https://aws.amazon.com/cloudwatch/pricing/>  
* Getting Started with Amazon CloudWatch (User Guide) <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/GettingStarted.html>  
* What Is Amazon CloudWatch Logs? <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html>  
* AWS Services That Publish CloudWatch Metrics <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html>  
* View Available Metrics <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/viewing_metrics_with_cloudwatch.html>  
* Amazon Simple Notification Service (SNS) <https://aws.amazon.com/sns/>  
* AWS Technical Essentials – Part 2 (Metric Filters example) <https://explore.skillbuilder.aws/.../aws-technical-essentials-part-2>

---

## Changelog  

| Date (BST-3) | Version | Change |
|--------------|---------|--------|
| 2025-06-12 | 1.1 | Added EventBridge vs. alarms, composite alarms, Contributor Insights, security, cost table, critical analysis. |
| 2025-06-11 | 1.0 | Initial CloudWatch walkthrough (dashboards, alarms, logs, metrics). |
