# Amazon EC2 Auto Scaling  
*High-availability, cost-efficient elasticity for every workload – and everything you need for the AWS SAA-C03 exam.*

---

## Table of Contents
1. [Introduction](#introduction)  
2. [Why Auto Scaling? Traditional vs. Cloud](#why-auto-scaling-traditional-vs-cloud)  
3. [Vertical vs. Horizontal Scaling](#vertical-vs-horizontal-scaling)  
4. [Core Components](#core-components)  
   4.1 [Launch Templates](#launch-templates)  
   4.2 [Auto Scaling Groups (ASGs)](#auto-scaling-groups-asgs)  
   4.3 [Scaling Policies](#scaling-policies)  
5. [Advanced Mechanics](#advanced-mechanics)  
   5.1 [Fleet Management](#fleet-management)  
   5.2 [Lifecycle Hooks & Warm Pools](#lifecycle-hooks--warm-pools)  
   5.3 [Mixed-Instance & Purchase-Option Strategies](#mixed-instance--purchase-option-strategies)  
   5.4 [Instance Refresh & Rolling Updates](#instance-refresh--rolling-updates)  
   5.5 [Capacity Rebalancing for Spot](#capacity-rebalancing-for-spot)  
6. [ELB Integration & Health Checks](#elb-integration--health-checks)  
7. [Operational Best Practices](#operational-best-practices)  
8. [Cost-Optimization Guide](#cost-optimization-guide)  
9. [SAA-C03 Exam Tips & Corner-Case Gotchas](#saa-c03-exam-tips--corner-case-gotchas)  
10. [Hands-On: End-to-End Setup Walkthrough](#hands-on-end-to-end-setup-walkthrough)  
11. [References](#references)  
12. [Changelog](#changelog)  

---

## Introduction
Amazon EC2 Auto Scaling (ASG) continuously monitors your instances and adjusts capacity to **meet real-time demand at the lowest possible cost**, while automatically replacing unhealthy instances. The service is **free; you pay only for the EC2 capacity that actually runs**.

---

## Why Auto Scaling? Traditional vs. Cloud
| Model | Peak-Capacity Provisioning | Off-Peak Cost | Spike Handling | Admin Overhead |
|-------|---------------------------|--------------|---------------|----------------|
| On-Prem / Static Cloud | Buy servers for Black-Friday load; idle most of the year | High (idle hardware) | Manual or impossible | Rack & patch forever |
| **EC2 + Manual Start/Stop** | Pay-as-you-go, but ops must predict and click | Better, still error-prone | Human latency ⇒ outages | Shift-based babysitting |
| **EC2 Auto Scaling** | Scales **just-in-time** by metrics, schedules, or ML forecasts | Pay only for running instances | Real-time elasticity | Set-and-forget |

---

## Vertical vs. Horizontal Scaling
| Dimension | Vertical (“scale-up”) | Horizontal (“scale-out”) |
|-----------|----------------------|--------------------------|
| **How** | Stop instance → pick larger type → start | Add more identical instances |
| Disruption | Yes (stop/start) | None (online) |
| Limit | Capped at biggest instance | Practically unlimited* |
| Automation | Poor | Excellent (Auto Scaling) |
| Use when | Quick experiment, legacy licensing | Stateless / modern web & microservices |

\* Default ASG quota: 2 000 instances per ASG, raise via Service Quotas.

---

## Core Components

### Launch Templates
**Answer:** *“What does each new instance look like?”*

Essential parameters  
```text
AMI ID • Instance type • Key pair • Security groups
IAM role • User data • (Optional) EBS mapping, tags
```

Key facts  
* Versioning supports safe rollback (`DefaultVersion`, `LatestVersion`).  
* Clone from a running instance, an older template, or start from scratch.  
* Prefer templates; **launch configurations are legacy** (no versioning, no new features).

---

### Auto Scaling Groups (ASGs)
**Answer:** *“Where do instances launch, and how many?”*

| Setting | Purpose |
|---------|---------|
| **VPC & Subnets** | Pick **≥ 2 subnets in distinct AZs** for HA. |
| **Min / Desired / Max** | Hard floor, current target, and cost ceiling. |
| **Purchase Options** | 100 % On-Demand, 100 % Spot, or a weighted mix. |
| **Health Check Type** | `EC2` or `ELB`; grace period defaults to 300 s. |
| **Termination Policy** | OldestInstance (default) \| ClosestToNextHour \| AllocationStrategy \| etc. |
| **Instance Protection** | Flag critical nodes to survive scale-in. |

---

### Scaling Policies
**Answer:** *“When do we scale?”*

| Policy Type | Trigger | Cooldown Handling | Typical Use |
|-------------|---------|-------------------|-------------|
| **Manual** | Human/CLI/CFN | N/A | One-off ops |
| **Scheduled** | `cron` or `at` time | N/A | Predictable office hours |
| **Simple** | 1 alarm ➜ 1 action | Global cooldown | Legacy |
| **Step** | Alarm tiers (e.g., +2 @ 80 %, +4 @ 95 %) | `EstimatedInstanceWarmup` per step | Burst workloads |
| **Target Tracking** | Thermostat (e.g., CPU 60 %, RequestCount = 1 000) | Auto cooldown | Most workloads |
| **Predictive** | ML forecast (≥ 24 h data) | Pre-warm capacity | Cyclical traffic |

---

## Advanced Mechanics

### Fleet Management
* Continuous health-checking → replace failed nodes automatically.  
* **Cross-AZ rebalancing** keeps capacity even if one AZ fails.  
* **Group metrics** (`GroupInServiceInstances`, etc.) visible in CloudWatch.

### Lifecycle Hooks & Warm Pools
```ascii
Pending:Wait ──> custom bootstrap ──> CompleteLifecycleAction
Terminating:Wait ──> drain & copy logs ──> CompleteLifecycleAction
```
* Hooks publish to SNS / SQS / EventBridge.  
* **Warm Pools** keep pre-initialised instances in `Stopped` or `Running` state for lightning-fast scale-out (billed at reduced or full rate).

### Mixed-Instance & Purchase-Option Strategies
```jsonc
"InstancesDistribution": {
  "OnDemandPercentageAboveBaseCapacity": 20,
  "SpotAllocationStrategy": "capacity-optimized",
  "SpotInstancePools": 4
},
"Overrides": [
  { "InstanceType": "t3.micro", "WeightedCapacity": 1 },
  { "InstanceType": "t3a.micro", "WeightedCapacity": 1 }
]
```
* **Capacity Weights** let a `c6g.large` (4 vCPU) satisfy 2 × “2-vCPU units”.  
* **Spot + On-Demand** blend lowers cost; enable `CapacityRebalance` for 2-minute Spot eviction notice handling.

### Instance Refresh & Rolling Updates
* Trigger by bumping launch-template version or via API/CFN/CDK.  
* Controls: `MinHealthyPercentage`, `SkipMatching`, `MaxBatchSize`.  
* Requires `Max` ≥ `Desired` + batch to avoid capacity deficit.

---

## ELB Integration & Health Checks
1. Attach ASG to one or more target groups.  
2. New instance → **register** → must pass ALB/NLB health checks before traffic flows.  
3. If `HealthCheckType = ELB`, a failed ALB check marks the instance unhealthy and ASG replaces it.  
4. **Align timers**  
   * ALB `deregistration_delay` (default 300 s)  
   * ASG `HealthCheckGracePeriod` & `Cooldown`  
   to prevent connection drops during scale-in.

---

## Operational Best Practices
* Use **detailed monitoring (1-min)** for quicker reactions.  
* Tag ASG and instances for cost & ops visibility (`aws:autoscaling:groupName`).  
* Raise **quotas early**: 20 ASGs/Region, 2 000 instances/ASG.  
* Protect critical boxes with **Instance Protection** or move them to **Standby** during maintenance.  
* **SuspendProcesses** (`AZRebalance`, `Launch`, `Terminate`) when performing disruptive ops.  
* Store session state off-box (DynamoDB, ElastiCache) – Auto Scaling kills stickiness.  
* Test **scale-in** as hard as scale-out. Thrashing = 502s and angry users.

---

## Cost-Optimization Guide
1. **Right-size first** → then scale-out.  
2. Blend **Spot (≈ 70 % savings)** with On-Demand, add **Savings Plans** for baseline.  
3. Cap runaway spend with a sane **Max capacity**.  
4. Predictive scaling + warm pools beats over-provisioning for heavy-startup apps.  
5. Instance Refresh + new AMI with performance tuning can lower fleet size.

---

## SAA-C03 Exam Tips & Corner-Case Gotchas
> Memorise these; they show up **a lot**.

| Topic | What AWS Loves to Ask | Quick Answer |
|-------|----------------------|--------------|
| **Lifecycle hook before receiving traffic** | “Run a script to download huge model before instance is InService” | `Pending:Wait` hook + `CompleteLifecycleAction` |
| **Gracefully drain Spot** | “2-minute notice, avoid dropped requests” | Enable `CapacityRebalance` + `Terminating:Wait` hook |
| **Preserve node during patch window** | “Keep instance even if scale-in triggers” | `InstanceProtectionFromScaleIn=true` |
| **Update AMI with zero downtime** | “Rolling replace across ASG” | `Instance Refresh` |
| **Cron-based traffic wave** | “Scale to 10 at 08:00 every weekday” | Scheduled action |
| **Cross-region DR** | “Fail-over to us-west-2 only during disaster” | Duplicate ASG with `Desired=0`, adjust Route 53 |
| **Stop flapping** | “Scale-in/out every minute” | Increase cooldown / warm-up, or use target tracking (built-in dampening) |
| **Stateful legacy app** | “Session data lost after scale-in” | Externalise session store OR ALB sticky cookies + high Min capacity |
| **Limit exam trick** | `Desired` below `Min` in CFN → stack fails | Keep `Desired ≥ Min` |

---

## Hands-On: End-to-End Setup Walkthrough

> **Prerequisites:** AWS account, IAM role with `AmazonEC2AutoScalingFullAccess`, existing ALB, VPC with two public subnets.

```bash
# 1. Build AMI (packer or manual) …

# 2. Create Launch Template
aws ec2 create-launch-template \
  --launch-template-name app-launch-template \
  --version-description "v1" \
  --launch-template-data '{
      "ImageId":"ami-0123456789abcdef0",
      "InstanceType":"t3.micro",
      "SecurityGroupIds":["sg-0abc12def3456789"],
      "IamInstanceProfile":{"Arn":"arn:aws:iam::123456789012:instance-profile/AppRole"},
      "UserData":"...base64..."
  }'

# 3. Create Auto Scaling Group
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name app-asg \
  --launch-template 'LaunchTemplateName=app-launch-template,Version=1' \
  --min-size 2 --desired-capacity 2 --max-size 4 \
  --vpc-zone-identifier "subnet-0a123...,subnet-0b456..." \
  --target-group-arns arn:aws:elasticloadbalancing:...

# 4. Add Target-Tracking Policy (CPU 60 %)
aws autoscaling put-scaling-policy \
  --auto-scaling-group-name app-asg \
  --policy-name cpu60 \
  --policy-type TargetTrackingScaling \
  --target-tracking-configuration '{
      "PredefinedMetricSpecification":{"PredefinedMetricType":"ASGAverageCPUUtilization"},
      "TargetValue":60,
      "EstimatedInstanceWarmup":300
  }'
```

Stress-test with `stress --cpu 2 --timeout 600` or your app’s built-in button, watch CloudWatch → **new instances launch**, ALB shows healthy, CPU avg drops, ASG scales-in after load subsides.

---

## References
* AWS Docs – *What Is Amazon EC2 Auto Scaling*  
  https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html  
* AWS User Guide – *Create an ASG using a Launch Template*  
  https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html  
* AWS User Guide – *Target Tracking Policies*  
  https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html  
* AWS User Guide – *Step & Simple Policies*  
  https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-simple-step.html  
* AWS User Guide – *ASG Capacity Limits*  
  https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-capacity-limits.html  
* AWS FAQs – Amazon EC2 Auto Scaling  
  https://aws.amazon.com/ec2/autoscaling/faqs/  

---

## Changelog
| Date (UTC) | Version | Change Description |
|------------|---------|--------------------|
| 2025-06-12 | 1.0 | Initial comprehensive guide, transcript integration, critical-analysis additions. |

---
