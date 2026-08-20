# 02 — Design Resilient Architectures

**Exam Weight:** 26%

## 1. Domain Objectives

### Task 2.1 — Design scalable and loosely coupled architectures
Event-driven design, queues, pub/sub, microservices, serverless, containers, scaling, caching, storage choice, workflow orchestration.

### Task 2.2 — Design highly available and/or fault-tolerant architectures
Multi-AZ / Multi-Region, failover, DR, Route 53, backups, quotas, proxies, durability, observability, eliminating single points of failure.

---

## 2. Core Resilience Principles

- Design for **failure**, not perfect infrastructure.
- Spread critical workloads across **multiple AZs**.
- Keep application tiers **stateless** where possible.
- Use **Auto Scaling + load balancing**.
- Decouple synchronous dependencies with **queues/events**.
- Separate **HA**, **backup**, and **DR**.
- Choose DR based on **RPO/RTO and cost**.
- Prefer managed failover mechanisms.
- Avoid a database, NAT path, appliance, or single instance becoming a single point of failure.

---

## 3. SQS vs SNS vs EventBridge

| Service | Pattern | Best use | Key clue |
|---|---|---|---|
| SQS | Queue | Buffer and decouple producer/consumer | “process later”, “absorb spikes” |
| SNS | Pub/sub fan-out | Push one event to many subscribers | “notify multiple systems” |
| EventBridge | Event bus + rules | Route events by pattern/source | “event-driven integration/routing” |

### SQS
Use when:
- producers and consumers scale independently
- consumer may be temporarily unavailable
- you need buffering
- work should be retried

Design:
`Producer → SQS → Auto Scaling workers`

### SNS fan-out
`Publisher → SNS → SQS-A + SQS-B + Lambda`

> **Decision Rule:** One durable work stream → SQS. One event to many consumers → SNS fan-out. Rule-based event integration → EventBridge.

---

## 4. Load Balancing + Auto Scaling

### ALB
Best for HTTP/HTTPS Layer 7:
- host/path routing
- web apps
- containers / microservices

### NLB
Best for:
- TCP/UDP/TLS
- very high network performance
- static IP style requirements / network-level traffic

### GWLB
Best for:
- transparent third-party virtual network appliances

### Auto Scaling
Use:
- across multiple AZs
- with health checks
- target tracking / other policy types based on workload metric

> **Exam rule:** Replace a single EC2 web server with **ALB + Auto Scaling across at least two AZs**.

---

## 5. RDS Multi-AZ vs Read Replica

| Feature | Multi-AZ | Read Replica |
|---|---|---|
| Primary purpose | High availability / failover | Read scaling |
| Application reads from standby | Normally no | Yes |
| Failover | Managed HA behavior | Not the core purpose |
| Can be cross-Region | Architecture-dependent | Often supported for engines/features |
| Exam clue | “minimize DB downtime” | “read-heavy workload” |

> **Decision Rule:** HA → **Multi-AZ**. Read scaling → **Read Replica**.

### Aurora
High-yield:
- separates compute and distributed storage
- multiple copies of data across AZs
- writer + reader endpoints
- Aurora replicas for read scale/failover
- Aurora Global Database for global DR/read scenarios
- Aurora Serverless for variable/intermittent database capacity patterns

---

## 6. Storage Resilience

### S3
- Regional object storage designed for very high durability.
- Versioning protects against accidental overwrite/delete scenarios.
- Replication can be same-Region or cross-Region.
- Lifecycle and Glacier classes support long-term recovery/retention patterns.

### EBS
- Volume is AZ-scoped.
- Snapshot is the mechanism to recreate/move data beyond the original volume/AZ.
- Do not treat a single EBS volume as a cross-AZ shared datastore.

### EFS
- Regional shared Linux file system architecture.
- Useful when multiple instances/AZs require shared file access.

---

## 7. Route 53 Resilience

