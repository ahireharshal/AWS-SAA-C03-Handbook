# 04 — Design Cost-Optimized Architectures

**Exam Weight:** 20%

## 1. Domain Objectives

- **4.1** Cost-optimized storage
- **4.2** Cost-optimized compute
- **4.3** Cost-optimized databases
- **4.4** Cost-optimized networks

---

## 2. Cost Optimization Mindset

The exam often asks for the solution that satisfies requirements at the **lowest cost** or with the **least operational overhead**.

Ask:
- Can resources scale down or to zero?
- Is workload interruptible?
- Is demand predictable?
- Can old data move to a cheaper tier?
- Are you paying NAT/data-transfer charges unnecessarily?
- Can caching reduce origin/DB requests?
- Is managed/serverless cheaper than operating a fleet at this utilization?
- Are non-production availability requirements lower than production?

---

## 3. EC2 Purchasing Models

### On-Demand
Use:
- short-term
- unpredictable
- no commitment

### Savings Plans / Reserved-style commitment
Use:
- predictable sustained usage
- long-running baseline

### Spot
Use:
- interruption-tolerant
- batch
- stateless/fault-tolerant workers
- flexible fleets

Do not use Spot as the sole answer for a workload that cannot tolerate interruption.

> **Decision Rule:** stable baseline → commitment discount. Flexible interruptible capacity → Spot.

---

## 4. Serverless Cost Pattern

### Lambda
Best when:
- intermittent/bursty
- execution-based workload
- no need for always-on server
- event-driven

### Fargate
Best when:
- containers are needed
- you want to avoid managing EC2 container hosts

### EC2
May be more economical for:
- sustained predictable compute
- specialized instance requirements
- workloads that need OS-level control

---

## 5. Storage Cost

### S3 lifecycle
Move objects based on access pattern:
- Standard for frequent access
- IA classes for less frequent access
- Intelligent-Tiering when access pattern is unknown/changing and the economics fit
- Glacier classes for archival

Cost dimensions include:
- storage
- requests
- retrieval
- data transfer
- minimum storage duration / retrieval characteristics depending on class

> **Decision Rule:** long-term archive rarely accessed → Glacier class, not S3 Standard.

### EBS
Right-size:
- volume size
- volume type
- provisioned IOPS/throughput

Do not over-provision premium IOPS when workload does not need them.

### EFS
Use lifecycle/tiering features where appropriate for infrequently accessed files.

---

## 6. Database Cost

### RDS/Aurora
Optimize:
- instance class
- storage
- read replicas only when needed
- reserved/committed capacity options where applicable
- stop/dev strategies where supported and appropriate
- Aurora Serverless for variable/intermittent requirements when it fits

### DynamoDB
**Provisioned**
- predictable traffic
- can optimize known capacity

**On-demand**
- unpredictable or spiky traffic
- simpler capacity planning
- potentially higher unit cost for stable high-volume workloads

> **Decision Rule:** unpredictable workload → on-demand. Predictable steady workload → evaluate provisioned + autoscaling/commitment options.

### Caching
ElastiCache/DAX can lower repeated database read load, but cache cost must be justified by the workload.

---

## 7. Network Cost Traps

### NAT Gateway
Costs can include:
- hourly gateway charge
- per-GB processing
- normal data-transfer dimensions

Common optimization:
- use VPC endpoints for supported AWS services instead of routing those flows through NAT
- avoid unnecessary cross-AZ NAT paths
- design one-per-AZ for resiliency when required, understanding cost tradeoff

### Cross-AZ / Cross-Region
Data transfer may incur charges.
Place communicating resources intelligently where requirements allow.

### CloudFront
Can reduce origin load and improve global delivery; may also improve data-transfer economics for internet delivery depending on traffic.

### Direct Connect vs VPN
Select based on:
- bandwidth
- consistency
- setup time
- cost
- business requirement

VPN is typically quicker/lower-entry-cost; Direct Connect is for dedicated connectivity requirements.

---

## 8. Cost Management Services

### Cost Explorer
Analyze and visualize historical/current cost and usage trends.

### AWS Budgets
Threshold-based budget tracking/alerts.

### Cost and Usage Report
Detailed cost/usage dataset for deeper analysis.

### Cost allocation tags
Attribute spend to teams/apps/environments.

> **Decision Rule:** “Alert when monthly spend exceeds threshold” → **AWS Budgets**.  
> “Analyze spending trends” → **Cost Explorer**.

---

## 9. Cost-Optimized Architecture Patterns

### Bursty API
`API Gateway → Lambda → DynamoDB on-demand`

Why:
- pay-per-use characteristics
- scales with traffic
- minimal idle infrastructure

### Predictable production web tier
`ALB → ASG baseline using discounted commitment + optional Spot for tolerant extra capacity`

### Data archive
`S3 → Lifecycle → Glacier`

### Private workload accessing S3
`Private EC2 → S3 Gateway Endpoint`
rather than NAT for S3 traffic.

---

## 10. Common Cost Traps

- Choosing Multi-Region active-active when business only needs Multi-AZ.
- Paying for premium EBS IOPS without performance need.
- Keeping archive data in S3 Standard forever.
- Sending supported AWS-service traffic through NAT when an endpoint is more appropriate.
- Using On-Demand EC2 forever for a stable baseline without evaluating commitments.
- Using Spot for non-interruptible stateful workloads.
- Over-sizing instances instead of using measurements and scaling.
- Keeping idle dev/test infrastructure running 24/7 when it can be scheduled/stopped/serverless.

---

## 11. 5-Minute Review

- Stable compute → Savings Plans/reservation options.
- Interruptible → Spot.
- Bursty/event-driven → serverless.
- S3 lifecycle → cheaper tiers.
- Archive → Glacier.
- Predictable DynamoDB → provisioned can be efficient.
- Unpredictable DynamoDB → on-demand.
- Right-size RDS/EC2/EBS.
- VPC endpoint can avoid NAT for supported service traffic.
- Cross-AZ/Region transfer can cost money.
- Budgets = threshold/alert.
- Cost Explorer = analysis.
- CUR = detailed billing data.
- Tags = allocation.
- Production and non-production need not have identical HA cost.

## Source Coverage

- Official exam guide: Domain 4 Tasks 4.1–4.4.
- Training deck: EC2 pricing/purchasing, storage classes/lifecycle, databases, CloudFront/networking, VPC, cost management references.
