# 03 — Design High-Performing Architectures

**Exam Weight:** 24%

## 1. Domain Objectives

- **3.1** High-performing/scalable storage
- **3.2** High-performing elastic compute
- **3.3** High-performing databases
- **3.4** High-performing/scalable networks
- **3.5** High-performing data ingestion/transformation

---

## 2. Storage Decision Matrix

| Need | Service |
|---|---|
| Durable object storage | S3 |
| Boot/block storage for EC2 | EBS |
| Shared Linux NFS file system | EFS |
| Windows SMB / specialized file systems | FSx family |
| On-prem integration with cloud storage | Storage Gateway |
| Large offline migration | Snow Family |
| Online file/object transfer | DataSync |

### S3
Use when:
- object storage
- static assets
- data lakes
- backup/archive
- massive scale

Performance features from the course:
- multipart upload
- Transfer Acceleration
- S3 Select for selective retrieval use cases
- CloudFront for edge caching

### EBS
Choose based on IOPS/throughput/latency requirements.
- General-purpose SSD for broad workloads
- Provisioned IOPS for I/O-intensive, latency-sensitive DB workloads
- HDD variants for throughput-oriented workloads where supported/appropriate

### EFS
Use for:
- shared POSIX/NFS-style Linux storage
- horizontally scaled EC2 fleets needing common files

---

## 3. Compute Selection

| Workload | Likely choice |
|---|---|
| General-purpose servers | General-purpose EC2 |
| CPU-heavy | Compute optimized |
| Memory-heavy | Memory optimized |
| Accelerated GPU/ML | Accelerated computing |
| Short/event-driven | Lambda |
| Containers with no server management | Fargate |
| Kubernetes requirement | EKS |
| AWS-native containers | ECS |
| Batch jobs | AWS Batch |

### Scale horizontally when possible
Prefer:
`Load balancer → Auto Scaling fleet`
over a single oversized server when architecture supports it.

### Lambda
Performance considerations:
- memory selection also affects available compute capacity
- concurrency and downstream limits matter
- good fit for bursty/event-driven systems

---

## 4. Database Selection

| Requirement | Service |
|---|---|
| Relational OLTP / joins / SQL | RDS / Aurora |
| High-scale key-value / serverless NoSQL | DynamoDB |
| In-memory cache | ElastiCache |
| Data warehouse / OLAP | Redshift |
| Query S3 with SQL | Athena |
| Search / full-text | OpenSearch |
| Graph relationships | Neptune |
| MongoDB-compatible document workload | DocumentDB |

### RDS
Use for:
- relational transactions
- SQL and joins
- supported managed engines

Scale:
- vertically via instance size
- reads via read replicas
- storage autoscaling where supported

### Aurora
Use when:
- relational engine compatibility
- higher cloud-native availability/performance features
- reader scaling
- global database patterns
- variable load with Aurora Serverless when appropriate

### DynamoDB
Key characteristics from the course:
- serverless NoSQL
- provisioned or on-demand capacity
- Multi-AZ architecture by default
- DAX for microsecond-style cached reads
- Global Tables for active-active multi-Region patterns
- Streams for event processing

> **Decision Rule:** unpredictable key-value traffic + minimal administration → **DynamoDB on-demand**.

### ElastiCache
Use for:
- frequently read data
- sessions
- database query result caching
- very low latency

Redis/Valkey-style deployments offer richer persistence/replication features than Memcached-style simple distributed cache patterns.

---

## 5. Cache Hierarchy

Possible caching layers:
1. Browser/client
2. CloudFront edge
3. API Gateway caching where applicable
4. ElastiCache
5. DAX for DynamoDB
6. Application-local cache

> **Exam clue:** “Reduce database read load with sub-millisecond access” → ElastiCache.  
> “Accelerate DynamoDB reads with microsecond latency” → DAX.

---

## 6. ALB vs NLB vs GWLB

