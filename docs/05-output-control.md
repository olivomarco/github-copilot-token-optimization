# 2.4 Output Control

[← Back to Guide](index.md)

Ask for the response you need. Start with ordinary concise instructions; keep explanation, warnings, and verification evidence when they help you act.

## 2.4.1 Request Code-Only Responses

For code generation where you already understand the approach:

```text
Code only, no explanation.
```

Or constrain a single task:

```text
Add input validation to processOrder(). Code only.
```

Do not use this to suppress errors, missing requirements, or failed tests. For learning and debugging, ask for the explanation.

## 2.4.2 Constrain Response Format

| Need | Instruction |
|---|---|
| Quick answer | "Answer in one sentence" |
| Short summary | "3 bullet points max" |
| Machine-readable extraction | "Reply as JSON" |
| Comparison | "Table format" |
| Decision | "Yes or no, then one line why" |

These are format choices, not fixed savings rates. Judge whether the result contains enough information to use.

## 2.4.3 System-Level Terse Output

Use the [concise starter template](10-practical-setup.md#step-1-set-a-concise-response-default) for a project default. Override it when a task needs more detail.

## Caveman: optional output style

Caveman can shorten assistant narration. Extra savings over ordinary concise instructions may be small. Measure total task cost and correctness before treating it as a cost optimization.

The **response-style skill** does not compress input context or thinking tokens; its rules add input. Upstream's memory-file compressor and proxy are separate mechanisms.[^honest]

### What has been measured

Evidence checked October 27, 2026. Upstream links are pinned to a source revision, not a promise about later releases.

| Study | Comparison and result | Limits |
|---|---|---|
| Upstream response-style evaluation | Standard Caveman: **3% median output reduction beyond `Answer concisely.`** | Ten prompts, one run, `claude-opus-5-5`, approximate `o200k_base` counts. Tested skill 3.1.0; not rerun after the 3.2.0 rule changes. No reviewed fidelity snapshot.[^honest][^evals] |
| JetBrains, July 2026 | Forced Caveman vs stock Claude Code: **8.5% fewer output tokens** on 82 paired tasks | SkillsBench, `claude-sonnet-5`, low effort. No statistically detectable quality difference (`p = 0.82`), not proof of equivalence.[^jetbrains] |
| Same JetBrains full run, billed cost | Raw skill-arm total **11.6% higher** | One long-context outlier reversed the aggregate cost result. Output savings did not guarantee lower spend.[^jetbrains] |

Different controls, workloads, and skill versions answer different questions. Do not average these results or turn either into a universal savings rate. Upstream publishes no reviewed aggregate result for the old default-verbose-reply headline.[^honest]

### Compare default, concise, and Caveman

Adapted from upstream's three-arm design.[^evals]

| Arm | Extra response-style instruction |
|---|---|
| A: default | None |
| B: concise | `Answer concisely.` |
| C: Caveman | Same concise instruction plus a pinned Caveman skill |

Use identical tasks, repo revisions, models, effort, tools, permissions, and budgets. Isolate style instructions so a preloaded Caveman rule does not contaminate the controls. Keep the task language unchanged.

Repeat each task in each arm, interleave the order, and report the run count and spread. Separate prose Q&A from tool-heavy implementation. Preserve raw outputs and provider usage records.

For every run, record:

- acceptance checks, test results, and missing facts or conditions
- input, output, cache-read/cache-write, and reasoning tokens where exposed
- billed cost, elapsed time, retries, and tool calls

Compare C with B to isolate Caveman's added value; compare B with A to measure generic concision. Report paired per-task differences and total billed cost, including failed runs and outliers. Offline tokenizer counts estimate text length, not provider charges.

Use Caveman where the result stays useful and the measured tradeoff suits the workload. Prefer plain concise instructions where they do as well. This guide has not run its own paid three-arm benchmark.

## Sources

[^honest]: Caveman, [Honest Numbers](https://github.com/JuliusBrussee/caveman/blob/9b68f7ed12b05e7efaff18e65c5a3fc2849fd0a4/docs/HONEST-NUMBERS.md).
[^evals]: Caveman, [evaluation design and snapshot limitations](https://github.com/JuliusBrussee/caveman/blob/9b68f7ed12b05e7efaff18e65c5a3fc2849fd0a4/evals/README.md).
[^jetbrains]: Denis Shiryaev, JetBrains, [Does Speaking to Agents Like Cavemen Really Save 65% of Tokens? We Test](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/).

---

**Next:** [Workflow Optimization →](06-workflow-optimization.md)
