# Using these guardrails with Mistral (Le Chat / Codestral)

## Le Chat Agent (recommended)

1. Le Chat → **Agents** → **Create Agent**.
2. **System prompt:** paste the system-prompt section from `PROMPT.md` (between the `<!-- BEGIN SYSTEM PROMPT -->` markers).
3. Optionally attach `CHECKLIST.md` and `reference.md` as documents.
4. Save and use the Agent for Terraform reviews.

## Codestral / API

If you call Mistral models programmatically (Codestral, mistral-large, etc.), use the system-prompt section of `PROMPT.md` as the `system` message in your chat-completions request and feed the Terraform files as the `user` message.

## One-off chat

Paste the system-prompt section as the first message, prefixed with:

> *Adopt the following instructions for the rest of this conversation:*

## Notes

- Do not paste real PHI, real account IDs, or production secrets into consumer Le Chat.
- For sensitive reviews, prefer Mistral's enterprise / on-premises offering with appropriate data-handling terms.
