# AWS Certified Solutions Architect – Associate (SAA-C03) Handbook

A GitHub-friendly, exam-focused handbook derived from the supplied **AWS Certified Solutions Architect Associate** training slides and the official **AWS Certified Solutions Architect – Associate (SAA-C03) Exam Guide**.

> This is a transformed study aid, not a reproduction of the course slides. Use the official AWS documentation as the final authority for mutable quotas, pricing, regional availability, and recently changed features.

## Exam Blueprint

| Domain | Weight |
|---|---:|
| [Design Secure Architectures](01-Design-Secure-Architectures.md) | 30% |
| [Design Resilient Architectures](02-Design-Resilient-Architectures.md) | 26% |
| [Design High-Performing Architectures](03-Design-High-Performing-Architectures.md) | 24% |
| [Design Cost-Optimized Architectures](04-Design-Cost-Optimized-Architectures.md) | 20% |

## Handbook Navigation

- [00 – Exam Map](00-SAA-C03-Exam-Map.md)
- [01 – Design Secure Architectures](01-Design-Secure-Architectures.md)
- [02 – Design Resilient Architectures](02-Design-Resilient-Architectures.md)
- [03 – Design High-Performing Architectures](03-Design-High-Performing-Architectures.md)
- [04 – Design Cost-Optimized Architectures](04-Design-Cost-Optimized-Architectures.md)
- [05 – Service Comparison Matrix](05-SAA-C03-Service-Comparison-Matrix.md)
- [06 – Numbers & Defaults to Remember](06-SAA-C03-Numbers-to-Remember.md)
- [07 – Final Cram Sheet](07-SAA-C03-Final-Cram-Sheet.md)
- [08 – Source Coverage & Verification](08-Source-Coverage-and-Verification.md)

## How to Study With This Handbook

1. Read **00 – Exam Map** first.
2. Study domains in exam-weight order: **Secure → Resilient → High-Performing → Cost-Optimized**.
3. For every service, learn the **decision rule**, not only the definition.
4. Revisit **05 – Service Comparison Matrix** frequently.
5. Review **06 – Numbers & Defaults** only after understanding the architecture.
6. Use **07 – Final Cram Sheet** in the final days before the exam.

## Core Exam Mindset

When two answers are technically possible, look for the requirement that makes one **better**:

- **least operational overhead** → prefer managed/serverless
- **high availability** → Multi-AZ, managed failover, no single point of failure
- **read scaling** → replicas/cache, not merely HA
- **decoupling** → queues/events
- **global low latency** → edge/global services
- **cost optimization** → right-size, tier, pay-per-use, avoid unnecessary data transfer
- **security** → least privilege, temporary credentials, private networking, encryption
