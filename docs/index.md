# Token Optimization Guide

Reduce GitHub Copilot token spend without losing useful answers or code.

[Start with Part 1](01-why-tokens-matter.md){ .md-button .md-button--primary }
[Jump to Practical Setup](10-practical-setup.md){ .md-button }
<a class="md-button" href="https://olivomarco.github.io/github-copilot-token-optimization/slides/briefing.html">Concise practitioner briefing</a>
<a class="md-button" href="https://olivomarco.github.io/github-copilot-token-optimization/slides/index.html">Full 8-hour workshop</a>

## What This Covers

- How Usage-Based Billing turns token usage into cost
- Why cutting unwanted output can save more than shortening prompts
- How to shrink always-on context, history, and tool overhead
- How model-specific prompt guides improve first-pass quality and reduce rework
- When Ask Mode, Edit Mode, and Agent Mode make financial sense
- How to set enterprise guardrails without relying on unsupported controls
- How to make these practices part of your team's work

## Fastest Wins

1. Constrain output by default: `Code only, no explanation.` and `No explanations unless asked.`
2. Keep `.github/copilot-instructions.md` small and specific.
3. Protect cache in long sessions: keep `{model, reasoning effort, loaded skills, active MCP/tool set, agent/profile}` stable; if you must change one, start a fresh chat with a short handoff summary.
4. Use Ask Mode for questions that do not need tools.
5. Retune prompts and instructions against the official guide for your target model.
6. Disable MCP servers you are not using.
7. Convert DOCX/PDF/Office/media inputs to Markdown before AI work; start with [MarkItDown](https://github.com/microsoft/markitdown).
8. Audit long-running agent sessions and repeated back-and-forth.
9. Install one shell-output filter: [RTK](https://github.com/rtk-ai/rtk) or [`snip`](https://github.com/edouard-claude/snip). These CLI proxies filter `git`, test runners, `grep`, build tools, and other command output before it reaches the agent. Use one filter layer per command path; do not stack them by default.
10. For repeated navigation in large repos, try [Graphify](https://github.com/Graphify-Labs/graphify). It maps code with tree-sitter AST and writes `graphify-out/graph.json`, which agents can query instead of repeating structural file reads. Install: `uv tool install graphifyy`.

## Read by Topic

### Foundations

- [Why Tokens Matter](01-why-tokens-matter.md)

### Techniques

- [Prompt Compression](02-prompt-compression.md)
- [Language Comparison](03-language-comparison.md)
- [Context Management](04-context-management.md)
- [Output Control](05-output-control.md)
- [Workflow Optimization](06-workflow-optimization.md)
- [Always-On Context Problem](07-agents-md-problem.md)
- [MCP & Tool Costs](08-mcp-tool-costs.md)

### Comparisons

- [Comparisons & Data](09-comparisons-data.md)
- [Outcome per Token](13-outcome-per-token.md)

### Implementation

- [Practical Setup](10-practical-setup.md)
- [Model Selection & Pricing](11-models-and-pricing.md)
- [Enterprise Governance](12-enterprise-governance.md)

## Quick Terms

- **UBB**: usage-based billing. Copilot Business and Enterprise spend is tracked through AI-credit usage rather than request counters.
- **AI credits**: the pooled billing unit used after the cutover.
- **Auto mode**: Copilot's default model selector. Use it when you do not need a specific model.
- **Ask Mode**: single-shot interaction. Lowest-overhead choice for simple questions.
- **Agent Mode**: multi-step interaction for tasks that need tools. More capable, but more expensive.
- **Content Exclusion**: admin control for keeping selected repo content out of Copilot context.
- **Format tax**: extra tokens from rich file metadata and layout noise in DOCX, PDF, HTML, slides, spreadsheets, images, and audio/video extraction. Convert to Markdown first.

## Useful Links

- [Official GitHub Copilot docs](https://docs.github.com/copilot)
- [Usage-based billing for organizations and enterprises](https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-organizations-and-enterprises)
- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)
- [Awesome GitHub Copilot Customizations](https://github.com/github/awesome-copilot-customizations)
- [LLMLingua](https://github.com/microsoft/LLMLingua)
- [Caveman project](https://github.com/JuliusBrussee/caveman)
- [RTK — Rust Token Killer](https://github.com/rtk-ai/rtk)
- [snip](https://github.com/edouard-claude/snip) — YAML-extensible shell-output filter for Copilot CLI and other agent shells
- [Tokentop](https://github.com/tokentopapp/tokentop) — local live dashboard for agent token, cost, and burn-rate visibility; supports Copilot CLI
- [minimal-context-tools](https://github.com/SebastienDegodez/copilot-instructions/tree/main/plugins/minimal-context-tools) — skill pack for lower-context CLI search/query patterns
- [Graphify](https://github.com/Graphify-Labs/graphify) — build a persistent knowledge graph of your codebase; agents query `graphify-out/graph.json` instead of re-reading files. Supports GitHub Copilot, VS Code workflows, and other assistants. PyPI package: `graphifyy`
- [Microsoft MarkItDown](https://github.com/microsoft/markitdown) — convert PDF, Office files, images, audio, HTML, ZIP contents, YouTube URLs, EPUBs, and more to Markdown for LLM workflows
- [Marc Bara: "Your .docx Is Wasting 33% of Your AI Budget"](https://medium.com/@marc.bara.iniesta/your-docx-is-wasting-33-of-your-ai-budget-86a3d229d042)
- [Dina Berry: "How I Cut Token Usage from 52% to 13%"](https://dfberry.github.io/2026-05-06-tuning-up-copilot-context) — real measured numbers from a Copilot CLI production setup (Microsoft/GitHub content contributor)

## Notes

- `/chronicle` is **Copilot CLI** only (also available inside JetBrains via interactive Copilot CLI sessions). It is **not** available in VS Code — use [AI Engineering Coach](06-workflow-optimization.md#258-vs-code-usage-analytics-ai-engineering-coach) there. Subcommands include `cost tips`, `improve`, `tips`, `standup`, `search`, and `reindex`.
- Usage-Based Billing is labeled **UBB** in this repo.
