# Traffic Routing with AWS Elastic Load Balancing  
*A complete, exam-ready guide for SAA-C03 candidates*

---

## Table of Contents
1. [Introduction](#introduction)  
2. [Prerequisites](#prerequisites)  
3. [ELB Fundamentals](#elb-fundamentals)  
4. [Types of Load Balancers](#types-of-load-balancers)  
   1. [Classic Load Balancer (CLB)](#classic-load-balancer)  
   2. [Application Load Balancer (ALB)](#application-load-balancer)  
   3. [Network Load Balancer (NLB)](#network-load-balancer)  
   4. [Gateway Load Balancer (GWLB)](#gateway-load-balancer)  
5. [ELB Core Components](#elb-core-components)  
6. [Health Checks](#health-checks)  
7. [Cross-Zone Load Balancing](#cross-zone-load-balancing)  
8. [Security & TLS](#security--tls)  
9. [Monitoring, Logging & Cost](#monitoring-logging--cost)  
10. [Global & Hybrid Architectures](#global--hybrid-architectures)  
11. [Decision Flowchart](#decision-flowchart)  
12. [Comparison Matrix](#comparison-matrix)  
13. [Critical Analysis & Exam Gotchas](#critical-analysis--exam-gotchas)  
14. [References](#references)  
15. [Changelog](#changelog)  

---

## Introduction
Elastic Load Balancing (ELB) is AWS’s fully-managed service that automatically distributes incoming application traffic across multiple targets—Amazon EC2 instances, containers, IP addresses, and AWS Lambda functions. It removes the undifferentiated heavy lifting of running, scaling, and securing your own load-balancing software while adding deep integrations with Auto Scaling, AWS WAF, AWS Shield, and AWS Certificate Manager (ACM).

---

## Prerequisites
| Requirement | Notes |
|-------------|-------|
| AWS account with administrative privileges | IAM least-privilege recommended for production |
| VPC with at least two public and two private subnets (multi-AZ) | Enables high availability |
| EC2 Auto Scaling group (optional but typical) | For dynamic fleet management |
| **IAM** permissions for ELB, ACM, WAF, CloudWatch, Route 53 | Needed for full feature set |
| TLS certificate in **ACM** (for HTTPS/TLS listeners) | Free public certs or imported |

---

## ELB Fundamentals
* Load balancer is **regional**, highly available, and scales automatically.  
* Sits **in-path**: client ↔ **ELB** ↔ target.  
* Default algorithm: **Round-Robin** (ALB & CLB) or **Flow Hash** (NLB & GWLB).  
* ELB works hand-in-glove with Auto Scaling: connection draining (now “deregistration delay”) ensures in-flight requests finish before instance termination.

---

## Types of Load Balancers

### Classic Load Balancer
Legacy Layer-4/7 option. Only appears in migration scenarios:
* HTTP/HTTPS + TCP listeners.
* Limited feature set (no HTTP/2, no advanced routing, no WAF).
* Cross-zone LB **off by default**; enable to distribute evenly.
* Idle timeout 60 s (editable).  
**Migration tip:** Move to ALB/NLB for modern workloads.

---

### Application Load Balancer
* **Layer 7** (HTTP/HTTPS, HTTP/2, gRPC).  
* Advanced routing: host-based, path-based, header, method, query-string, **weighted** target groups (canary/blue-green).  
* **User authentication**: OIDC/SAML/LDAP via Cognito or directly.  
* **Fixed responses** & redirects (e.g., force HTTP→HTTPS).  
* **TLS offload** with **ACM** (supports **SNI** up to 25 certs per listener).  
* **WAF** & **Shield** integration.  
* Supports **sticky sessions** (ALB cookie).  
* **Cross-zone** always on & **free**.

---

### Network Load Balancer
* **Layer 4** (TCP, UDP, TLS).  
* Ultra-low latency, millions of requests per second.  
* Preserves **source IP** (useful for security appliances & logging).  
* **Static IP** per AZ; optional **Elastic IP** for whitelisting.  
* Supports TLS termination or pass-through.  
* Cross-zone **off by default**—turning it on may incur inter-AZ data charges.  
* PrivateLink & API Gateway **VPC Link** target type.  
* Stickiness via **source IP** algorithm.

---

### Gateway Load Balancer
* **Layer 3 gateway** + Layer 4 LB for third-party virtual appliances (firewalls, IDS/IPS, DPI).  
* Distributes flows symmetrically across appliance fleet.  
* Deploy appliances rapidly via AWS **Marketplace** AMIs.  
* Requires **GWLB Endpoints** (powered by PrivateLink) to insert in traffic path.  
* Flow stickiness: 5-tuple or 3-tuple hashing.  
* Cross-AZ resilience; appliance health checks built-in.

---

## ELB Core Components
| Component | Purpose | Key Exam Notes |
|-----------|---------|----------------|
| **Listener** | Front-door port/protocol (e.g., HTTPS 443) | One LB can have many listeners |
| **Target Group** | Collection of targets (EC2, IP, Lambda, ALB) + health check | Attributes: slow-start, deregistration delay |
| **Rule** | Condition → action mapping evaluated in priority order | ALB supports forward, fixed-response, redirect |

---

## Health Checks
| Mode | Works With | Parameters |
|------|------------|------------|
| **TCP** | Any LB type | Success if 3-way handshake completes |
| **HTTP/HTTPS** | ALB/NLB/CLB | Configurable path (e.g., `/monitor`), success codes, interval (5–30 s), healthy/unhealthy thresholds |
| **gRPC** | ALB (gRPC target groups) | Status = `SERVING` |

**Best Practice:** Create an app-level endpoint (`/healthz` or `/monitor`) that checks DB, S3, downstream services—don’t just test “port open”.

---

## Cross-Zone Load Balancing
| LB Type | Default | Cost Implications | CLI Flag |
|---------|---------|-------------------|----------|
| **ALB** | ON | Free | `load_balancer_attributes CrossZoneLoadBalancing.enabled=true` |
| **NLB** | OFF | Turning **ON** can add inter-AZ data charges | Same attribute |
| **GWLB** | N/A (flows are zonal) | — | — |

Turn **off** on NLBs that front zonal stateful services (e.g., cache) to save inter-AZ cost.

---

## Security & TLS
* **ACM** issues free public certs; import private certs if needed.  
* **SNI**: ALB chooses the proper cert during TLS handshake.  
* **TLS Policies**: Choose modern cipher suites (e.g., `ELBSecurityPolicy-TLS13-1-2-2021-06`).  
* **AWS WAF** attaches only to ALB, CLB, and CloudFront—**not** NLB/GWLB.  
* **AWS Shield Advanced** optional for DDoS cost protection on public ALBs/NLBs.  
* **Security Groups**:  
  * ALB/CLB: SG required.  
  * NLB: SG only when using TLS listener; otherwise none.  
  * GWLB: no SG; relies on appliance SGs.

---

## Monitoring, Logging & Cost
| Feature | ALB | NLB | GWLB |
|---------|-----|-----|------|
| **Access Logs** | gzip to S3 every 5 min; 60+ fields | Flow-log style (JSON) | — |
| **CloudWatch Metrics** | `RequestCount`, `TargetResponseTime` | `ActiveFlowCount`, `NewFlowCount` | `HealthyHostCount` |
| **X-Ray** | ALB only | — | — |
| **Idle Timeout** | 60 s default, 1–4 000 s | 350 s default, 10–4000 s | — |
| **Cost Unit** | LCU = new + active + processed bytes + rule evals | NLCU = connections + bytes | GWLCU = processed bytes |

---

## Global & Hybrid Architectures
* **Route 53** + health checks → fail-over ALBs across Regions.  
* **AWS Global Accelerator** provides static Anycast IPs, IPv6 dual-stack, global TCP/UDP acceleration → regional ALBs/NLBs.  
* **Hybrid**: ALB/NLB target type **IP** = on-premise servers through Direct Connect / VPN.

---

## Decision Flowchart
```text
                         ┌──────────────────────────────┐
                         │       Start: Workload        │
                         └──────────────┬───────────────┘
                                        │
          ┌─────────────────────────────┴───────────────────────────┐
          │Need Layer-7 routing, WAF, auth, redirects, gRPC/HTTP2? │
          └─────────────┬───────────────────────────────────────────┘
                        │Yes                                       │No
                        │                                          │
          ┌─────────────▼─────────────┐                ┌───────────▼─────────────┐
          │Need inline security       │                │Need ultra-low latency,  │
          │appliances (firewall/IPS)? │                │static IP, TCP/UDP, TLS? │
          └─────────────┬─────────────┘                └───────────┬─────────────┘
                        │Yes                                   │Yes              │No
                        │                                      │                 │
        ┌───────────────▼──────────────┐       ┌───────────────▼──────────────┐  │
        │ Gateway Load Balancer (GWLB) │       │ Network Load Balancer (NLB)  │  │
        └──────────────────────────────┘       └──────────────────────────────┘  │
                                                                                 │
                                                  ┌──────────────────────────────▼┐
                                                  │ Application Load Balancer    │
                                                  │ (if revisit Layer-7 needed)  │
                                                  └──────────────────────────────┘
```

---

## Comparison Matrix
| Feature | **CLB** | **ALB** | **NLB** | **GWLB** |
|---------|---------|---------|---------|----------|
|OSI Level| 4 & 7 | 7 | 4 | 3 & 4 |
|Protocols| HTTP, HTTPS, TCP | HTTP, HTTPS, HTTP/2, gRPC | TCP, UDP, TLS | IP |
|Target Types| Instance | Instance, IP, Lambda | Instance, IP, ALB | Instance, IP |
|Static / Elastic IP| No | No | **Yes** | No |
|Preserve Source IP| No | **Yes** | **Yes** | Yes |
|WAF Support| Yes | **Yes** | No | No |
|Fixed Response / Redirect| No | **Yes** | No | No |
|Cross-Zone Default| Off | **On (free)** | Off | N/A |
|Sticky Sessions| Cookie | Cookie | Source IP | Flow hash |
|ACM / TLS Offload| Yes | **Yes** | **Yes** | No |
|PrivateLink Target| No | No | **Yes** | N/A |
|Pricing Unit| Hour + LCU | Hour + LCU | Hour + NLCU | Hour + GWLCU |

---

## Critical Analysis & Exam Gotchas

| Gap Filled | Why It Matters (SAA-C03) | Quick Recall Trigger |
|------------|--------------------------|----------------------|
| **Classic LB** legacy | Might ask “Which LB to retire?” | *Migration / deprecate CLB* |
| Cross-zone default differences | Affects cost design | *ALB free, NLB $$* |
| **WAF only on ALB/CLB/CF** | Security design Qs | *NLB ≠ WAF* |
| **Global Accelerator vs. Route 53** | Multi-Region HA | *Static IP & TCP accel* |
| **LCU / NLCU / GWLCU** | Cost optimisation | *Four LCU dimensions* |
| **HTTP/2 & gRPC** | Micro-services | *Only ALB layer-7* |
| **TLS on NLB** | Preserve src IP with encryption | *TLS listener uses ACM* |
| **PrivateLink / VPC Link** | Secure service-to-service | *Only NLB Interface EP* |
| **Slow-start / Dereg delay** | Zero-downtime deploy | *300 s default drain* |
| **Lambda target quirks** | Serverless integration | *Lambda always healthy* |

> **Exam Tip:** If the scenario mentions *host-based routing, WAF, user auth, redirects, or gRPC*, the answer is **Application Load Balancer**. If it mentions *million-TPS TCP/UDP, static IPs, source IP preservation, or PrivateLink*, pick **Network Load Balancer**. Inline security appliances? **Gateway Load Balancer**. Legacy migration? **Classic Load Balancer**.

---

## References
- Elastic Load Balancing Features — AWS Docs  
  https://aws.amazon.com/elasticloadbalancing/features/#Product_comparisons  
- AWS Certificate Manager  
  https://aws.amazon.com/certificate-manager/  
- Authenticate Users Using an Application Load Balancer  
  https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html  
- AWS WAF Developer Guide  
  https://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html  
- AWS Blog — Introducing Gateway Load Balancer  
  https://aws.amazon.com/blogs/aws/introducing-aws-gateway-load-balancer-easy-deployment-scalability-and-high-availability-for-partner-appliances/  

---

## Changelog
| Date (BST) | Version | Change Description |
|------------|---------|--------------------|
|2025-06-04  | 1.0 | Initial notes on ELB basics |
|2025-06-12  | 2.0 | Added gaps: CLB, cross-zone, Global Accelerator, logs, WAF, cost models, decision flowchart, critical analysis |

