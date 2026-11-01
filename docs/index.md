# Token Optimization Guide

Spend fewer tokens on unnecessary context, narration, and rework. Keep the information needed to complete and verify the task.

<div class="guide-entry" markdown="1">

<div class="guide-entry__actions" markdown="1">

[Start with Practical Setup](10-practical-setup.md){ .md-button .md-button--primary }
[Understand token costs](01-why-tokens-matter.md){ .md-button }

</div>

<div class="guide-entry__presentations" markdown="1">

[Practitioner briefing](https://olivomarco.github.io/github-copilot-token-optimization/slides/briefing.html)
[Full 8-hour workshop](https://olivomarco.github.io/github-copilot-token-optimization/slides/index.html)

</div>

</div>

Community guidance, not official GitHub or Microsoft policy.

## Find the source of waste

| Mechanism | Read here |
|---|---|
| Response length and format | [Output Control](05-output-control.md) |
| Request clarity and shorthand | [Prompt Compression](02-prompt-compression.md) and [Language Comparison](03-language-comparison.md) |
| Loaded context, instruction scope, file conversion, cache/handoffs | [Context Management](04-context-management.md) |
| Relevance of always-on instructions | [Always-On Context Problem](07-agents-md-problem.md) |
| Tool definitions, command output, and codebase maps | [MCP & Tool Costs](08-mcp-tool-costs.md) |
| Modes and workflow-specific habits | [Workflow Optimization](06-workflow-optimization.md) |
| Planning, acceptance checks, and rework | [Outcome per Token](13-outcome-per-token.md) |
| Model rates, routing, and effort | [Model Selection & Pricing](11-models-and-pricing.md) |
| Organization budgets and access | [Enterprise Governance](12-enterprise-governance.md) |
| Evidence and comparisons | [Comparisons & Data](09-comparisons-data.md) |

## Start concise; test stronger style rules

Ordinary concise language is the default. Caveman remains an option, but shorter narration does not guarantee a cheaper agent task. Read the [scoped evidence and three-arm comparison](05-output-control.md#caveman-optional-output-style) before adopting it for cost savings.

<details markdown="1">
<summary>Terms and reference links</summary>

### Quick terms

- **Input**: prompts plus loaded instructions, files, history, and tool definitions.
- **Output**: generated tokens; visible prose is only part of an agent's work.
- **Cached input**: eligible reused context, billed under the applicable cache rate.
- **UBB**: usage-based billing. Check current plan terms and AI-credit rules.
- **Mode**: the workflow surface, such as Ask, Edit, or Agent; separate from model choice.
- **Format tax**: layout or extraction noise that can add input without helping the task.

### Reference links

- [Official GitHub Copilot docs](https://docs.github.com/copilot)
- [Usage-based billing for organizations and enterprises](https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-organizations-and-enterprises)
- [Awesome GitHub Copilot Customizations](https://github.com/github/awesome-copilot-customizations)
- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)
- [Output-style evidence and sources](05-output-control.md#sources)
- [Tool catalog and setup](08-mcp-tool-costs.md)
- [CLI coaching and VS Code analytics](06-workflow-optimization.md#257-close-the-loop-with-chronicle)

</details>

Measurements need a baseline, workload, model, and unit. Token reductions from different mechanisms are not additive; see [Comparisons & Data](09-comparisons-data.md).
