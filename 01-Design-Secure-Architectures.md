# 01 — Design Secure Architectures

**Exam Weight:** 30%

## 1. Domain Objectives

### Task 1.1 — Design secure access to AWS resources
Focus on IAM, federation, roles, multi-account governance, resource policies, least privilege, root-user protection, and temporary credentials.

### Task 1.2 — Design secure workloads and applications
Focus on VPC segmentation, security groups, NACLs, endpoints, WAF/Shield, secrets, application identities, and secure hybrid connectivity.

### Task 1.3 — Determine appropriate data security controls
Focus on encryption at rest/in transit, KMS, ACM, data policies, backup/retention, key policy design, and data classification.

---

## 2. High-Yield Themes

- Prefer **roles and temporary credentials** over long-lived access keys.
- Apply **least privilege**.
- Protect the **root account with MFA** and avoid routine use.
- Use **SCPs as guardrails**, not as grants of permission.
- Use **resource policies** when a resource itself must trust another principal/account/service.
- Put application resources in **private subnets** when public reachability is not required.
- **Security Groups are stateful**; **NACLs are stateless**.
- Use **Secrets Manager** for managed secret rotation.
- Use **KMS** for managed encryption-key integration across AWS services.
- Use **CloudHSM** when dedicated HSM control is a hard requirement.
- Use **WAF for Layer 7 web threats**; Shield for DDoS.
- Encrypt in transit with **TLS/ACM**.

---

## 3. Security Service Decision Matrix

| Service | Primary purpose | Use when | Avoid / alternative | Scope / model | Exam trigger |
|---|---|---|---|---|---|
| IAM | AWS authorization | Users, roles, policies | Not an app-user directory | Global identity service | least privilege |
| STS | Temporary credentials | Role assumption, federation | Avoid long-lived keys | Temporary sessions | cross-account role |
| IAM Identity Center | Workforce federation | Central workforce access | Cognito for application users | Multi-account workforce | SSO |
| Organizations / SCP | Account governance | Guardrails across accounts | Does not grant permissions | Organization-level | prevent accounts from doing X |
| Cognito | App-user identity | Web/mobile authentication | IAM Identity Center for workforce | Managed identity | millions of app users |
| KMS | Managed keys | Encrypt AWS-integrated resources | CloudHSM for dedicated HSM | Regional key service | SSE-KMS |
| CloudHSM | Dedicated HSM | Customer-controlled HSM tenancy | KMS for simpler managed keys | VPC-based cluster | dedicated HSM |
| Secrets Manager | Secret storage + rotation | DB/API credentials | Parameter Store for simpler config | Managed | rotate passwords |
| ACM | TLS certificates | ELB/CloudFront/API Gateway HTTPS | Not general secret store | Managed cert lifecycle | HTTPS certificate |
| WAF | Layer 7 filtering | SQLi/XSS/rate/geo rules | Shield for network DDoS | Web ACL | SQL injection |
| Shield | DDoS protection | Network/transport DDoS | WAF for HTTP request filtering | Managed | DDoS |
| GuardDuty | Threat detection | Suspicious account/network activity | Not a firewall | Managed detection | threat findings |
| Macie | S3 sensitive-data discovery | PII/sensitive data in S3 | Not network security | Managed classification | discover PII |
| Inspector | Vulnerability management | EC2/ECR/Lambda vulnerability findings | Not runtime firewall | Managed scanning | CVEs |

---

## 4. IAM Deep Dive

### IAM users, groups, roles, policies

**Use when**
- You need AWS authorization for people or workloads.
- You need a role for EC2, Lambda, ECS tasks, cross-account access, or federation.

**Do NOT**
- Put access keys in source code.
- Give broad `AdministratorAccess` when narrower access works.
- Use the root user for daily operations.

**Key architecture**
- **User**: long-lived identity.
- **Group**: collection of IAM users.
- **Role**: assumed identity with temporary credentials.
- **Policy**: permission document.
- **Trust policy**: who can assume a role.
- **Permissions policy**: what the role/user can do.

