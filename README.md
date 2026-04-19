# hipaa-terraform-guardrails

Opinionated review guardrails for **AWS infrastructure defined in Terraform** working with **Protected Health Information (PHI)**. Ships as **portable AI-assistant instructions** plus a standalone **PR review checklist** that any team can adopt.

> **Not legal advice and not a compliance certification.** Using this guidance does not make a workload HIPAA compliant. Compliance requires organizational policies, risk analysis, BAAs, workforce training, and processes that go far beyond Infrastructure-as-Code. See [`CLAUDE.md`](./CLAUDE.md) and [`PROMPT.md`](./PROMPT.md) for how AI assistants should frame their output.

## What this gives you

- **One canonical system prompt** in [`PROMPT.md`](./PROMPT.md) that any AI assistant can adopt — paste verbatim into Custom GPTs, Claude.ai Projects, Gemini Gems, Mistral Le Chat Agents, or any system-prompt slot.
- **Per-tool entry points** that auto-load where the tool supports it:
  - [`CLAUDE.md`](./CLAUDE.md) for **Claude Code** and **claude.ai Projects**
  - [`AGENTS.md`](./AGENTS.md) for **OpenAI Codex CLI**, **Aider**, **Continue**, **Cline**, **Sourcegraph Cody / Amp**, and other tools that follow the AGENTS.md convention
  - [`.github/copilot-instructions.md`](./.github/copilot-instructions.md) for **GitHub Copilot Chat**
  - [`.cursor/skills/hipaa-terraform-aws/`](./.cursor/skills/hipaa-terraform-aws/) and [`.cursor/rules/hipaa-terraform-aws.mdc`](./.cursor/rules/hipaa-terraform-aws.mdc) for **Cursor**
- A **plain-text PR checklist** ([`CHECKLIST.md`](./CHECKLIST.md)) you can paste into a GitHub PR template even if you don't use any AI tool.
- A **CFR §164.312 theme reference** ([`.cursor/skills/hipaa-terraform-aws/reference.md`](./.cursor/skills/hipaa-terraform-aws/reference.md)) mapping Security Rule technical safeguards to common AWS / Terraform expressions.

## Compatibility matrix

| AI tool | Auto-loaded? | How |
|---|---|---|
| **Cursor** (IDE / CLI) | Yes | Reads `.cursor/skills/hipaa-terraform-aws/` (Agent Skill) and `.cursor/rules/*.mdc` (Project Rule on `*.tf`) |
| **Claude Code** (Anthropic CLI / IDE) | Yes | Reads `CLAUDE.md` from repo root |
| **GitHub Copilot Chat** | Yes | Reads `.github/copilot-instructions.md` from repo root |
| **OpenAI Codex CLI** | Yes | Reads `AGENTS.md` from repo root |
| **Aider, Continue, Cline, Cody, Amp** | Yes (AGENTS.md-compatible tools) | Read `AGENTS.md` from repo root |
| **ChatGPT** (web, Custom GPT, Project) | Manual paste | Paste `PROMPT.md` system-prompt section into custom instructions — see [`docs/usage/chatgpt.md`](./docs/usage/chatgpt.md) |
| **Claude.ai** (web Project) | Manual paste | Paste `PROMPT.md` into Project instructions — see [`docs/usage/claude.md`](./docs/usage/claude.md) |
| **Google Gemini** (Gems, Code Assist) | Manual paste | Paste `PROMPT.md` into a Gem or chat — see [`docs/usage/gemini.md`](./docs/usage/gemini.md) |
| **Mistral Le Chat / Codestral** | Manual paste | Paste `PROMPT.md` into a Le Chat Agent or as a system message — see [`docs/usage/mistral.md`](./docs/usage/mistral.md) |
| **Anything else** | Manual paste | `PROMPT.md` is intentionally generic; it works with any LLM that accepts a system prompt |

## Quickstart per tool

### Cursor

Drop `.cursor/skills/hipaa-terraform-aws/` into either:

| Scope | Path |
|-------|------|
| Personal (all your projects) | `~/.cursor/skills/hipaa-terraform-aws/` |
| Project (committed to a repo) | `<repo>/.cursor/skills/hipaa-terraform-aws/` |

