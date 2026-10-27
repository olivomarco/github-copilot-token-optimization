# Outcome per Token

[← Back to Guide](index.md)

---

Aim for **more accepted work per token spent**: merged pull requests, closed bugs, passing tests, and fewer rounds of rework.

A short prompt costs more overall if the agent guesses, edits the wrong files, and starts over. A longer plan can be cheaper when it gets the first implementation right.

## Why Outcome per Token Matters

Judge token spend against completed work.

Tomasz Tunguz describes this as the shift to intelligence per dollar: the application layer competes on the cost of a closed ticket, shipped PR, or resolved support case, not the cheapest raw token.[^tunguz] That maps directly to Copilot work. The useful metric is:

```text
outcome per token = verified work completed / total tokens spent
```

For agentic coding, cost behaves differently than in simple chat. The Microsoft/Stanford paper "How Do AI Agents Spend Your Money?" reports that agentic coding tasks consume roughly **1,000×** more tokens than code chat, runs on the same task can vary by up to **30×**, and higher token usage does not reliably improve accuracy.[^agent-costs]

### What the research shows

| Finding | Why it matters |
|---|---|
| Agentic coding can consume roughly **1,000×** more tokens than code chat | Do not extrapolate chat-cost intuition to agent sessions |
| Same task can vary by up to **30×** across runs | Budget with margin; one run is not a stable cost estimate |
| Higher token usage does not reliably improve accuracy | More exploration is not automatically better work |
| Accuracy often peaks at intermediate cost and then saturates | Defaulting to the biggest model for every step can waste money |
| Input tokens dominate agentic cost | Context hygiene matters as much as output terseness |
| Models underestimate their own token use | Do not trust an agent's pre-task cost guess; use budgets and stop rules |
| Token efficiency varies by model independent of pass rate | Compare outcome and cost together, not benchmark score alone |

## The Outcome-per-Token Loop

Use these habits to reduce wasted work:

![Outcome-per-token loop: pick the task shape, plan before code, route the model, target context, verify evidence, and close cleanly.](assets/diagrams/outcome-per-token-loop.svg)

1. pick the right task shape
2. plan before implementation
3. route the right model to the right phase
4. preserve clean context and cache boundaries
5. verify before claiming done

Watch for vague prompts, oversized context, expensive models left selected, missing acceptance criteria, and tests run only after extensive edits. These patterns make rework harder to avoid.

## Prompt Skills as Superpowers

