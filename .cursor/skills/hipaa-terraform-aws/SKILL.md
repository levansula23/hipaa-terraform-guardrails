---
name: hipaa-terraform-aws
description: >-
  Reviews AWS infrastructure expressed in Terraform for HIPAA Security Rule
  technical themes (access, audit, integrity, transmission) and common
  healthcare-platform patterns. Use when the user mentions HIPAA, PHI, BAA,
  healthcare compliance, clinical data, Bedrock, Textract, SageMaker, HL7,
  Mirth, SOC 2 security themes, or wants an IaC-focused safeguards pass before
  merge or apply.
---

# HIPAA-oriented Terraform review (AWS)

## Scope and disclaimer

This skill is **not legal advice** and does not determine HIPAA compliance. Covered entities and business associates need organizational policies, risk analysis, BAAs, workforce training, and processes beyond IaC. Use this to **spot gaps in technical controls** and to **align Terraform changes** with common HIPAA Security Rule *themes*. CFR anchors live in [reference.md](reference.md).

When **PHI leaves the VPC** (for example to Amazon Bedrock, Textract, SageMaker public endpoints, or a third-party API), treat that as a **subprocessor / BAA decision**: confirm AWS Artifact BAA coverage, service **HIPAA eligibility** for the account and region at implementation time, and **data minimization** in logs.

---

## When to apply this skill

- Terraform (or CloudFormation / CDK) changes touch **S3, RDS / Aurora, DynamoDB, OpenSearch, Lambda, ECS / EKS, VPC endpoints, KMS, IAM, logging, WAF, CloudFront, API Gateway, Bedrock, Textract, SageMaker, Step Functions, SQS / SNS, EventBridge, or cross-account roles**.
- Pull requests add **new data stores**, **broader IAM**, **public endpoints**, **third-party integrations**, or **ML / LLM** invocations.
- The user is implementing **HL7, FHIR, clinical document, or claims** pipelines (high likelihood of PHI in object stores and messages).
- The user mentions HIPAA, PHI, ePHI, BAA, HITECH, healthcare, clinical, EHR, or auditor.

---

## Review workflow

Walk these sections in order on every relevant PR or change set.

### 1. Data inventory
- Identify **which resources may store, process, or transit PHI**.
- Note any new **subprocessors** (Bedrock, Textract, SageMaker public endpoints, third-party APIs, SaaS webhooks).

### 2. Encryption
- **At rest:** prefer SSE-KMS with a **customer-managed key** for PHI-bearing stores; SSE-S3 only where PHI is impossible.
- **In transit:** enforce TLS (S3 `aws:SecureTransport` deny; ALB listeners on HTTPS; database connections require TLS).
- **KMS key policy:** scope `kms:Decrypt` / `kms:Encrypt` principals; enable rotation.

### 3. Access (IAM)
- Replace wildcards (`s3:*`, `kms:*`, `dynamodb:*`, `secretsmanager:*`) with **explicit action lists**.
- Use **roles** (not users); one role per workload where practical.
- Apply **conditions** where helpful: `aws:SourceArn`, `aws:PrincipalArn`, `kms:ViaService`, `aws:SourceVpce`.
- CI authenticates via **OIDC** to assume roles; no long-lived access keys.

### 4. Network
- Data plane in **private subnets**; no public IP on database, queue, or ML training resources.
- **Security groups**: expose only required ports, only to required source SGs / CIDRs.
- No `0.0.0.0/0` ingress on data ports. Admin paths via VPN, bastion, or **Session Manager**.
- Use **VPC endpoints** for S3 / KMS / SSM / CloudWatch Logs to remove data-plane egress where possible.

### 5. Audit and logging
- **CloudTrail** enabled (org or account, multi-region, log file integrity validation).
- **VPC flow logs** enabled.
- CloudWatch and S3 access log groups have **explicit retention**.
- Application, Lambda, and Step Functions logs **do not** contain PHI, full prompts / responses, tokens, or note text in production.

