# Part 3: Comparisons & Data

[← Back to Guide](index.md)

Compare techniques by the cost they change. A shorter response, a smaller input file, fewer agent steps, and a lower model rate are different outcomes.

## Choose by mechanism

| Mechanism | What to try | What to measure | Full guidance |
|---|---|---|---|
| Output length | Concise responses, code-only when appropriate, explicit formats | Output tokens, completeness, billed task cost | [Output Control](05-output-control.md) |
| Prompt clarity | Remove filler; state requirements and exceptions | Prompt tokens, corrections, task success | [Prompt Compression](02-prompt-compression.md) |
| Loaded context | Smaller always-on files, scoped instructions, relevant files | Actual loaded context and cache usage | [Context Management](04-context-management.md) |
| Instruction relevance | Remove discoverable, stale, and duplicate guidance | Correctness and cost on the same repo tasks | [Always-On Context Problem](07-agents-md-problem.md) |
| Tool overhead | Scope MCPs; use one shell-output filter; reuse structural maps | Loaded schemas, result size, missed diagnostic detail | [MCP & Tool Costs](08-mcp-tool-costs.md) |
| Rework and agent steps | Appropriate mode, clear plan, targeted verification | Accepted work, retries, calls, total cost | [Workflow Optimization](06-workflow-optimization.md) and [Outcome per Token](13-outcome-per-token.md) |
| Billed rate | Match model and effort to task | Billed cost at comparable task quality | [Model Selection & Pricing](11-models-and-pricing.md) |
| Spending limits | Budgets, cohorts, model-access policy | Spend against budget; blocked useful work | [Enterprise Governance](12-enterprise-governance.md) |

This is a selection guide, not a ranking of universal savings percentages.

## Read evidence in its original scope

| Evidence type | What it establishes | What it does not establish |
|---|---|---|
| Hand-written before/after pair | How wording can change | Actual model output, quality, or typical savings |
| Offline tokenizer count | Text length for that tokenizer | Provider-billed usage or monetary savings |
| Controlled response-style comparison | Output difference for its tasks and controls | Whole-session cost or other models' behavior |
| Repeated verified agent tasks | Task outcomes and usage under that harness | A guarantee for every repo or workflow |
| Tool-vendor benchmark | A candidate worth testing | Independent proof of your bill reduction |

The [Caveman evidence and three-arm protocol](05-output-control.md#caveman-optional-output-style) distinguish default replies from ordinary concision and whole-task cost. The [language comparison](03-language-comparison.md) contains the language tables; the [benchmark discussion](13-outcome-per-token.md#benchmarks-useful-not-decisive) covers harness and score limitations.

## Do not add savings percentages

Two techniques can remove the same tokens. Their effects are not additive.

For illustration, if output accounts for 10% of a bill, cutting that output by 10% saves 1% of the bill before instruction overhead or changed behavior. This is arithmetic, not a measured Caveman result.

Measure input, cache, output, reasoning where exposed, and total billed cost separately. Compare the same task and acceptance checks. For billing units, use [Model Selection & Pricing](11-models-and-pricing.md).

## Record enough to reproduce a claim

Name the source, date, model, skill/tool version, task set, control, run count, tokenizer or provider usage, and quality checks. Label vendor-reported results and unverified estimates.

A result from one task or one run can suggest a test. It should not become a default savings range in this guide.

---

**Next:** [Practical Setup →](10-practical-setup.md)
