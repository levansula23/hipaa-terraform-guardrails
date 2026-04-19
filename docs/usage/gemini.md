# Using these guardrails with Gemini

Two paths: **Gemini app / Gems** (web and mobile) and **Gemini Code Assist** (IDE agent in JetBrains and VS Code).

## Gemini Gem (recommended for web)

1. Gemini app → **Gems** → **New Gem**.
2. **Instructions:** paste the system-prompt section from `PROMPT.md` (between the `<!-- BEGIN SYSTEM PROMPT -->` markers).
3. Save. Use the Gem whenever you want to review Terraform.

## Gemini Code Assist (IDE)

Gemini Code Assist will follow general instructions in your prompt. Either:

- Paste the system-prompt section into a fresh chat as the first message, prefixed with *"Adopt the following instructions for the rest of this conversation:"*, or
- If your tier supports per-project / per-workspace style guides, point that style guide at this repo's `PROMPT.md`.

## One-off chat (any Gemini surface)

Attach `PROMPT.md` and `CHECKLIST.md` to the chat, then say:

> *Use PROMPT.md as your operating instructions and walk CHECKLIST.md against the Terraform I attach next.*

## Notes

- Google offers HIPAA-aligned terms only on specific enterprise tiers. Do not paste real PHI, real account IDs, or production secrets into consumer Gemini.
- For sensitive reviews, prefer an enterprise / Workspace plan with appropriate data-handling terms.
