# Model Selection & Pricing

Choose the least expensive available model that completes the task reliably. Include retries and review time, not just the rate for one request.

## What determines cost

| Factor | What to check |
|---|---|
| Model | Input, cached-input, and output rates for the actual surface |
| Reasoning effort | Extra reasoning work where the model exposes this control |
| Context | Fresh input versus eligible cached input |
| Workflow | Agent steps, retries, and repeated history |
| Plan and policy | Model availability, discounts, budgets, and organization restrictions |

Use the [GitHub model comparison](https://docs.github.com/en/copilot/reference/ai-models/model-comparison), [supported models](https://docs.github.com/en/copilot/reference/ai-models/supported-models), and [billing documentation](https://docs.github.com/en/copilot/concepts/billing/copilot-requests) for current availability and charges. Direct API list prices from [Anthropic](https://www.anthropic.com/pricing) or [OpenAI](https://openai.com/api/pricing/) are not a Copilot bill estimate.

[Workflow Optimization](06-workflow-optimization.md) covers modes; [Context Management](04-context-management.md#235-caching-store-and-reuse-context-within-prompts) covers cache stability and handoffs.

## Good default heuristics

| Task | Starting point | Escalate when |
|---|---|---|
| Syntax lookup, short explanation, small edit | Auto or a fast, inexpensive model | The answer is wrong or lacks needed context |
| Routine implementation with clear acceptance checks | A model that has handled comparable work | Repeated failures cost more than a stronger model |
| Cross-file diagnosis or ambiguous planning | A stronger reasoning model | Verification still exposes unresolved assumptions |
| Architecture or high-stakes review | A capable model plus explicit human review | Expert review is needed; model choice is not a substitute |
| Execution of an approved, concrete plan | Try a cheaper model in a fresh session | The implementation requires new design decisions |

These are starting points, not a guarantee that a model tier fits every task. Measure accepted outcomes on your own work.

### Auto selection

Auto selects within the supported pool for the surface. Where supported, task-optimized Auto considers task complexity as well as health and availability. Check its current pool, policy restrictions, and any eligible discount rather than treating it as access to every premium model. See [GitHub's Auto-selection documentation](https://docs.github.com/en/copilot/concepts/models/auto-model-selection).

### Reasoning effort

Where effort is configurable, choose it with the task. Use lower effort for straightforward work; increase it when deeper reasoning improves acceptance results enough to justify the extra work. A concise final answer does not show how much reasoning was billed.

Pick model and effort before a long task. If the task changes enough to justify a different configuration, use a short handoff rather than automatically carrying the entire history. Follow the [cache workflow](04-context-management.md#235-caching-store-and-reuse-context-within-prompts).

## Policy and data handling

Availability varies by plan, preview status, geography, organization policy, and client. A model in the picker is not automatically approved for regulated work.

Check legacy annual-plan access, regional restrictions, compliance surcharges, and extended-context/effort availability against the current [model and pricing rules](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing). Do not apply chat cost assumptions to code completions or next edit suggestions; see [Enterprise Governance](12-enterprise-governance.md).

Before routing sensitive work, confirm the applicable provider terms, data-retention controls, and organization policy. GitHub documents [model hosting](https://docs.github.com/en/copilot/reference/ai-models/model-hosting) and [policy controls](https://docs.github.com/en/copilot/concepts/enterprise/policies). Do not infer the same retention terms for every model, particularly previews. Fable models, for example, retain prompts and outputs by default; eligible enterprises need separate approval and configuration for the time-bound zero-retention exemption.

## Judge routing by completed work

Compare cost, elapsed time, acceptance checks, and retries for similar tasks. A cheap request that needs several repair attempts can cost more than one successful stronger-model run.

Keep billing mechanisms separate: model rates, discounts, eligible caching, and session overhead act differently. See [Comparisons & Data](09-comparisons-data.md) before combining savings estimates, and [Enterprise Governance](12-enterprise-governance.md) for organization-level levers.

---

**Next:** [Enterprise Governance →](12-enterprise-governance.md)
