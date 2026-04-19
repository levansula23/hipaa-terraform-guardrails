# hipaa-terraform-guardrails

Opinionated review guardrails for **AWS infrastructure defined in Terraform** working with **Protected Health Information (PHI)**. Ships as a portable [Cursor](https://cursor.com) **Agent Skill** plus a standalone **PR review checklist** that any team can adapt.

> **Not legal advice and not a compliance certification.** Using this guidance does not make a workload HIPAA compliant. Compliance requires organizational policies, risk analysis, BAAs, workforce training, and processes that go far beyond Infrastructure-as-Code. See [`CLAUDE.md`](./CLAUDE.md) for how AI assistants should frame their output.

## What this gives you

- A **Cursor skill** at [`.cursor/skills/hipaa-terraform-aws/`](./.cursor/skills/hipaa-terraform-aws/) that auto-applies when an agent sees Terraform changes touching data stores, IAM, networking, logging, KMS, WAF, AI/ML services, or anything labeled HIPAA / PHI / BAA.
- A **plain-text PR checklist** ([`CHECKLIST.md`](./CHECKLIST.md)) you can paste into a GitHub PR template even if you don't use Cursor.
- A **CFR §164.312 theme reference** ([`.cursor/skills/hipaa-terraform-aws/reference.md`](./.cursor/skills/hipaa-terraform-aws/reference.md)) mapping Security Rule technical safeguards to common AWS / Terraform expressions.

## Who this is for

- Platform / DevOps / SRE engineers shipping HIPAA-relevant workloads on AWS.
- Security engineers who want a lightweight, IaC-first review pass before a heavier audit.
- Healthcare AI teams using **Bedrock**, **Textract**, **SageMaker**, or third-party LLMs on data that may include PHI.

## Install (Cursor users)

Either drop this repo's `.cursor/skills/hipaa-terraform-aws/` into:

| Scope | Path |
|-------|------|
| Personal (all your projects) | `~/.cursor/skills/hipaa-terraform-aws/` |
| Project (committed to a repo) | `<repo>/.cursor/skills/hipaa-terraform-aws/` |

…or add this repo as a submodule and symlink the skill folder. Do **not** use `~/.cursor/skills-cursor/` — that path is reserved for Cursor's built-in skills.

## Use without Cursor

Copy [`CHECKLIST.md`](./CHECKLIST.md) into your repo's `.github/PULL_REQUEST_TEMPLATE/` or paste it as a checklist on infrastructure PRs.

## Contributing

Issues and PRs welcome — especially:
- Additional **Terraform anti-patterns** with concrete fixes.
- Mappings for **other clouds** (GCP, Azure) under separate skill folders.
- Translations of the checklist for related frameworks (HITRUST, SOC 2, ISO 27001) — keep wording careful and non-promissory.

## License

GPL-3.0. See [`LICENSE`](./LICENSE).
