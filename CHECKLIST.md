# PR review checklist — HIPAA-oriented Terraform on AWS

> Paste this into your `.github/PULL_REQUEST_TEMPLATE/` or comment it on infrastructure PRs. Items are **review themes**, not legal conclusions. See [`.cursor/skills/hipaa-terraform-aws/reference.md`](./.cursor/skills/hipaa-terraform-aws/reference.md) for CFR §164.312 anchors.

## 1. Data inventory
- [ ] Listed every new or changed resource that may **store, process, or transit PHI/ePHI**.
- [ ] Confirmed which resources are **subprocessors** (Bedrock, Textract, SageMaker public endpoints, third-party APIs).
- [ ] Documented **data flow** in the PR description (source → transform → destination).

## 2. Encryption
- [ ] **At rest:** SSE-KMS with a customer-managed key for PHI-bearing stores; SSE-S3 only where PHI is impossible.
- [ ] **In transit:** TLS-only enforced (e.g. S3 bucket policy denies `aws:SecureTransport = false`; ALB listeners on HTTPS; databases require TLS).
- [ ] **Key policy** scopes which principals can `kms:Decrypt` and `kms:Encrypt`; rotation enabled.

## 3. Access (IAM)
- [ ] No wildcards on sensitive actions (`s3:*`, `kms:*`, `dynamodb:*`, `secretsmanager:*`).
- [ ] Roles, not users; one role per workload where practical.
- [ ] Conditions used where helpful (`aws:SourceArn`, `aws:PrincipalArn`, `kms:ViaService`, `aws:SourceVpce`).
- [ ] No long-lived access keys introduced; CI uses **OIDC** to assume roles.

## 4. Network
- [ ] Data plane (databases, queues with PHI) lives in **private subnets**; no public IP.
- [ ] Security groups expose **only** the ports needed and **only** to the needed source SG/CIDR.
- [ ] No `0.0.0.0/0` ingress to data ports; admin paths go through VPN, bastion, or **Session Manager**.
- [ ] **VPC endpoints** used for S3 / KMS / SSM / Logs where it removes data-plane egress.

## 5. Audit and logging
- [ ] **CloudTrail** enabled at org/account level; multi-region; log file integrity validation on.
- [ ] **VPC flow logs** enabled.
- [ ] CloudWatch / S3 access logs have **defined retention**.
- [ ] Application and pipeline logs **do not** contain PHI, full prompts/responses, tokens, or note text in production.

## 6. Integrity and recovery
- [ ] S3 versioning enabled where overwrite recovery matters; lifecycle policy reflects records-retention requirements.
- [ ] Backups defined for databases (RDS automated backups / snapshots; SQL backup plan if on EC2).
- [ ] Object Lock or immutability considered for medical-records buckets (after legal review).
- [ ] Plan does not introduce destructive defaults (no implicit deletes of buckets, snapshots, or KMS keys).

## 7. Change safety
- [ ] `terraform plan` reviewed; **no unexpected destroys or replacements** on data-bearing resources.
- [ ] High-risk wiring (S3 → SQS event notifications, IAM trust changes, KMS key policy edits) sits behind a **feature flag / variable** so it can be enabled in a separate apply.
- [ ] State backend is private, encrypted, and access-restricted; state files are not committed.
- [ ] Branch protection on `main` and required status checks are in place.

## 8. Subprocessors and AI/ML
- [ ] AWS service used with PHI is covered by your **executed BAA** and is in a **HIPAA-eligible** configuration for the chosen region/model at implementation time.
- [ ] Model invocation logging and output destinations do not retain PHI against policy.
- [ ] Training and evaluation data are governed (de-identification or BAA scope as appropriate); evaluation **holdout** sets are never used for tuning or fine-tuning.

## 9. Secrets
- [ ] No plaintext secrets in `.tf`, variables, or example tfvars.
- [ ] Secrets sourced from **SSM Parameter Store (SecureString)** or **Secrets Manager**; references via data sources, not literals.
- [ ] CI logs do not echo secret values.

## 10. Output
Group findings as:

- **Must fix before merge** — public data store, broad IAM, missing encryption, PHI in logs.
- **Should fix soon** — missing DLQ/alarm, missing backup/retention, weak TLS policy, missing flow logs.
- **Compliance debt** — documentation, dashboards, runbooks, retest triggers, formal policies.

Each finding should reference the **resource address** and propose a **concrete Terraform change**.
