# CLAUDE.md

Guidance for Claude (and other coding agents) when working in this repository.

## What this repo is

`hipaa-terraform-guardrails` is a small, opinionated set of review guardrails packaged as a **Cursor Agent Skill** and a **plain-text PR checklist** for AWS infrastructure expressed in Terraform that may handle Protected Health Information (PHI).

It is **not**:

- A compliance certification.
- Legal advice.
- A Terraform module that creates resources.

It **is**:

- Documentation and review prompts that help engineers and AI assistants spot common gaps before infrastructure changes are merged or applied.

## Tone and framing rules for the agent

When using or extending this content, Claude should:

1. **Never claim a workload is "HIPAA compliant"** because it adopted these guardrails. Use phrasing like *"aligned with HIPAA Security Rule technical themes"* or *"reduces the risk of common gaps"*.
2. Always pair findings with **concrete Terraform direction** (resource address, attribute, suggested value or pattern), not vague advice.
3. Treat **PHI leaving the VPC** (Bedrock, Textract, SageMaker public endpoints, third-party APIs) as a **subprocessor / BAA decision** that requires human review, not just a code change.
4. Refuse to generate examples that **embed PHI**, real account IDs, real ARNs, or real patient identifiers.
5. Prefer **least-privilege IAM** — replace wildcards (`s3:*`, `kms:*`) with action lists scoped by ARN and condition.

## Default workflow when asked to "review this Terraform"

1. Read the Terraform files and identify **resources that may store, process, or transit PHI**.
2. Walk the [`CHECKLIST.md`](./CHECKLIST.md) sections in order.
3. Output findings grouped as **Must fix before merge / Should fix soon / Compliance debt**.
4. For each finding, name the **resource address**, the **risk theme**, and the **concrete fix**.
5. End with a short **"escalate to security or compliance"** list for items the agent cannot resolve in code.

## Editing this repo

- Keep `SKILL.md` under ~500 lines per Cursor authoring guidance; push long tables into `reference.md`.
- Don't introduce repo-specific or company-specific examples — use **generic resource names** (`aws_s3_bucket.phi_documents`, `aws_kms_key.phi`).
- Don't add binary assets, secrets, real ARNs, or environment-specific values.

## Out of scope

- Application-layer concerns (auth flows, session handling, audit logging in app code).
- Administrative and physical safeguards (workforce training, facility access, BCDR exercises) — flag them when relevant, but they belong in a compliance program, not in this repo.
