# Token Optimization Guide for GitHub Copilot

Reduce unnecessary input, output, and rework. Judge changes by useful completed work and total cost, not token counts alone.

> [!IMPORTANT]
> Community guidance, not official GitHub or Microsoft policy. Verify product behavior, pricing, and organization controls in the [official Copilot docs](https://docs.github.com/copilot).

## Start here

[Practical Setup](docs/10-practical-setup.md) is the implementation checklist. [Why Tokens Matter](docs/01-why-tokens-matter.md) explains the cost model.

Start with concise plain language, relevant context, and an appropriate mode/model. Add tools or stronger style rules only when they address waste you can identify.

![Token cost anatomy: input includes hidden context; output and eligible cached input have separate rates.](docs/assets/diagrams/token-cost-anatomy.svg)

## Choose the mechanism

| What needs attention | Canonical chapter |
|---|---|
| Long answers or optional Caveman style | [Output Control](docs/05-output-control.md) |
| Unclear or wordy requests | [Prompt Compression](docs/02-prompt-compression.md) |
| Language/tokenizer tradeoffs | [Language Comparison](docs/03-language-comparison.md) |
| Loaded files, scoped instructions, Markdown conversion, cache/handoffs | [Context Management](docs/04-context-management.md) |
| Whether always-on guidance earns its place | [Always-On Context Problem](docs/07-agents-md-problem.md) |
| MCP overhead, RTK/snip, and Graphify setup | [MCP & Tool Costs](docs/08-mcp-tool-costs.md) |
| Ask/Edit/Agent choices and workflow-specific habits | [Workflow Optimization](docs/06-workflow-optimization.md) |
| Plans, verification, and avoiding rework | [Outcome per Token](docs/13-outcome-per-token.md) |
| Model routing, effort, availability, and pricing | [Model Selection & Pricing](docs/11-models-and-pricing.md) |
| Budgets, access policy, and enterprise controls | [Enterprise Governance](docs/12-enterprise-governance.md) |
| How to compare claims and measurements | [Comparisons & Data](docs/09-comparisons-data.md) |

## Caveman: optional, not the default

Start with ordinary concise instructions. The [evidence review](docs/05-output-control.md#caveman-optional-output-style) separates output-length changes from whole-task cost and explains why older headline claims do not establish savings on your workload.

Use the [default/concise/Caveman protocol](docs/05-output-control.md#compare-default-concise-and-caveman) to test added value beyond `Answer concisely.` This guide has not run its own paid three-arm benchmark.

## Presentations and reference

- [Practitioner briefing (18 slides)](slides/briefing.html)
- [Full customer workshop (8 hours)](slides/index.html)
- [Guide home and reference links](docs/index.md)

Savings depend on workload, model, host, and billing. Do not add percentages from different mechanisms or treat an output-only result as a bill reduction.
