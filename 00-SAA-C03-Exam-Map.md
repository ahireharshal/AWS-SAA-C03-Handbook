# 00 — SAA-C03 Exam Map

## Official Domains

### Domain 1 — Design Secure Architectures — 30%
- **1.1** Design secure access to AWS resources
- **1.2** Design secure workloads and applications
- **1.3** Determine appropriate data security controls

### Domain 2 — Design Resilient Architectures — 26%
- **2.1** Design scalable and loosely coupled architectures
- **2.2** Design highly available and/or fault-tolerant architectures

### Domain 3 — Design High-Performing Architectures — 24%
- **3.1** Determine high-performing and/or scalable storage solutions
- **3.2** Design high-performing and elastic compute solutions
- **3.3** Determine high-performing database solutions
- **3.4** Determine high-performing and/or scalable network architectures
- **3.5** Determine high-performing data ingestion and transformation solutions

### Domain 4 — Design Cost-Optimized Architectures — 20%
- **4.1** Design cost-optimized storage solutions
- **4.2** Design cost-optimized compute solutions
- **4.3** Design cost-optimized database solutions
- **4.4** Design cost-optimized network architectures

## High-Priority Cross-Domain Services

| Service / Area | Secure | Resilient | Performance | Cost | Why it matters |
|---|:---:|:---:|:---:|:---:|---|
| IAM / STS / Organizations | ★★★ | ★ | ★ | ★ | Identity, roles, cross-account, governance |
| VPC / SG / NACL / endpoints | ★★★ | ★★ | ★★★ | ★★ | Private design, routing, hybrid, transfer cost |
| EC2 / Auto Scaling / ELB | ★★ | ★★★ | ★★★ | ★★★ | Core elastic architecture |
| S3 | ★★★ | ★★★ | ★★★ | ★★★ | Security, durability, performance, storage classes |
| RDS / Aurora | ★★ | ★★★ | ★★★ | ★★★ | HA vs read scaling, backups, replicas |
| DynamoDB | ★★ | ★★★ | ★★★ | ★★★ | Serverless NoSQL, on-demand/provisioned, global tables |
| CloudFront / Global Accelerator | ★★ | ★★★ | ★★★ | ★★ | Edge acceleration and global routing |
| SQS / SNS / EventBridge | ★ | ★★★ | ★★★ | ★★ | Decoupling and event-driven design |
| Lambda / API Gateway | ★★ | ★★★ | ★★★ | ★★★ | Serverless architecture |
| EBS / EFS / FSx | ★★ | ★★★ | ★★★ | ★★★ | Block vs file storage decisions |
| Route 53 | ★ | ★★★ | ★★★ | ★ | DNS, health checks, routing policies |
| KMS / Secrets Manager / ACM | ★★★ | ★ | ★ | ★ | Encryption, key/secrets/cert lifecycle |

## Architecture Questions to Ask

Before choosing a service, identify:

1. **Data type:** object, file, block, relational, key/value, streaming?
2. **Access pattern:** read-heavy, write-heavy, sequential, random, global?
3. **Availability:** one AZ acceptable? Multi-AZ? Multi-Region?
4. **Recovery:** required RPO and RTO?
5. **Traffic:** predictable, bursty, intermittent?
6. **Security:** public/private, encryption, key ownership, cross-account?
7. **Operations:** can the team manage servers, or is low-ops mandatory?
8. **Cost:** always-on vs pay-per-use, data transfer, lifecycle/tiering?
9. **Latency:** milliseconds, sub-millisecond, edge delivery?
10. **Protocol:** HTTP/HTTPS, TCP/UDP/TLS, NFS/SMB, SQL?

## Exam Language → Architectural Bias

| Phrase | Bias |
|---|---|
| least operational overhead | Managed or serverless |
| decouple components | SQS / EventBridge / SNS |
| absorb traffic spikes | Queue + autoscaling |
| highly available relational DB | RDS Multi-AZ / Aurora |
| scale database reads | Read replicas / cache |
| globally distributed static content | CloudFront |
| fixed global IPs | Global Accelerator |
| shared Linux file system | EFS |
| Windows file shares | FSx for Windows File Server |
| object storage | S3 |
| private access to AWS service | VPC endpoint / PrivateLink |
| temporary cross-account access | IAM role + STS |
| protect against SQL injection/XSS | AWS WAF |
| DDoS protection | Shield |
| secret rotation | Secrets Manager |
| unpredictable NoSQL workload | DynamoDB on-demand |
| interruption-tolerant compute | Spot |
| predictable long-running compute | Savings Plans / Reserved options |
