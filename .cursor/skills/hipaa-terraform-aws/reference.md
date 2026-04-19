# Reference: CFR anchors, AWS themes, authoring notes

## HIPAA Security Rule — technical safeguard themes

These are **engineering review themes**, not legal conclusions. Exact implementation is entity-specific.

| CFR (subpart) | Theme | Common AWS / Terraform expressions |
|---------------|-------|--------------------------------------|
| §164.312(a)(1) | Access control | IAM roles not users; least privilege; per-workload roles; internal ALBs; VPN / PrivateLink for admin paths |
| §164.312(b) | Audit controls | CloudTrail; S3 server access logging or data events (cost-aware); VPC flow logs; CloudWatch retention; **avoid logging PHI** |
| §164.312(c)(1) | Integrity | S3 versioning; immutability / Object Lock (legal review); signed URLs with TTL; checksums on pipelines |
| §164.312(c)(2) | Person or entity authentication | Largely application-layer (MFA, certificate-bound identities); infra supports TLS, certificate management, OIDC |
| §164.312(d) | Transmission security | TLS-only listeners; `aws:SecureTransport` deny on buckets; WAF; HSTS at edge where applicable |
| §164.312(e)(1) | Transmission integrity and encryption | Same as above plus message-level security for HL7 / FHIR (often VPN or controlled interfaces — may live outside Terraform) |

**Administrative and physical safeguards** (workforce, facility, contingency plan, evaluations) are largely **not** visible in Terraform. Call them out when a change implies a **policy** gap (for example: branch protection, on-call access to production state, runbooks for incident response).

---

## Pattern hooks (generic)

Examples of how this checklist tends to map onto real architectures, with neutral resource names.

| Pattern | Control intent | Terraform / ops touchpoints |
|---------|----------------|-----------------------------|
| Document ingest bucket | Confidentiality and integrity of ePHI at rest | Dedicated bucket; SSE-KMS with customer-managed key; full Block Public Access; TLS-only policy; lifecycle aligned with records policy |
| EC2 / container with `PutObject` to PHI bucket | Least privilege | Scoped `s3:PutObject` on a specific ARN prefix only; verify **denied** on other buckets |
| Lambda → primary database | Minimize blast radius | Dedicated security group; **one** ingress rule from the Lambda SG; Lambdas in private subnets |
| S3 → SQS → orchestrator | Availability and traceability | Encrypted queues; DLQ + depth alarm; feature flag before enabling notifications |
| LLM / Textract on clinical text | Subprocessor and transmission | BAA confirmed; eligible region and model; verbose payload logging disabled |
| Dataset for fine-tuning or evaluation | Use limitation and process integrity | Train / dev / holdout splits locked in storage; do not "peek" at holdout; version training artifacts and prompts |

---

## Third-party and AI-specific reminders

- **Business Associate Agreements:** AWS services used with PHI must be under an executed BAA and configured per AWS HIPAA guidance for that service.
- **Model training:** Training data containing PHI needs a **lawful basis** under the organization's compliance program; de-identification may be required for some secondary uses.
- **Evaluation metrics:** Publishing benchmarks is fine; **never** publish patient-identifiable content or raw notes in public repos, posts, or screenshots.
- **AI assistant outputs:** Findings should never claim a system *is* HIPAA compliant; they describe **alignment with technical themes** and **gaps to address**.

---

## Skill authoring notes (Cursor-specific)

- Skill directory contains this `reference.md` and a `SKILL.md` with YAML `name` + `description`.
- `description` includes **what** and **when** so the agent can auto-select the skill.
- Keep `SKILL.md` concise; link here for long tables (progressive disclosure).
- Personal skills: `~/.cursor/skills/<name>/`. Project skills (shared with a repo): `<repo>/.cursor/skills/<name>/`.
- Do not place skills in `~/.cursor/skills-cursor/` — that path is reserved for Cursor's built-in skills.

For authoring guidance, see Cursor's create-skill documentation.
