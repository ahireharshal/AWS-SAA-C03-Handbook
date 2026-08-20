# 06 — Numbers & Defaults to Remember

> **Important:** AWS quotas and product specifications change. Memorize numbers only when they help distinguish exam answers. Treat mutable limits as verification candidates before exam day.

| Topic | Value from supplied study material / exam guide | Why it matters | Type |
|---|---:|---|---|
| Exam scored questions | 50 | Exam structure | Exam guide |
| Exam unscored questions | 15 | Exam structure | Exam guide |
| Passing scaled score | 720 / 1000 | Exam structure | Exam guide |
| Domain 1 | 30% | Study priority | Exam invariant for SAA-C03 guide version |
| Domain 2 | 26% | Study priority | Exam invariant for SAA-C03 guide version |
| Domain 3 | 24% | Study priority | Exam invariant for SAA-C03 guide version |
| Domain 4 | 20% | Study priority | Exam invariant for SAA-C03 guide version |
| RDS automated backup PITR | up to 35 days in course summary | Common backup distinction | Current-service specification; verify |
| DynamoDB PITR | up to 35 days in course summary | Recovery feature | Current-service specification; verify |
| Aurora storage copies | 6 copies across 3 AZs in course summary | HA design concept | Service architecture fact; verify current docs |
| Aurora Global replication | course states sub-second storage replication | Global performance concept | Current-service specification; verify |
| CloudFront / edge counts | course contains point-in-time counts | Low exam value | **Do not memorize exact count** |
| Region AZ counts | course gives example ranges | Architecture concept more important than exact count | **Do not over-memorize** |

## Numbers More Important as Concepts Than Exact Values

### Availability Zones
Remember:
- Regions contain multiple isolated AZs.
- Architect production workloads across multiple AZs.
- Exact number of AZs per Region can change.

### S3
Remember:
- massive object scalability
- multipart upload for large objects
- object-size and request limits are mutable specifications; verify only if your practice exams test them

### Lambda
Remember:
- concurrency, timeout, memory, payload limits can appear in practice material, but these are mutable and should be checked against current AWS docs.

### SQS
Remember:
- visibility timeout, message retention, long polling, FIFO behavior matter conceptually.
- exact quotas may change.

## Memorization Rule

Prioritize:
1. **Layer numbers:** ALB L7, NLB L4.
2. **Exam weights.**
3. **Architecture distinctions:** Multi-AZ vs replica, stateful vs stateless.
4. **DR ordering:** Backup → Pilot Light → Warm Standby → Active-Active in increasing cost/decreasing RTO.
5. Only then memorize service-specific numeric defaults that repeatedly appear in practice questions.