The `.cursor/rules/hipaa-terraform-aws.mdc` Project Rule will auto-attach when you edit `*.tf` / `*.tfvars` / `*.hcl` files.

> Do not place skills in `~/.cursor/skills-cursor/` — that path is reserved for Cursor's built-in skills.

### Claude Code, Copilot Chat, Codex CLI, Aider, Continue, Cline, Cody, Amp

Vendor this repo into your project (clone, submodule, or copy these files to the consuming repo's root):

- `CLAUDE.md`
- `AGENTS.md`
- `.github/copilot-instructions.md`
- `PROMPT.md`
- `CHECKLIST.md`

Each tool will auto-load its respective entry file. All entry files point at `PROMPT.md` and `CHECKLIST.md` as the canonical content, so updates flow from one place.

### ChatGPT, Claude.ai, Gemini, Mistral (web chats)

Open the per-tool guide and paste the `PROMPT.md` system-prompt section into that product's custom-instruction slot:

- [`docs/usage/chatgpt.md`](./docs/usage/chatgpt.md)
- [`docs/usage/claude.md`](./docs/usage/claude.md)
- [`docs/usage/gemini.md`](./docs/usage/gemini.md)
- [`docs/usage/mistral.md`](./docs/usage/mistral.md)

### No AI tool at all

Paste [`CHECKLIST.md`](./CHECKLIST.md) into your repo's `.github/PULL_REQUEST_TEMPLATE/` and walk it manually on infrastructure PRs.

## Who this is for

- Platform / DevOps / SRE engineers shipping HIPAA-relevant workloads on AWS.
- Security engineers who want a lightweight, IaC-first review pass before a heavier audit.
- Healthcare AI teams using **Bedrock**, **Textract**, **SageMaker**, or third-party LLMs on data that may include PHI.

## Repository layout

```
hipaa-terraform-guardrails/
├── README.md
├── LICENSE
├── PROMPT.md                                 # canonical system prompt (paste into any AI tool)
├── CHECKLIST.md                              # plain-text PR checklist
├── CLAUDE.md                                 # Claude Code / claude.ai entry point
├── AGENTS.md                                 # cross-tool agent instructions (Codex CLI, Aider, ...)
├── .github/
│   └── copilot-instructions.md               # GitHub Copilot Chat entry point
├── .cursor/
│   ├── rules/
│   │   └── hipaa-terraform-aws.mdc           # auto-attaches on *.tf / *.tfvars / *.hcl
│   └── skills/
│       └── hipaa-terraform-aws/
│           ├── SKILL.md                      # Cursor Agent Skill (mirrors PROMPT.md)
│           └── reference.md                  # CFR §164.312 themes + authoring notes
└── docs/
    └── usage/
        ├── chatgpt.md
        ├── claude.md
        ├── gemini.md
        └── mistral.md
```

`PROMPT.md`, `SKILL.md`, `CLAUDE.md`, `AGENTS.md`, and `.github/copilot-instructions.md` carry the same hard rules and review workflow. `PROMPT.md` is the source of truth — keep the others aligned when you edit.

## Important data-handling note

Most consumer AI tools (ChatGPT, Claude.ai, Gemini, Mistral Le Chat, Copilot Individual, etc.) are **not** appropriate for real PHI on their default tiers. For sensitive reviews:

- Use enterprise / business tiers with appropriate data-handling terms (and a BAA where the vendor offers one).
- Or run reviews against **synthetic** or **redacted** Terraform that contains no real account IDs, ARNs, hostnames, or PHI.

## Contributing

Issues and PRs welcome — especially:

- Additional **Terraform anti-patterns** with concrete fixes.
- Mappings for **other clouds** (GCP, Azure) under separate skill / rule folders.
- Translations of the checklist for related frameworks (HITRUST, SOC 2, ISO 27001) — keep wording careful and non-promissory.
- Per-tool usage notes for additional assistants.

## License

GPL-3.0. See [`LICENSE`](./LICENSE).