| Feature | ALB | NLB | GWLB |
|---|---|---|---|
| Layer | 7 | 4 | Network appliance insertion |
| Protocol focus | HTTP/HTTPS | TCP/UDP/TLS | Appliance traffic |
| Routing | Host/path/header-style app routing | Network flow | Transparent appliance steering |
| Best use | Web apps, microservices | Very high network performance, non-HTTP | Firewalls/inspection appliances |

---

## 7. CloudFront vs Global Accelerator

| Requirement | CloudFront | Global Accelerator |
|---|---|---|
| CDN caching | **Yes** | No |
| HTTP static/dynamic acceleration | Yes | Can accelerate network path |
| TCP/UDP apps | No general CDN role | **Yes** |
| Static anycast IPs | Not the main design point | **Yes** |
| Fast regional failover | Possible through origin strategies | Strong use case |
| Edge caching | **Yes** | No |

> **Decision Rule:** cache content at edge → **CloudFront**.  
> fixed global IPs / TCP-UDP / deterministic fast endpoint failover → **Global Accelerator**.

---

## 8. Route 53 Performance

Latency-based routing:
- routes users toward the endpoint with lower measured network latency.

Geolocation:
- routes based on user location/business policy, not simply “fastest”.

Weighted:
- traffic splitting, testing, migrations.

---

## 9. Data Ingestion & Analytics

### Kinesis
Use for streaming data.
Think:
- continuous events
- real-time ingestion
- ordered shard-based stream concepts depending on service

### SQS
Use for work queues rather than a streaming analytics backbone.

### Firehose-style delivery pattern
When the requirement is managed delivery of streaming data to supported destinations, choose the managed delivery service rather than building custom consumers when appropriate.

### Glue
ETL/data catalog and transformation workflows.

### Athena
Serverless SQL queries over data in S3.

### EMR
Managed big-data frameworks for large distributed data processing.

### Redshift
Analytical data warehouse.

---

## 10. Hybrid / Migration Performance

### DataSync
Online accelerated data movement between on-premises and AWS storage.

### Storage Gateway
Hybrid storage integration where on-prem applications need file/volume/tape interfaces backed by AWS.

### Snow Family
Use when network transfer is impractical for very large datasets.

Course heuristic:
> If network migration would take too long (for example, beyond an acceptable multi-day/week window), consider Snow devices.

---

## 11. Performance Traps

- Multi-AZ is primarily an HA answer, not a read-scaling answer.
- A larger EC2 instance is not always better than horizontal scaling.
- EBS, EFS, and S3 are not interchangeable: block vs file vs object.
- CloudFront caches content; Global Accelerator does not.
- DAX is for DynamoDB, not RDS.
- ElastiCache may require application changes to actually use the cache.
- Read replicas improve read capacity but introduce replication considerations.
- Always check downstream bottlenecks when scaling Lambda/EC2 consumers.

---

## 12. 5-Minute Review

- S3 = object.
- EBS = block.
- EFS = shared Linux file.
- FSx = managed specialized file systems.
- ALB = L7.
- NLB = L4.
- CloudFront = CDN/cache.
- Global Accelerator = global network acceleration/static IP.
- RDS/Aurora = relational.
- DynamoDB = serverless NoSQL.
- ElastiCache = in-memory.
- DAX = DynamoDB cache.
- Redshift = warehouse.
- Athena = SQL on S3.
- Glue = ETL/catalog.
- Kinesis = streaming.
- DataSync = online transfer.
- Snow = offline/edge bulk transfer.
- ASG = elastic EC2.
- Lambda = event-driven/serverless compute.

## Source Coverage

- Official exam guide: Domain 3 Tasks 3.1–3.5.
- Training deck: EC2, Instance Storage, HA/Scaling, RDS/Aurora/ElastiCache, S3, CloudFront, Storage Extras, Messaging, Serverless, Databases, Analytics.
