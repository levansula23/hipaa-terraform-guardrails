# CLAUDE.md

Guidance for Claude when working in this repository or reviewing AWS Terraform that may handle PHI. Applies to **Claude Code** (auto-loaded) and to **claude.ai Projects** when this file is uploaded as project knowledge.

## Repository purpose

`hipaa-terraform-guardrails` is a small, opinionated set of review guardrails packaged as portable AI-assistant instructions plus a plain-text PR checklist for AWS infrastructure expressed in Terraform that may handle Protected Health Information (PHI).

It is **not**:

- A compliance certification.
- Legal advice.
- A Terraform module that creates resources.

It **is**:

- Documentation and review prompts that help engineers and AI assistants spot common gaps before infrastructure changes are merged or applied.

## Canonical instructions

The full system-prompt-style instructions live in [`PROMPT.md`](./PROMPT.md). When asked to "review this Terraform" or anything in scope (see *When to engage* in `PROMPT.md`), follow `PROMPT.md` and the workflow in [`CHECKLIST.md`](./CHECKLIST.md).

## Hard rules

1. Never claim a workload is "HIPAA compliant" because of this guidance. Use phrasing like *"aligned with HIPAA Security Rule technical themes"* or *"reduces the risk of common gaps."*
2. Never embed real PHI, real account IDs, real ARNs, or real patient identifiers in examples or generated code.
3. Treat PHI leaving the VPC (Bedrock, Textract, SageMaker public endpoints, third-party APIs) as a subprocessor / BAA decision that requires human review, not just a code change.
4. Prefer least-privilege IAM — replace wildcards (`s3:*`, `kms:*`, `iam:*`) with explicit action lists scoped by ARN and condition keys.
5. Pair every finding with a concrete Terraform direction (resource address, attribute, suggested value or pattern), not vague advice.

## Default workflow when asked to "review this Terraform"

1. Read the Terraform files and identify resources that may store, process, or transit PHI.
2. Walk the [`CHECKLIST.md`](./CHECKLIST.md) sections in order.
3. Output findings grouped as **Must fix before merge / Should fix soon / Compliance debt**.
4. For each finding, name the resource address, the risk theme, and the concrete fix.
5. End with an **"Escalate to security or compliance"** list for items the agent cannot resolve in code.

## Editing this repo

- Keep `PROMPT.md` and `.cursor/skills/hipaa-terraform-aws/SKILL.md` aligned. If you change one, update the other.
- Push long tables into `.cursor/skills/hipaa-terraform-aws/reference.md`.
- Use generic resource names (`aws_s3_bucket.phi_documents`, `aws_kms_key.phi`).
- No binary assets, no secrets, no real ARNs.

## Out of scope

Application-layer auth flows, in-app audit logging, workforce training, facility access, and BCDR exercises. Flag them when relevant; they belong in a compliance program, not in this repo.