Routing policies you should recognize:
- Simple
- Weighted
- Latency-based
- Failover
- Geolocation
- Multi-value answer

Health checks + failover policies are key for disaster recovery.

> **Decision Rule:** “Automatically route users to secondary endpoint when primary becomes unhealthy” → **Route 53 failover routing + health checks**.

---

## 8. Disaster Recovery Strategies

| Strategy | Runtime footprint | Cost | Typical RTO | Idea |
|---|---:|---:|---|---|
| Backup & Restore | Minimal | Lowest | Highest | Restore after event |
| Pilot Light | Core services running | Low–medium | Faster | Keep critical core alive |
| Warm Standby | Reduced full environment | Medium–high | Faster | Scale up on disaster |
| Multi-Site / Active-Active | Full production | Highest | Lowest | Both environments active |

### RPO
Maximum acceptable **data loss window**.

### RTO
Maximum acceptable **downtime**.

> **Decision Rule:** Tightest RTO/RPO generally costs more.

---

## 9. Serverless Resilience

### Lambda
Good for:
- event-driven workloads
- variable traffic
- short-lived execution
- minimal server administration

Patterns:
- API Gateway → Lambda → DynamoDB
- S3 event → Lambda
- EventBridge → Lambda
- SQS → Lambda

Use DLQs / failure destinations / retries according to integration semantics.

### Step Functions
Use for:
- multi-step workflows
- state management
- retries/branching/orchestration

> **Trap:** Do not use a custom EC2 coordinator when Step Functions directly solves workflow orchestration with lower operational overhead.

---

## 10. Container Resilience

### ECS
AWS-native container orchestration.

### EKS
Managed Kubernetes control plane when Kubernetes ecosystem/API is required.

### Fargate
Serverless compute for containers; removes node management.

> **Decision Rule:** “Run containers without managing EC2 worker nodes” → **Fargate**.

---

## 11. Common Architecture Patterns

### Highly available web application
`Route 53 → ALB → Auto Scaling EC2 across 2+ AZs → RDS Multi-AZ`

### Decoupled processing
`API/Web tier → SQS → Worker ASG → Database`

### Fan-out
`Producer → SNS → multiple SQS queues → independent consumers`

### Global static/dynamic front door
`Users → CloudFront → ALB/API/S3`

### DR
Primary Region:
`Route 53 → ALB → ASG → DB`
Secondary Region:
appropriate warm/pilot/active resources based on RTO/RPO.

---

## 12. Resilience Traps

- A backup is not high availability.
- Read replicas are not a substitute for Multi-AZ HA.
- One EC2 instance behind an ALB is still a single-instance risk.
- One AZ is not a highly available Regional architecture.
- A NAT Gateway placed in one AZ can create an avoidable dependency for workloads in other AZs.
- Synchronous chains create cascading failure risk; use queues/events when the business process permits asynchronous handling.
- Caching improves performance but must not become the only copy of authoritative data unless designed that way.

---

## 13. 5-Minute Review

- Multi-AZ = HA.
- Read replica = scale reads.
- Queue = decouple + buffer.
- SNS = fan-out.
- EventBridge = event routing.
- Step Functions = workflow orchestration.
- ALB + ASG + multiple AZs = classic HA web tier.
- Route 53 health check + failover = DNS DR.
- Backup/restore cheapest but slowest recovery.
- Multi-site fastest recovery but highest cost.
- RPO = data loss tolerance.
- RTO = downtime tolerance.
- S3 versioning/replication = resilience tools.
- EBS is AZ-scoped; snapshot for recovery/copy workflows.
- EFS = shared regional file system.
- Fargate = containers without node management.

## Source Coverage

- Official exam guide: Domain 2 Tasks 2.1 and 2.2.
- Training deck: High Availability & Scalability, RDS/Aurora, Route 53, Messaging, Serverless Architectures, VPC, Disaster Recovery & Migrations.
- Strong coverage: classic HA, messaging, Multi-AZ, Route 53, DR strategies.