The community project [`obra/superpowers`](https://github.com/obra/superpowers) packages repeatable agent practices as skills.[^superpowers] It is not an official GitHub product. This chapter uses its approach to organize practices that can reduce rework:

| Skill | What it does | Token effect |
|---|---|---|
| Brainstorming | Explore multiple approaches before choosing | Prevents early lock-in and wrong-direction code |
| Planning | Converts intent into file-level steps and checks | Reduces guessing during execution |
| Greenness | Keeps tests passing through the work | Avoids debugging unknown baseline failures |
| Verification before completion | Requires evidence before "done" | Prevents false completion and rework |
| Impeccable close | Finishes cleanly with criteria, tests, and PR summary aligned | Prevents review churn and follow-up agent sessions |
| Branch-close discipline | Ends branch/session cleanly after merge | Prevents stale context from leaking into next task |

The skills below are patterns. The public libraries that follow are example implementations of those patterns, not mandatory dependencies.

## Skill Libraries Worth Borrowing From

Borrow skills that clarify requirements, tool use, tests, handoffs, or review. These are community libraries, not official GitHub or Microsoft guidance. Their benefits are mostly qualitative and experience-based, not proof of universal token savings.

| Library | Best borrowed skill | Use when | Caveat |
|---|---|---|---|
| [`obra/superpowers`](https://github.com/obra/superpowers) | TDD, planning, verification, branch finish | Need broad agent SDLC discipline | Community framework |
| [`softaworks/agent-toolkit`](https://github.com/softaworks/agent-toolkit) | Planning orchestration, handoff, entropy reduction | Complex features, long sessions, bloated instruction files | Personal toolkit; qualitative claims |
| [`catpilotai/catpilot-ai-guardrails`](https://github.com/catpilotai/catpilot-ai-guardrails) | Security and tool-loop guardrails | Agents touch secrets, cloud, DB, Docker, or supply chain | Guidance, not runtime enforcement |
| [`vercel-labs/agent-browser`](https://github.com/vercel-labs/agent-browser/blob/main/skills/agent-browser/SKILL.md) | Snapshot, wait, and evidence discipline | Agents test browser UIs | Browser-specific; installed CLI content is authoritative |
| [`vercel-labs/writing-guidelines`](https://github.com/vercel-labs/writing-guidelines) | Plan-as-prompt, output review, AI-tell detection | Docs, PR descriptions, specs, generated prose | Editorial guidance, not measured token reduction |
| [`mattpocock/skills`](https://github.com/mattpocock/skills) | TDD, bug diagnosis, code review, domain modeling | Engineering tasks need sharper loops | Examples skew Claude and TypeScript workflows |
| [`PramodDutta/qaskills`](https://github.com/PramodDutta/qaskills) | QA and test-generation skills | Need Playwright, API, BDD, security, accessibility, or bug-report depth | Large skill files; early project |

### Skill Library Star History

[![Skill library Star History Chart](https://api.star-history.com/svg?repos=obra/superpowers,softaworks/agent-toolkit,catpilotai/catpilot-ai-guardrails,vercel-labs/agent-browser,vercel-labs/writing-guidelines,mattpocock/skills,PramodDutta/qaskills&type=timeline)](https://www.star-history.com/#obra/superpowers&softaworks/agent-toolkit&catpilotai/catpilot-ai-guardrails&vercel-labs/agent-browser&vercel-labs/writing-guidelines&mattpocock/skills&PramodDutta/qaskills&timeline)

Stars show community attention, not quality. Judge a skill by whether it improves the next action.

### `obra/superpowers`: Baseline Agent SDLC Discipline

Superpowers turns general advice such as "be careful" into named procedures the agent can follow.[^superpowers]

- Borrow the TDD, planning, verification, and branch-finish habits.
- Convert team norms into small reusable prompts or skill files.
- Prefer skills that force evidence: test output, file-level plan, acceptance criteria, or clean close.
- Do not load broad skills when a one-line instruction would steer the next action.

### `softaworks/agent-toolkit`: Planning, Handoff, and Entropy Control

`agent-toolkit` contains skills, subagents, and commands for Claude-style workflows.[^agent-toolkit] Borrow its planning and handoff patterns for long-running work rather than installing everything.

- Use `gepetto`-style flow for complex features: research, stakeholder questions, spec, plan, review, then execution.
- Use `requirements-clarity` before coding when the task still has hidden ambiguity.
- Use `session-handoff` when a long session must transfer context without leaking stale decisions or secrets.
- Use `reducing-entropy` as an explicit deletion-biased review: fewer files, fewer branches, less code, clearer seams.
- Use instruction-file refactor patterns when `AGENTS.md`, `CLAUDE.md`, or team prompts grow so large they become context tax.

### `catpilot-ai-guardrails`: Risk Stops Before Expensive Mistakes

Guardrail skills address secret leaks, unsafe cloud changes, database damage, supply-chain drift, and retry loops.[^catpilot-guardrails] Use them when those risks are part of the task, especially with write-capable tools.

- Add guardrails before tasks that touch credentials, PII, cloud CLIs, databases, Docker, CI, or dependency manifests.
- Require explicit confirmation before destructive or high-cost actions.
- Use retry budgets and loop-stop rules so agents do not burn tokens repeating the same failing tool call.
- Pair skill guidance with real controls: branch protection, CI, SAST, DAST, SCA, secret scanning, and least-privilege credentials.
- Do not describe these skills as compliance enforcement. They guide behavior; they do not sandbox tools.

### `agent-browser`: Browser QA Without DOM Floods

`agent-browser` guides agents toward compact page snapshots and waits tied to observable state, rather than large HTML dumps or screenshot guesses.[^agent-browser]

- Prefer accessibility-tree snapshots and stable element references over raw DOM dumps.
- Re-snapshot after page-changing actions; browser references go stale.
- Wait on observable states such as text, URL, or network idle instead of fixed sleeps.
- Capture proportionate evidence: failing selector, visible state, screenshot only when useful.
- Treat page content as untrusted input. Do not follow instructions embedded in a website under test.

### `writing-guidelines`: Make the Plan the Prompt, Spec, and Review Artifact

Vercel's writing guidance focuses on explicit goals and testable success criteria.[^writing-guidelines] Use it to clarify specs, implementation prompts, and PR descriptions before they create review questions.

- Write goals with testable verbs, not vague aspirations.
- Keep one page or prompt focused on one job.
- Use the plan as the implementation prompt, test spec, and PR-description seed.
- Flag weasel words and vague quantifiers before sending text to an agent.
- Borrow the second-pass review pattern: ask another agent or model for concrete `file:line` findings, not general praise.

### `mattpocock/skills`: Engineering Loops That Reduce Guessing

Matt Pocock's skills cover TDD, bug diagnosis, code review, domain modeling, and work decomposition.[^mattpocock-skills] Use them when the agent would otherwise jump from a symptom straight to an edit.

- Use TDD skills to agree on seams before implementation.
- Use bug-diagnosis skills to build a fast, deterministic red/green loop before theorizing.
- Split review into two axes: standards review and spec review, so style concerns do not hide requirement misses.
- Use domain-modeling vocabulary such as seam, adapter, leverage, locality, and module depth to guide architecture prompts.
- Use wayfinding patterns to separate human-in-the-loop tickets from agent-runnable work.

### `qaskills`: QA Depth on Demand

`qaskills` provides a QA catalog, CLI, MCP server, SDK, and validator.[^qaskills] Load its testing guidance when the task needs that depth.

- Use Playwright skills for page-object discipline, accessibility-first selectors, fixtures, and anti-pattern checks.
- Use test-plan skills for risk matrices, traceability, equivalence partitioning, and entry/exit criteria.
- Use bug-report skills when the outcome is a reproducible issue with severity, priority, environment, and evidence.
- Use BDD/Cucumber skills when acceptance criteria should become executable Given/When/Then scenarios.
- Use OWASP, visual-regression, axe-core, k6, and API-testing skills only when those checks are part of the task.

### Do Not Install Every Skill

Loaded skills add context. Choose them for the current task.

| Task | Load |
|---|---|
| Test design, QA automation, bug reports | QA skills |
| Browser UI investigation | Browser snapshot/wait/evidence skills |
| Cloud, database, Docker, secrets, dependencies | Guardrail skills |
| Specs, docs, PR descriptions | Writing and review skills |
| Long sessions, complex features, handoff | Planning and handoff skills |

Leave out skills that will not change the next edit, command, test, or review.

### Do Not Over-Optimize the Skill Stack

Do not let skill selection delay the work. For most tasks, start with a clear plan and one review of its assumptions.

Use a simple `plan + grill-me` loop before adding more machinery:

1. write the plan with acceptance criteria, likely files, risks, and verification command
2. ask a second pass to grill the plan: missing edge cases, wrong assumptions, cheaper path, and failure modes
3. revise once, then execute

Add specialized skills only when they change the next action. If the task is not security-heavy, browser-heavy, QA-heavy, or handoff-heavy, more skill loading is probably context tax.

### Brainstorming Skill

Use this before the first edit when requirements are ambiguous.

```text
Before coding, list three implementation approaches.
For each: files likely touched, risks, test strategy, and token/cost risk.
Do not edit files.
```

Compare approaches before committing to one that may need to be undone.

### Planning Skill

Planning turns "build X" into executable steps.

Use the official VS Code [Plan agent](https://code.visualstudio.com/docs/agents/planning) when available. It can be selected from the agent dropdown or invoked with `/plan`, generates a high-level plan plus implementation and verification steps, and supports separate model settings for planning and implementation through `chat.planAgent.defaultModel` and `github.copilot.chat.implementAgent.model`.[^plan-agent]

For GitHub.com tasks, Copilot cloud agent supports a research, plan, iterate flow: ask it to research the repo, iterate on a plan, then implement the agreed plan only when ready.[^cloud-plan]

Important caveats:

- VS Code Plan agent session memory is cleared when the conversation ends. Save important plans externally before closing the session.[^plan-agent]
- Copilot cloud agent planning and iteration before creating a PR are GitHub.com capabilities; integrations such as Azure Boards, JIRA, Linear, Slack, or Teams support direct PR creation only.[^cloud-agent]
- Cloud agent sessions have a 59-minute hard limit. Break large work into smaller tasks.[^cloud-agent]
- Business and Enterprise users need the relevant admin policy enabled before using cloud agent.[^cloud-agent]

### Greenness Skill

"Greenness" is this guide's label for a simple discipline: keep the test baseline green.

Before asking an agent to modify code:

1. know whether tests pass now
2. tell the agent the baseline
3. ask it to preserve that baseline
4. run tests before accepting completion

If tests start red and the agent does not know that, it spends tokens debugging pre-existing failures. If tests go red during the task and the agent keeps editing, it compounds uncertainty.

### Verification-before-Completion Skill

Do not accept "done" without evidence.

```text
Before you report completion, run the targeted tests or build,
state the exact command, and confirm each acceptance criterion.
```

Verification helps catch incomplete work before review or another agent session.

### Impeccable Close Skill

"Impeccable close" is this guide's label, not an official Copilot concept.

A good close includes:

1. accepted criteria checked
2. tests/build run where relevant
3. no accidental scope creep
4. no stale TODOs or commented-out attempts
5. PR summary matches the actual diff
6. next step named only if one is truly needed

A clear close reduces the need to reconstruct what changed during review and follow-up work.

### Branch-Close Discipline

After merge, close the branch and any long-running agent session. Do not keep using the same context for the next unrelated task.

Stale sessions accumulate decisions, tool output, file reads, and abandoned approaches. Every later prompt may drag that history forward as input tokens. A clean close keeps the next task from paying for the previous one.

## Plan First, Then Execute Cheaply

The strongest pattern is two separate phases:

![Plan first, execute cheaply: plan with a strong reasoning model, save acceptance criteria, execute in a fresh cheaper session, then verify or revise the plan.](assets/diagrams/plan-execute-cheaply.svg)

GitHub's [Optimize AI Usage](https://docs.github.com/en/copilot/tutorials/optimize-ai-usage) guidance makes the same point: defaulting to the most capable model can increase token usage without improving the outcome, and overusing reasoning models in execution-heavy tasks can reduce quality by making the model overthink or introduce unnecessary changes.[^optimize-ai] The same page gives the practical rule: plan with a strong reasoning model, then implement with a cheaper model.

Why fresh session matters:

- the planning conversation does not get re-sent on every execution turn
- the execution context starts clean
- the plan becomes a stable, cache-friendly prefix
- model routing is deliberate instead of accidental

This is the expanded version of [Plan First, Then Execute §2.5.9](06-workflow-optimization.md#259-plan-first-then-execute-and-route-the-phases).

Use [Model Selection & Pricing](11-models-and-pricing.md) to choose planning and execution models, set effort, and check pricing and policy restrictions.

## Benchmarks: Useful, Not Decisive

Benchmarks help choose lanes. They do not replace measurement on your repo.

| Benchmark | Measures | Why it matters | Caveat |
|---|---|---|---|
| [SWE-bench Verified](https://www.swebench.com/verified.html) | 500 human-verified Python GitHub issues | Classic software-engineering proxy | Python-heavy, static, possible contamination risk |
| [SWE-bench Pro](https://labs.scale.com/leaderboard/swe_bench_pro_public) | Harder professional repo tasks | Shows enterprise difficulty cliff | Scores depend on scaffold and current live leaderboard |
| [SWE-bench Live](https://swe-bench-live.github.io/) | Continuously updated issues | Reduces saturation and memorization | Dynamic scores shift over time |
| [DeepSWE](https://github.com/datacurve-ai/deep-swe) | 113 original long-horizon tasks across several languages | Good coding-agent and cost-per-task lens | Small task count; reasoning tier changes results |
| [Terminal-Bench](https://www.tbench.ai/) | Terminal and shell workflows | Maps to build/test/devops agent work | Small task count; terminal skill is not all coding skill |
| [Artificial Analysis](https://artificialanalysis.ai/methodology) | Intelligence, pricing, latency, provider comparison | Good cost/intelligence scatter plot | Composite scores may not match coding-only needs |

### Score Snapshot

| Benchmark | Model / condition | Score | Confidence |
|---|---|---:|---|
| SWE-bench Verified | Top models at SWE-bench Pro paper-era cross-reference | >70% | Verified from Scale AI Pro page |
| SWE-bench Pro public | GPT-5, paper-era | 23.3% | Verified |
| SWE-bench Pro public | Claude Opus 4.1, paper-era | 23.1% | Verified |
| SWE-bench Pro private | GPT-5, paper-era | 14.9% | Verified |
| Terminal-Bench 2.0 | Frontier models | <65% | Verified from benchmark abstract |
| DeepSWE | GPT-5.6 Sol `[max]` | 72.7% | Directional; third-party mirror and reasoning-tier dependent |
| DeepSWE | Top-three spread | <3.1 points | Directional; third-party mirror and reasoning-tier dependent |

Use benchmark numbers with labels:

- **Verified**: source directly read from primary leaderboard or paper.
- **Directional**: aggregator or secondary source.
- **Anecdotal**: Reddit, Discord, social media, single-session reports.

## Why Harness Matters

Benchmark scores depend on the setup as well as the model:

- agent scaffold
- tool access
- retrieval strategy
- reasoning effort
- cache pricing assumptions
- task language mix
- benchmark version
- whether the run is single-attempt or multi-attempt

SWE-bench itself notes that versions using different action formats are not directly comparable. DeepSWE entries often include reasoning level in the model name, such as `[max]`. Artificial Analysis may answer a different question: broad intelligence per dollar, not pure coding-agent pass rate.[^swebench][^deepswe][^artificial-analysis]

Use benchmarks to pick candidates. Use your own repo tasks to pick defaults.

## A Practical Decision Checklist

Before starting an expensive agent session:

1. **Is the task actually agentic?** If not, use Ask mode or inline completion.
2. **Is the baseline green?** If not, fix or record it first.
3. **Is the plan written?** If not, plan first.
4. **Can execution run on a cheaper model?** If yes, do that in a fresh session.
5. **Is context targeted?** Attach only the plan and relevant files.
6. **Are acceptance criteria explicit?** If not, write them before execution.
7. **Is verification defined?** Name the test/build/check command up front.
8. **Will a model switch invalidate cache?** If yes, start fresh instead.
9. **Is the model policy allowed for this org/customer?** Check admin and compliance constraints.
10. **Will the close be clean?** Require test evidence and a concise summary.

## Cross-References

- [Workflow Optimization §2.5.9](06-workflow-optimization.md#259-plan-first-then-execute-and-route-the-phases) — shorter version of the plan-first habit
- [Context Management](04-context-management.md) — cache and context hygiene
- [Output Control](05-output-control.md) — output token reduction
- [Practical Setup](10-practical-setup.md) — setup and operating habits
- [Model Selection & Pricing](11-models-and-pricing.md) — model and pricing surfaces
- [Enterprise Governance](12-enterprise-governance.md) — budgets, model policy, and admin rollout

## References

[^agent-costs]: Longju Bai et al., ["How Do AI Agents Spend Your Money? Analyzing and Predicting Token Consumption in Agentic Coding Tasks"](https://arxiv.org/abs/2604.22750), arXiv:2604.22750.

[^tunguz]: Tomasz Tunguz, ["Intelligence Per Dollar"](https://tomtunguz.com/tokens-per-result).

[^superpowers]: [`obra/superpowers`](https://github.com/obra/superpowers), community agentic-skills framework.

[^agent-toolkit]: [`softaworks/agent-toolkit`](https://github.com/softaworks/agent-toolkit), community agent skill toolkit.

[^catpilot-guardrails]: [`catpilotai/catpilot-ai-guardrails`](https://github.com/catpilotai/catpilot-ai-guardrails), community security guardrail skills.

[^agent-browser]: [`vercel-labs/agent-browser` agent browser skill](https://github.com/vercel-labs/agent-browser/blob/main/skills/agent-browser/SKILL.md).

[^writing-guidelines]: [`vercel-labs/writing-guidelines`](https://github.com/vercel-labs/writing-guidelines), practitioner writing and review guidance.

[^mattpocock-skills]: [`mattpocock/skills`](https://github.com/mattpocock/skills), community engineering skill collection.

[^qaskills]: [`PramodDutta/qaskills`](https://github.com/PramodDutta/qaskills), QA skill catalog and tooling.

[^plan-agent]: VS Code Docs, ["Planning with Copilot"](https://code.visualstudio.com/docs/agents/planning).

[^cloud-plan]: GitHub Docs, ["Use Copilot agents: Research, plan, iterate"](https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/research-plan-iterate).

[^cloud-agent]: GitHub Docs, ["About Copilot cloud agent"](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent).

[^optimize-ai]: GitHub Docs, ["Optimize AI Usage"](https://docs.github.com/en/copilot/tutorials/optimize-ai-usage).

[^swebench]: [SWE-bench Verified](https://www.swebench.com/verified.html).

[^deepswe]: [DeepSWE](https://github.com/datacurve-ai/deep-swe).

[^artificial-analysis]: [Artificial Analysis methodology](https://artificialanalysis.ai/methodology).

---

**Next:** [Back to Home →](index.md)
