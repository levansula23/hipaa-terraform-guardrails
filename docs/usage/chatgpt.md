# Using these guardrails with ChatGPT

`PROMPT.md` is written so the section between `<!-- BEGIN SYSTEM PROMPT -->` and `<!-- END SYSTEM PROMPT -->` can be pasted verbatim into any ChatGPT custom-instruction slot.

## Option 1: Custom GPT (recommended for sharing)

1. ChatGPT → **Explore GPTs** → **Create**.
2. In **Configure**:
   - **Name:** `HIPAA Terraform Guardrails`
   - **Description:** `Reviews AWS Terraform that may handle PHI for HIPAA Security Rule technical themes. Not legal advice.`
   - **Instructions:** paste the `PROMPT.md` system-prompt section.
   - **Capabilities:** keep Code Interpreter on if you want it to parse uploaded `.tf` files; web browsing optional.
3. Optionally upload `CHECKLIST.md` and `.cursor/skills/hipaa-terraform-aws/reference.md` as **Knowledge** files so the GPT can cite them.
4. Save as **Anyone with the link** if you want to share.

## Option 2: Project (private, no shareable GPT)

1. ChatGPT → **Projects** → **New project**.
2. In project **Instructions**, paste the `PROMPT.md` system-prompt section.
3. Add `CHECKLIST.md` and `reference.md` as project **files**.
4. Drop Terraform files into the chat to review.

## Option 3: One-off chat

Paste the system-prompt section as your first message, prefixed with:

> *Adopt the following instructions for the rest of this conversation:*

Then attach your Terraform.

## Notes

- ChatGPT cannot run `terraform plan` for you. Paste the plan output as text.
- Do not upload real PHI, real account IDs, or production secrets.
- For sensitive reviews, prefer an enterprise / business plan with appropriate data-handling terms.
