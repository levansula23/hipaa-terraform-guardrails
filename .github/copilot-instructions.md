# Copilot instructions

GitHub Copilot Chat: when working in this repository or when reviewing AWS Terraform that may handle PHI, follow the canonical instructions in [`/PROMPT.md`](../PROMPT.md) and the checklist in [`/CHECKLIST.md`](../CHECKLIST.md).

## Hard rules

1. Never claim a workload is "HIPAA compliant" because of this guidance — frame findings as "aligned with HIPAA Security Rule technical themes."
2. Never embed real PHI, real account IDs, real ARNs, or real patient identifiers in examples or generated code.
3. Treat PHI leaving the VPC (Bedrock, Textract, SageMaker public endpoints, third-party APIs) as a subprocessor / BAA decision requiring human review.
4. Prefer least-privilege IAM. Replace wildcards (`s3:*`, `kms:*`, `iam:*`) with explicit action lists scoped by ARN.
5. Pair every finding with a concrete Terraform direction (resource address, attribute, suggested value).

## Output shape

Group findings as **Must fix before merge / Should fix soon / Compliance debt**. End with an *Escalate to security or compliance* list for items not fixable in code.
