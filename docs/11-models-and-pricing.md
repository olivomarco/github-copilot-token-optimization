# Model Selection & Pricing

[← Back to Guide](index.md)

---

Keep three pricing views separate:

1. **GitHub Copilot public docs** — model availability by plan, Auto behavior, and whatever relative pricing signals GitHub has published
2. **This repo's UBB framing** — the June 1, 2026 usage-based billing shift for Business and Enterprise: AI-credit metering and budgets by enterprise, org, cost center, and user
3. **Vendor API pricing** — per-token pricing from providers like Anthropic, where input and output are billed differently

These are different units. Do not compare them as if they were interchangeable.

## The Official GitHub Docs You Want

- [About Copilot auto model selection](https://docs.github.com/en/copilot/concepts/auto-model-selection)
- [Requests in GitHub Copilot](https://docs.github.com/en/copilot/concepts/billing/copilot-requests)
- [Plans for GitHub Copilot](https://docs.github.com/en/copilot/get-started/plans)

Use these pages to check:

- which models exist
- which plans include them
- which models are free vs premium on paid plans
- which models are currently documented as cheaper or more expensive relative to others
- how Auto selects models

The billing cutover date is **June 1, 2026**. For Business and Enterprise after that date, use AI-credit usage for governance. Premium-request references describe the earlier billing model.

## The Most Important Clarification About Auto

Use **Auto** by default, but understand its limits.

### What Auto actually does

Per [About Copilot auto model selection](https://docs.github.com/en/copilot/concepts/auto-model-selection):

- Auto chooses from the **supported Auto pool**, subject to your plan and org policies
- Auto selection is based on **real-time system health and model performance**
- On paid plans, Auto in Copilot Chat gets a **10% discount**
- Auto stays in the supported default pool rather than escalating into every premium model

Select a higher-cost premium model manually when needed. Auto does not select from every available model.

## What Question Each Pricing View Answers

| Pricing view | Unit | Best use | Source |
|---|---|---|---|
| GitHub public Copilot billing | Model multipliers and, after June 1, AI-credit usage | "What does GitHub currently expose about this model's cost inside Copilot?" | [Requests in GitHub Copilot](https://docs.github.com/en/copilot/concepts/billing/copilot-requests) |
| Repo UBB framing | AI credits and usage-based budgets | "How should we manage spend after the June 1, 2026 billing cutover?" | [Budgets for metered products](https://docs.github.com/en/billing/how-tos/budgets/setting-up-budgets-to-control-spending-on-metered-products) |
| Vendor API pricing | Input/output price per MTok | "How do raw tokens differ in value, especially input vs output?" | [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing) |

## Where Input vs Output Pricing Fits

Use Anthropic's API prices below to understand input/output price differences, not to calculate a Copilot bill. The GitHub docs cited here do not provide an equivalent Copilot per-model input/output table.

| Model | Input / MTok | Output / MTok |
|---|---:|---:|
| Claude Haiku 4.5 | $1 | $5 |
| Claude Sonnet 4.6 | $3 | $15 |
| Claude Opus 4.6 | $5 | $25 |

Source: [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)

This is useful for intuition:

- output tokens are materially more expensive than input tokens
- verbose responses can dominate cost faster than people expect
- output control remains one of the highest-ROI habits in this repo

## Reasoning Effort Is a Separate Cost Lever

Model choice is not the only dial. On supported reasoning-capable models, **thinking effort** or **reasoning effort** changes how much work the model does before answering.

For setup details, see [Practical Setup](10-practical-setup.md#reasoning-effort-another-lever).

### What it changes

- how many tokens the model spends thinking before responding
- how much tool use and preamble it tends to generate
- latency, especially on harder tasks

### Practical guidance

| Situation | Recommended effort | Why |
|---|---|---|
| High-volume, simple chat or classification | `low` | Cheapest. Good when small quality loss is acceptable |
| Typical coding, refactors, tool-heavy work on supported reasoning models | `medium` | Best balance of cost and quality; Anthropic recommends this as the default for Sonnet 4.6 |
| Hard architecture, security review, novel decomposition | `high` or `max` | Spend more only when the task clearly justifies it |

### Important nuance

> **Use reasoning effort only on models that support it.** Non-reasoning models such as GPT-4.1 and GPT-4o do not expose this control.

Choose in this order:

1. choose the right model tier
2. if the model supports it, choose the lowest reasoning effort that still gets the job done

Before moving to a more expensive model, check whether a supported effort setting on the current model meets the task's needs.

## So What Should You Actually Do?

### Default stance

1. **Use Auto by default** for general day-to-day work
2. **Use included or lower-cost models** for trivial tasks when you know you do not need anything stronger
3. **Pin a premium model manually** when the task clearly justifies it
4. **Review org model policy before enablement** so premium access expands intentionally, not by drift

### Good default heuristics

| Task | Default choice | Why |
|---|---|---|
| Syntax lookup, quick explanation, tiny edit | Included model or Auto | Cheapest path, good enough quality |
| Typical implementation, bug fix, refactor | Auto or standard model | Best quality-cost tradeoff |
| Architecture, threat modeling, novel decomposition | Manually pin premium model | Auto will not automatically escalate into the premium lane |

### Anti-patterns

- Leaving an expensive premium model pinned for the whole session
- Changing models mid-chat in a long session without thinking about accumulated context. Prior messages, tool results, and cacheable prefixes can still be part of the next request; switching into a higher-cost lane can make that carried context more expensive than starting fresh
- Enabling/disabling MCP servers mid-thread in long sessions. Tool-surface changes often invalidate stable cached prefixes
- Switching default/custom agent profiles mid-thread during expensive runs. Agent/profile changes can break cache continuity for the same conversation
- Assuming Auto will escalate to Opus when a task gets hard
- Using vendor API prices and Copilot pricing signals as if they were the same metric
- Recommending a model without checking whether the plan includes it
- Turning on every premium model for the whole org before checking who actually needs it

**Cache-protection rule:** choose the lane before work starts and hold it stable in long sessions:

```text
{ model, reasoning effort, loaded skills, active MCP/tool set, agent/profile }
```

If you must change the lane or harness (for example cheap/Auto to premium, `medium` to `high` reasoning effort, or a different skill/tool set), start a fresh chat with only the relevant summary and files. This preserves cache-friendly stability in the original session and avoids dragging long low-value history into a higher-cost request. The exact billing implementation can change by surface and plan, so treat cache loss as a risk to avoid rather than a guaranteed repricing calculation.

## Org Rollout Rule: Review Before Enablement

Teams also need model-access policy.

Use [Configuring access to AI models in Copilot](https://docs.github.com/en/copilot/using-github-copilot/ai-models/configuring-access-to-ai-models-in-copilot) to control which AI models are available. GitHub documents that organization owners and enterprise owners can enable or disable access to AI models for members.

Practical rule:

1. enable cheaper models first
2. review premium model need by workflow, team, and expected ROI
3. enable premium access narrowly
4. watch usage reports and AI-credit consumption before widening access

Use user-level AI credit budgets when you need a direct per-user cap. Remember that code completions and next edit suggestions are not billed in AI credits. More on that in [Enterprise Governance](12-enterprise-governance.md).

## Cross-References in This Repo

- [Practical Setup](10-practical-setup.md) — day-to-day setup and routing advice
- [Practical Setup § Reasoning Effort](10-practical-setup.md#reasoning-effort-another-lever) — the deeper treatment of effort levels
- [Workflow Optimization](06-workflow-optimization.md) — why Auto is still the best default
- [Enterprise Governance](12-enterprise-governance.md) — budgets, user-level caps, model policy, instruction scope
- [Home](index.md#quick-terms) — quick terms and supporting links

---

**Next:** [Enterprise Governance →](12-enterprise-governance.md)
