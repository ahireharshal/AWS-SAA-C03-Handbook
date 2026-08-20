# 07 — Final SAA-C03 Cram Sheet

## Security

- Root account: MFA, avoid daily use.
- IAM role + STS = temporary credentials.
- Never hardcode AWS keys.
- Explicit deny wins.
- SCP = permission guardrail, not grant.
- SG = stateful.
- NACL = stateless.
- WAF = HTTP Layer 7 threats.
- Shield = DDoS.
- GuardDuty = threat detection.
- Macie = sensitive data discovery in S3.
- KMS = managed AWS-integrated key management.
- CloudHSM = dedicated HSM control.
- Secrets Manager = secrets + rotation.
- ACM = TLS certificates.
- Cognito = application users.
- IAM Identity Center = workforce SSO.
- S3 private CloudFront origin → OAC.
- Private S3 access from VPC → gateway endpoint.

## Resilience

- Multi-AZ = high availability.
- Read replica = read scaling.
- Queue = decouple/buffer/retry.
- SNS = fan-out.
- EventBridge = rule-based events.
- Step Functions = workflow.
- ALB + ASG across AZs = classic HA.
- Route 53 health checks + failover = DNS DR.
- RPO = acceptable data loss.
- RTO = acceptable downtime.
- Backup/restore = cheapest, slowest recovery.
- Pilot light = critical core running.
- Warm standby = reduced full stack.
- Active-active = fastest recovery, highest cost.

## Storage

- S3 = object.
- EBS = block, AZ-scoped.
- EFS = shared Linux file.
- FSx = managed specialized file systems.
- S3 lifecycle = cost optimization.
- Versioning = accidental change/delete protection.
- Cross-Region replication = regional resilience/compliance pattern.
- Glacier = archive.

## Compute

- EC2 = server/OS control.
- Lambda = serverless event-driven functions.
- ECS = AWS-native container orchestration.
- EKS = Kubernetes.
- Fargate = containers without managing nodes.
- Batch = batch workload scheduler.
- Horizontal scaling generally improves elasticity/resilience.

## Database

- RDS/Aurora = relational SQL/OLTP.
- DynamoDB = serverless NoSQL key-value/document.
- ElastiCache = in-memory cache.
- DAX = DynamoDB read cache.
- Redshift = data warehouse.
- Athena = SQL on S3.
- Neptune = graph.
- DocumentDB = MongoDB-compatible managed document DB.

### Database exam split
- “HA” → Multi-AZ.
- “Read-heavy” → read replica/cache.
- “Unpredictable serverless NoSQL” → DynamoDB on-demand.
- “Joins/transactions” → RDS/Aurora.

## Networking

- ALB = L7 HTTP/HTTPS.
- NLB = L4 TCP/UDP/TLS.
- GWLB = virtual appliances.
- CloudFront = CDN + edge caching.
- Global Accelerator = static global IPs + network acceleration/failover.
- VPN = encrypted tunnel over internet.
- Direct Connect = dedicated hybrid connection.
- PrivateLink/interface endpoint = private service consumption.
- Transit Gateway = hub-style VPC/network connectivity.
- VPC peering = direct VPC-to-VPC, non-transitive.

## Messaging

- SQS = one queue, decouple.
- SNS = one-to-many push/fan-out.
- EventBridge = event bus and routing.
- Kinesis = streaming.
- SQS + autoscaling workers = absorb spikes.

## Cost

- On-Demand = flexible/no commitment.
- Savings Plan/Reserved style = predictable baseline.
- Spot = interruptible.
- Serverless = strong for variable/intermittent workloads.
- S3 lifecycle/tiering.
- Avoid unnecessary NAT processing.
- VPC endpoints can reduce NAT dependence.
- Watch cross-AZ/Region transfer.
- Right-size EC2/RDS/EBS.
- Budgets = alerts.
- Cost Explorer = analysis.
- CUR = detailed billing data.

## “MOST likely answer” rules

- Least ops → managed/serverless.
- Highly available → managed Multi-AZ, no SPOF.
- Scalable → horizontal + managed autoscaling.
- Decoupled → async queue/event.
- Global static content → CloudFront.
- Global static IP / TCP-UDP acceleration → Global Accelerator.
- Private AWS-service access → VPC endpoint.
- Temporary cross-account access → role + STS.
- Secret rotation → Secrets Manager.
- SQLi/XSS → WAF.
- DDoS → Shield.
- Shared POSIX files → EFS.
- Archive → Glacier.
- Read scaling → read replica/cache.
- DB failover → Multi-AZ.

## Elimination Strategy

1. Remove answers that violate a hard requirement.
2. Remove self-managed options when “least operational overhead” is explicit and a managed service fits.
3. Do not confuse performance features with HA features.
4. Do not choose expensive Multi-Region designs when Multi-AZ meets the requirement.
5. Prefer purpose-built AWS services over custom EC2 implementations when requirements match.
6. Watch words such as **MOST cost-effective**, **LEAST operational overhead**, **without application changes**, **minimal downtime**, and **no data loss**.
