# Using these guardrails with Claude

Two paths: **Claude Code** (CLI / IDE agent) and **claude.ai** (web).

## Claude Code (auto-loaded)

Claude Code automatically reads `CLAUDE.md` from the repo root. If you clone or vendor this repo into another project, copy `CLAUDE.md`, `PROMPT.md`, and `CHECKLIST.md` to the consuming repo's root, or reference this repo as a submodule and have a one-line `CLAUDE.md` that points to the submodule paths.

## claude.ai Project (recommended for web)

1. claude.ai → **Projects** → **Create Project**.
2. **Custom instructions:** paste the system-prompt section from `PROMPT.md` (between the `<!-- BEGIN SYSTEM PROMPT -->` markers).
3. **Project knowledge:** upload `PROMPT.md`, `CHECKLIST.md`, and `.cursor/skills/hipaa-terraform-aws/reference.md`.
4. Drop your Terraform into a project chat to review.

## One-off chat

Paste the system-prompt section as the first message, prefixed with:

> *Adopt the following instructions for the rest of this conversation:*

## Notes

- Anthropic offers BAAs only on specific enterprise tiers. Do not paste real PHI, real account IDs, or production secrets into consumer Claude.ai.
- For sensitive reviews, prefer an enterprise plan with appropriate data-handling terms.