**Exam trigger**
> “An application on EC2 needs to access S3 securely without storing credentials.”

**Answer**
> Attach an **IAM role / instance profile** to the EC2 instance.

### Policy evaluation concepts

Remember:
- Explicit **Deny** wins over Allow.
- An SCP sets the **maximum available permissions** for affected accounts/OUs.
- A permissions boundary limits what an IAM identity **can ever receive**.
- A resource policy grants/limits access **on the resource side**.

---

## 5. IAM Policy vs Resource Policy vs SCP vs Permissions Boundary

| Control | Attached to | Grants permission? | Main purpose |
|---|---|---:|---|
| Identity policy | IAM user/group/role | Yes | Define identity permissions |
| Resource policy | Resource | Yes, subject to evaluation | Resource-side trust/access |
| SCP | Organization / OU / account | No | Maximum permission guardrail |
| Permissions boundary | User / role | No by itself | Maximum permission ceiling |
| Session policy | Role session | No beyond role | Further restrict temporary session |

> **Decision Rule:** “Prevent every account in this OU from disabling logging” → **SCP**.  
> “Allow another account to access this S3 bucket” → **bucket resource policy**.

---

## 6. Security Group vs Network ACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Applied to | ENI / resource | Subnet |
| Stateful | **Yes** | **No** |
| Rules | Allow only | Allow + deny |
| Return traffic | Automatically allowed | Must be explicitly permitted |
| Evaluation | All rules considered | Rule order matters |
| Best use | Primary resource firewall | Subnet-level coarse control |

> **Decision Rule:** For normal instance access control, prefer **Security Groups**. Use NACLs when subnet-level stateless allow/deny behavior is required.

---

## 7. VPC Security Patterns

### Public web / private application architecture

`Internet → Route 53 → ALB (public subnets) → App servers (private subnets) → Database (private subnets)`

Security:
- Internet-facing ALB SG allows 443 from clients.
- App SG allows only traffic from ALB SG.
- DB SG allows only app SG.
- Private instances use NAT only when outbound internet is necessary.
- Prefer VPC endpoints for supported AWS services to avoid public internet traversal.

### VPC endpoints

- **Gateway endpoints**: commonly associated with S3 and DynamoDB.
- **Interface endpoints / PrivateLink**: private ENIs for supported services.

> **Decision Rule:** “Private instances must access S3 without NAT or internet” → **S3 gateway endpoint**.

---

## 8. KMS vs CloudHSM

| Feature | KMS | CloudHSM |
|---|---|---|
| Operational model | Highly managed | Customer-managed HSM cluster |
| Tenancy | Multi-tenant managed service | Dedicated HSMs |
| AWS service integration | Excellent | More specialized |
| IAM integration | Native | Customer manages HSM users/permissions |
| HA | Managed by AWS | Deploy multiple HSMs across AZs |
| Best fit | Most AWS encryption | Dedicated HSM/control requirements |

> **Decision Rule:** If the requirement is simply “encrypt AWS data with customer-managed keys,” prefer **KMS**. If the requirement explicitly demands **dedicated HSM control**, consider **CloudHSM**.

---

## 9. Secrets Manager vs Parameter Store

| Requirement | Secrets Manager | Systems Manager Parameter Store |
|---|---|---|
| Purpose-built secret storage | Yes | Supports secure parameters |
| Native rotation workflow | Strong | Less purpose-built |
| RDS credential rotation | Strong integration | Not the main differentiator |
| General config parameters | Possible | Excellent |
| Cost | Paid | Standard tier can be lower-cost |

> **Decision Rule:** “Automatically rotate database credentials” → **Secrets Manager**.  
> “Store application configuration and some encrypted parameters” → **Parameter Store**.

---

## 10. WAF, Shield, GuardDuty, Macie

