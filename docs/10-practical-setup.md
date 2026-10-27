# Part 4: Practical Setup

[← Back to Guide](index.md)

Use this checklist to apply the guide. Follow the linked chapters for explanations, evidence, and tool installation.

## Step 1: Set a concise response default

Create `.github/copilot-instructions.md` in your repository:

```bash
mkdir -p .github
touch .github/copilot-instructions.md
```

Start with ordinary concise language:

```markdown
Lead with the answer. Keep explanations proportional to the task.
Skip greetings, repeated summaries, and unsolicited follow-up offers.
Preserve technical terms, negation, conditions, numbers, units, code, and commands.
Use full sentences when brevity would make an action ambiguous.
For code changes, report what changed and the relevant test result or blocker.
```

This is a starting point, not a measured savings guarantee. For output formats and optional Caveman style, see [Output Control](05-output-control.md).

## Step 2: Add only project-specific guidance

Keep rules the agent needs but cannot reliably discover in the code. Put path-specific rules in scoped instruction files and occasional checklists in on-demand guidance.

See [Context Management](04-context-management.md#scoping-guidance) for placement and [Always-On Context Problem](07-agents-md-problem.md#265-what-to-actually-put-in-context-files) for what to keep.

## Step 3: Choose mode, model, and context

Before a long run:

1. Use the [mode decision guide](06-workflow-optimization.md#253-ask-mode-vs-agent-mode) to decide whether tools are needed.
2. Choose a [model and supported effort level](11-models-and-pricing.md#good-default-heuristics).
3. Close unrelated files and attach only relevant context. Convert rich documents using the [Markdown workflow](04-context-management.md#normalize-non-text-inputs-to-markdown-first).
4. Inspect [loaded tools](08-mcp-tool-costs.md#before-you-optimize-measure-whats-actually-loaded); disable unused servers and extension-provided tools.
5. Follow the [cache and handoff guidance](04-context-management.md#235-caching-store-and-reuse-context-within-prompts) when session settings must change.

For setup, choose only what the workload needs: [RTK](08-mcp-tool-costs.md#277-compress-tool-output-at-the-source-rtk) **or** [snip](08-mcp-tool-costs.md#278-rtk-alternative-snip) for noisy shell results; [Graphify](08-mcp-tool-costs.md#2711-persistent-codebase-maps-graphify) for repeated structural navigation.

## Step 4: Make Coding Agent tasks reproducible

### Prepare the environment

Use `copilot-setup-steps.yml` to pre-install dependencies deterministically. Keep the setup specific to the repo rather than asking the agent to rediscover it.

### Write a precise issue

Name the failure, location, expected behavior, and test:

```text
Bug: login fails when email contains '+' character.
File: src/auth/login.ts, validateEmail() on L42.
Fix: URL-encode the email before passing to the OAuth provider.
Test: add case for "user+tag@example.com" in login.test.ts.
```

Treat this as a worked example, not a diagnosis to apply without inspecting the code.

### Use focused agent profiles when useful

A repeat workflow can use a narrow tool list and task-specific instructions. Do not duplicate the whole project's guidance in each profile. The repo's `agents/token-saver.agent.md` is an existing terse-style example, not a benchmarked cost guarantee or the required default.

### Plan and verify

For multi-step work, use the [plan/execute/verify workflow](13-outcome-per-token.md#plan-first-then-execute-cheaply). Define acceptance checks, know the starting test baseline, and keep the final report tied to evidence.

## Step 5: Review usage and adjust

| Signal | Action |
|---|---|
| Repeated clarification or missing conditions | Use clearer wording and inspect task scope |
| Large loaded-context baseline | Review always-on instructions and active tools |
| Large shell results | Try one output filter; check that errors remain useful |
| Expensive sessions with weak results | Review model choice, retries, plan, and acceptance checks |
| A style rule seems helpful | Use the [three-arm protocol](05-output-control.md#compare-default-concise-and-caveman), not an assumed savings rate |

For history-based review, see [Copilot CLI `/chronicle`](06-workflow-optimization.md#257-close-the-loop-with-chronicle) and [VS Code AI Engineering Coach](06-workflow-optimization.md#258-vs-code-usage-analytics-ai-engineering-coach).

## Organization rollout

Set [budgets, per-user limits, and model policy](12-enterprise-governance.md) outside prompt files. Review spend alongside delivered work before expanding access.

---

**Next:** [Model Selection & Pricing →](11-models-and-pricing.md)