### 6. Integrity and recovery
- S3 versioning where overwrite recovery matters; lifecycle policy aligned with records-retention.
- Database backups defined (RDS automated backups, manual snapshot cadence, or DB-engine backup plan for EC2).
- **Object Lock / immutability** considered for medical-records buckets (after legal review).
- Plan does not introduce destructive defaults (no implicit destroys of buckets, snapshots, or KMS keys).

### 7. Change safety
- `terraform plan` reviewed; **no unexpected destroys or replacements** on data-bearing resources.
- High-risk wiring (S3 → SQS event notifications, IAM trust edits, KMS key policy edits) behind a **feature flag / variable** so it can be enabled in a separate, smaller apply.
- Terraform **state backend** is private, encrypted, access-restricted; state never committed.
- Branch protection on `main` and required status checks in place.

### 8. Subprocessors and AI / ML
- AWS service used with PHI is covered by an **executed BAA** and configured per AWS HIPAA guidance for that service, region, and model at implementation time.
- **Model invocation logging** does not retain PHI against policy.
- Training and evaluation data are governed (de-identification or BAA scope as appropriate); **evaluation holdout sets are never used** for tuning or fine-tuning.

### 9. Secrets
- No plaintext secrets in `.tf`, variables, or example tfvars.
- Secrets sourced from **SSM Parameter Store (SecureString)** or **Secrets Manager** via data sources, not literals.
- CI logs do not echo secret values.

---

## Output format for findings

Group findings so humans can triage quickly:

1. **Must fix before merge** — e.g. public data store, broad IAM, missing encryption, PHI in logs, destructive plan diff.
2. **Should fix soon** — missing DLQ / alarm, missing backup or retention, weak TLS policy, missing flow logs.
3. **Compliance debt** — documentation, dashboards, runbooks, retest triggers, formal policies.

Each item should name the **resource address** (for example `aws_s3_bucket.phi_documents`), the **risk theme**, and a **concrete Terraform direction** — not vague *"harden security"*.

End with an **"escalate to security or compliance"** list for items that are not fixable in code (BAAs, retention years, training, risk register entries).

---

## Common Terraform anti-patterns to flag

| Anti-pattern | Why it matters | Fix direction |
|---|---|---|
| S3 bucket without `block_public_acls`, `block_public_policy`, `ignore_public_acls`, `restrict_public_buckets` | Public PHI exposure risk | Add an `aws_s3_bucket_public_access_block` for every bucket, even private ones |
| Bucket policy missing `aws:SecureTransport` deny | Allows plaintext requests | Add explicit `Deny` on `aws:SecureTransport = false` |
| `iam:*` or `s3:*` in resource policies | Unbounded blast radius | Replace with explicit action list scoped by resource ARN |
| Lambda with PHI without VPC attachment to private subnets | Data plane on the public internet | Move into private subnets; use VPC endpoints for AWS services |
| CloudWatch log group without `retention_in_days` | Logs grow forever, raise discovery costs | Set a retention aligned with policy (often 30–365 days for ops; longer if legally required) |
| Step Functions / Lambda logging full input / output | Likely PHI in logs | Switch to structured logs that omit message bodies; mask before logging |
| RDS / Aurora without `storage_encrypted = true` and TLS-required parameter group | Encryption-at-rest and in-transit gap | Encrypt with KMS; require TLS at the engine level |
| Bedrock or SageMaker invocation in non-eligible region | Subprocessor configuration risk | Verify HIPAA eligibility for the model and region at implementation time |
| Terraform state on local disk or unencrypted bucket | State may contain sensitive material | Use a private, encrypted backend (S3 + KMS + DynamoDB lock or equivalent) with restricted access |

---

## Additional resources

- CFR §164.312 mapping and authoring notes: [reference.md](reference.md)
- Plain-text PR checklist (works without Cursor): see `CHECKLIST.md` at the repo root