### AWS WAF
Use for:
- SQL injection
- cross-site scripting
- IP/geo matching
- request rate controls
- HTTP header/body/URI rules

Common placements include CloudFront, ALB, API Gateway and other supported web endpoints.

### AWS Shield
- Shield Standard: baseline managed DDoS protection.
- Shield Advanced: enhanced DDoS capabilities and response features.

### GuardDuty
Detects suspicious behavior using AWS telemetry and threat intelligence.

### Macie
Discovers and classifies sensitive data in **Amazon S3**.

> **Trap:** WAF is not a generic TCP/UDP firewall. It is focused on web requests at Layer 7.

---

## 11. Data Security

### S3
High-yield controls:
- Block Public Access
- IAM and bucket policies
- SSE-S3 / SSE-KMS
- TLS in transit
- versioning
- Object Lock where retention/immutability is needed
- MFA Delete as an additional protection scenario
- CloudFront OAC for private S3 origins

### RDS / Aurora
- Security Groups
- encryption at rest with KMS
- TLS in transit
- IAM authentication where supported
- Secrets Manager integration
- backups/snapshots for recovery

### EBS
- KMS-backed encryption
- snapshots inherit/enforce encryption workflows
- scope is tied to AZ at volume level; snapshots are stored as managed snapshot objects

---

## 12. Encryption Decision Rules

| Requirement | Prefer |
|---|---|
| AWS-managed encryption with minimal key administration | AWS managed key / service-managed encryption |
| Customer controls key policy and lifecycle | Customer-managed KMS key |
| Dedicated HSM | CloudHSM |
| TLS certificate for ALB/API/CloudFront integration | ACM |
| Encrypted S3 with audit/control over key usage | SSE-KMS |
| Client controls encryption before upload | Client-side encryption |

---

## 13. Architecture Patterns

### Pattern: Private workload accessing S3
`EC2 private subnet → S3 Gateway Endpoint → S3`

Why:
- no NAT required for S3 path
- private routing
- endpoint/bucket policy can constrain access

### Pattern: Secure global web application
`Users → CloudFront + WAF → ALB → private EC2/ECS → RDS/Aurora`

Add:
- ACM for TLS
- Shield baseline DDoS protection
- Secrets Manager for credentials
- KMS for encryption

---

## 14. Common Exam Traps

- **SCPs do not grant access.** They restrict the maximum available permissions.
- **NACLs are stateless; SGs are stateful.**
- **Read replicas are not the primary RDS HA mechanism.**
- **WAF ≠ Shield.** Web exploit filtering vs DDoS protection.
- **Cognito ≠ IAM Identity Center.** Application users vs workforce SSO.
- **KMS ≠ CloudHSM.** Managed integrated key service vs dedicated HSM environment.
- **Do not hardcode AWS credentials.** Use roles/temporary credentials.
- A NAT Gateway gives outbound internet access; it is not the preferred answer when a suitable **VPC endpoint** meets the requirement.

---

## 15. 5-Minute Review

- Root: MFA, no daily use.
- Roles > embedded keys.
- Explicit deny wins.
- SCP = guardrail, not grant.
- SG = stateful.
- NACL = stateless.
- Private subnet ≠ automatically no internet; routing determines reachability.
- S3 private access → gateway endpoint.
- PrivateLink/interface endpoint → private service access.
- WAF → Layer 7 web filtering.
- Shield → DDoS.
- GuardDuty → threat detection.
- Macie → sensitive data in S3.
- Secrets Manager → secrets + rotation.
- KMS → default choice for AWS-integrated key management.
- CloudHSM → dedicated HSM requirement.
- ACM → TLS certificates.
- Encrypt at rest + in transit.
- OAC → private S3 origin behind CloudFront.

## Source Coverage

- Official exam guide: Domain 1 task statements and security skill areas.
- Training deck: IAM section; S3 security; Advanced Identity; Security & Encryption; VPC.
- Coverage is strong for core identity, encryption, network controls, WAF/Shield, and S3 security.
