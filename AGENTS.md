# AGENTS.md

Instructions for any AI coding agent operating in this repository (OpenAI Codex CLI, Aider, Continue, Cline, Sourcegraph Cody, Amp, etc., and any future tool that follows the AGENTS.md convention).

## Repository purpose

This repo packages opinionated review guardrails for **AWS infrastructure expressed in Terraform that may handle PHI under HIPAA**. It is documentation and prompts — it does **not** create AWS resources.

## Canonical instructions

The full system-prompt-style instructions live in [`PROMPT.md`](./PROMPT.md). When asked to "review this Terraform" or anything in scope (see *When to engage* in `PROMPT.md`), follow `PROMPT.md` and the workflow in [`CHECKLIST.md`](./CHECKLIST.md).

## Hard rules (summary)

1. Never claim a workload is "HIPAA compliant" because of this guidance — frame findings as "aligned with HIPAA Security Rule technical themes."
2. Never embed real PHI, real account IDs, real ARNs, or real patient identifiers in examples.
3. Treat PHI leaving the VPC (Bedrock, Textract, SageMaker public endpoints, third-party APIs) as a subprocessor / BAA decision needing human review.
4. Prefer least-privilege IAM; replace wildcards with explicit action lists scoped by ARN.
5. Pair every finding with a concrete Terraform direction (resource address + attribute + suggested value or pattern).

## Output shape

Group findings as **Must fix before merge / Should fix soon / Compliance debt**. End with an *Escalate to security or compliance* list for items not fixable in code.

## Editing this repo

- Keep `PROMPT.md` and `.cursor/skills/hipaa-terraform-aws/SKILL.md` aligned. If you change one, update the other.
- Push long tables into `.cursor/skills/hipaa-terraform-aws/reference.md`.
- Use generic resource names in examples (`aws_s3_bucket.phi_documents`, `aws_kms_key.phi`).
- No binary assets, no secrets, no real ARNs.

## Out of scope

Application-layer auth, in-app audit logging, workforce training, facility access, BCDR exercises.
