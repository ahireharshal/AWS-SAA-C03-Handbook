# Source & Coverage Notes

## Supplied Sources

1. **AWS Certified Solutions Architect Slides v48** by Stéphane Maarek.
2. **AWS Certified Solutions Architect – Associate (SAA-C03) Exam Guide**, Version 1.1.
3. `SKILL.md` defining the transformation and cheatsheet format.

## Course Sections Used as Primary Study Material

- IAM
- EC2 basics/associate/instance storage
- High Availability & Scalability
- RDS, Aurora & ElastiCache
- Route 53
- Classic Solutions Architecture
- S3 / S3 Advanced / S3 Security
- CloudFront & Global Accelerator
- Storage Extras
- Integration & Messaging
- Containers
- Serverless
- Databases
- Data & Analytics
- Monitoring / Audit
- Advanced Identity
- Security & Encryption
- VPC
- Disaster Recovery & Migrations
- More Solutions Architecture
- Exam Review & Tips

## Coverage Gaps / Verification Candidates

The supplied material is broad, but before the exam you should verify mutable facts in current AWS documentation, especially:

- exact service quotas
- current Lambda/SQS/API Gateway limits
- current S3 object-size/Glacier retrieval details
- current instance families and prices
- current service regional availability
- recently renamed/retired services/features
- current Aurora/DynamoDB limits
- current CloudFront/Global Accelerator specifics

The handbook intentionally emphasizes architecture decisions rather than copying every volatile quota from the slides.

## Transformation Policy

The handbook:
- reorganizes the source by official exam domain
- condenses repeated content
- converts diagrams into architecture decision patterns
- emphasizes service selection, traps, resilience, security, performance and cost
- does not reproduce the original slide deck verbatim
