# 05 — SAA-C03 Service Comparison Matrix

This file is designed for **“why this answer and not the other one?”** review.

## Storage

| Service | Type | Scope / access | Best for | Avoid when |
|---|---|---|---|---|
| S3 | Object | Regional API | objects, static assets, backups, data lake | block/file semantics required |
| EBS | Block | Volume in one AZ | EC2 disks, DB block storage | shared multi-AZ filesystem |
| EFS | File/NFS | Regional shared FS | Linux shared storage | Windows SMB requirement |
| FSx for Windows | File/SMB | Managed Windows file | Windows shares / AD integration | Linux NFS is enough |
| FSx for Lustre | High-performance file | Managed parallel FS | HPC/high-throughput processing | generic object archive |

**Decision:** object → S3; EC2 block → EBS; shared Linux file → EFS; specialized Windows/HPC filesystem → FSx.

---

## Database

| Service | Model | Best for | Scaling clue |
|---|---|---|---|
| RDS | Relational | managed SQL/OLTP | read replicas, instance/storage scaling |
| Aurora | Cloud-native relational | high availability/performance relational | Aurora replicas/global/serverless features |
| DynamoDB | Key-value/document NoSQL | massive serverless scale | provisioned/on-demand |
| ElastiCache | In-memory | cache/session | memory-based low latency |
| Redshift | Columnar warehouse | OLAP/BI | analytical scale |
| Athena | Serverless SQL on S3 | ad hoc S3 analytics | no cluster management |
| Neptune | Graph | relationships | graph traversal |
| DocumentDB | Document | MongoDB-compatible workloads | managed document DB |

**Decision:** joins/transactions → RDS/Aurora; huge key-value serverless → DynamoDB; cache → ElastiCache.

---

## RDS Multi-AZ vs Read Replica

| | Multi-AZ | Read Replica |
|---|---|---|
| Goal | HA/failover | Read scaling |
| Standby serves normal reads | No | Yes |
| Exam phrase | “minimize downtime” | “read-heavy” |

---

## Messaging

| Service | Pattern | Best clue |
|---|---|---|
| SQS | Queue | decouple/buffer/retry |
| SNS | Pub/sub | fan-out notifications |
| EventBridge | Event bus | rules/event routing |
| Step Functions | Workflow | orchestration/state machine |
| Kinesis | Stream | ordered/real-time streaming ingestion |

---

## Compute

| Service | Best for | Operational model |
|---|---|---|
| EC2 | OS control/general compute | manage instances |
| Lambda | event-driven functions | serverless |
| ECS | AWS-native containers | managed orchestration |
| EKS | Kubernetes | managed control plane |
| Fargate | serverless container compute | no worker-node management |
| Batch | batch scheduling | managed batch |

---

## Load Balancing

| | ALB | NLB | GWLB |
|---|---|---|---|
| Layer | 7 | 4 | Appliance insertion |
| HTTP routing | Excellent | No L7 routing | No |
| TCP/UDP | Not primary | Yes | appliance flows |
| Main use | web/microservices | high-performance network traffic | firewalls/inspection |

---

## Edge

| | CloudFront | Global Accelerator |
|---|---|---|
| CDN/cache | Yes | No |
| HTTP/S content | Yes | Can accelerate endpoint path |
| TCP/UDP | No general use | Yes |
| Static global IP | Not primary | Yes |
| Use | content delivery | global network entry/failover |

---

## Network Security

| | Security Group | NACL |
|---|---|---|
| Level | ENI/resource | subnet |
| Stateful | Yes | No |
| Deny rule | No explicit deny | Yes |
| Rule order | not order-based | order matters |

---

## Identity

| | IAM | IAM Identity Center | Cognito |
|---|---|---|---|
| Audience | AWS identities/workloads | Workforce SSO | App customers/users |
| Key use | permissions/roles | centralized workforce access | app authentication |

---

## Encryption / Secrets

| | KMS | CloudHSM | Secrets Manager | ACM |
|---|---|---|---|---|
| Main purpose | managed keys | dedicated HSM | secrets/rotation | TLS certificates |
| Best clue | SSE-KMS / CMK | dedicated HSM control | rotate DB password | HTTPS cert |

---

## Connectivity

| | Site-to-Site VPN | Direct Connect | PrivateLink |
|---|---|---|---|
| Path | encrypted over internet | dedicated private circuit | private service endpoint |
| Setup | relatively fast | longer/provisioned | service-specific |
| Best clue | encrypted hybrid quickly | consistent dedicated hybrid | consume service privately |

---

## DR Strategies

| | Cost | RTO | Runtime |
|---|---:|---:|---|
| Backup/Restore | Lowest | Slowest | backups only |
| Pilot Light | Low | Better | core running |
| Warm Standby | Medium | Fast | reduced full stack |
| Active-Active | Highest | Fastest | full production |

---

## S3 Storage Classes

| Class family | Access pattern |
|---|---|
| Standard | frequent |
| Standard-IA | infrequent, multi-AZ resilience |
| One Zone-IA | infrequent, single-AZ tolerance |
| Intelligent-Tiering | unknown/changing |
| Glacier Instant Retrieval | archive with immediate-style retrieval |
| Glacier Flexible Retrieval | archive with slower retrieval options |
| Glacier Deep Archive | lowest-cost long-term archive, longest retrieval |

---

## EC2 Purchasing

| Option | Use |
|---|---|
| On-Demand | no commitment, variable/short term |
| Savings Plans / Reserved | stable baseline |
| Spot | interruption-tolerant |
| Dedicated Host / Instance | licensing/compliance/isolation requirements |

---

## Exam Keyword → Choice

| Phrase | Choice |
|---|---|
| decouple | SQS |
| fan-out | SNS + subscribers/queues |
| event rules | EventBridge |
| workflow state | Step Functions |
| web L7 | ALB |
| TCP/UDP | NLB |
| edge cache | CloudFront |
| static global IP | Global Accelerator |
| HA relational | Multi-AZ |
| read scaling relational | Read Replica |
| shared Linux files | EFS |
| objects | S3 |
| EC2 disk | EBS |
| rotate secrets | Secrets Manager |
| TLS cert | ACM |
| DDoS | Shield |
| SQLi/XSS | WAF |
| S3 PII discovery | Macie |
| threat detection | GuardDuty |
