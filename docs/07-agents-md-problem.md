# 2.6 The Always-On Context Problem — Why Less Is More

[← Back to Guide](index.md)

---

> `AGENTS.md`, `.github/copilot-instructions.md`, and `CLAUDE.md` have different scopes and tool support. A repo can use more than one, but each adds context when its tool loads it. The research below tested `AGENTS.md`. Use it to inform pruning of other always-on files, not as a separate benchmark of those formats. For compression examples, see [Part 2.3](04-context-management.md).

## 2.6.1 The Research: Context Files Often Hurt

Does a detailed context file improve agent performance? The study below tested that assumption.

**The ETH Zurich study (Gloaguen et al., AGENTBENCH, Feb 2026) tested this at scale: 138 tasks across 12 repositories, 4 different coding agents.** The results:

| Finding | Data |
|---------|------|
| LLM-generated context files hurt performance | Declined in 5/8 experimental settings |
| Average correctness change | **−2%** on AGENTBENCH |
| Cost increase with LLM-generated context | **20-23%** more tokens burned |
| GPT-5.2 reasoning overhead | **22%** more reasoning tokens with context file |

In these settings, generated context files increased cost without improving average correctness.

## 2.6.2 Human-Written Files: Marginal at Best

Human-written context files performed slightly better — but not consistently:

| Model | Effect of Human-Written Context File |
|-------|--------------------------------------|
| Average across models | ~4% improvement (inconsistent) |
| Claude Code | **Worse** with human-written context |
| File discovery rates | Identical with or without context files |

File discovery rates were unchanged: agents could find the relevant files without the context file.

## 2.6.3 Why More Context Hurts

Four mechanisms explain the findings:

**1. Redundancy.** A context file may repeat facts already available in `package.json`, imports, and the directory structure. The agent processes the same information twice.

**2. Attention.** Liu et al., "Lost in the Middle" (2023), describe weaker use of information in the middle of long context. Important rules can be harder to retain when buried in a 200-line `AGENTS.md`.

**3. Anchoring.** Agents can follow outdated guidance too closely. Mentioning a tool in `AGENTS.md` increased its use **1.6x**, even when another tool better suited the task.

**4. Signal-to-noise ratio.** Every token of context competes for the model's attention. Low-value context (routine facts like "this project uses TypeScript" when `tsconfig.json` exists) dilutes the high-value context (project-specific cautions like "don't refactor the auth module — it has a pending security audit").

## 2.6.4 The Counterpoint: Efficiency vs. Correctness

Lulla et al. (Jan 2026) found human-written `AGENTS.md` **reduced runtime 29%** and **output tokens 17%**. They measured efficiency, not correctness.

Keep those outcomes separate. Faster navigation is useful, but it does not establish that the answer is correct.

## 2.6.5 What to Actually Put in Context Files

Addy Osmani's filter (Google, 2026):

> **"Can the agent discover this on its own by reading your code? If yes, delete it."**

What survives this filter:

| Keep | Delete |
|------|--------|
| "Use `uv` instead of `pip`" | "This is a Python project" |
| "Run tests with `--no-cache`" | "Tests are in the `tests/` directory" |
| "Don't refactor the auth module" | "We use JWT for authentication" |
| "Deploy requires VPN connection" | "Main branch is protected" |
| "DB migrations must run in order" | "We use PostgreSQL" |

Keep project-specific pitfalls: things that look routine but cause mistakes. Remove facts the agent can discover from code.

## 2.6.6 The Ideal: Treat It Like a Bug Tracker

```text
Start with almost empty file.
Agent trips on something → add one line.
Root cause gets fixed → delete that line.
```

Add guidance when a recurring mistake needs it. Remove that guidance when the underlying problem is fixed.

## 2.6.7 This Repo's Approach: 6 Lines, ~50 Tokens

This project's `.github/copilot-instructions.md`:

```text
Terse like caveman. Technical substance exact. Only fluff die.
Drop: articles, filler (just/really/basically), pleasantries, hedging.
Fragments OK. Short synonyms. Code unchanged.
Pattern: [thing] [action] [reason]. [next step].
ACTIVE EVERY RESPONSE. No revert after many turns. No filler drift.
Code/commits/PRs: normal. Off: "stop caveman" / "normal mode".
```

**6 lines. ~50 tokens.** Loaded every interaction.

Compare this with a typical `/init` file of **200+ lines, ~1,500 tokens**: a difference of 1,450 tokens per load. Keep the extra guidance only if it helps the agent complete the task.

**The math over a session:**

| Context File Style | Tokens per Load | 50 Interactions | Agent (20 steps) |
|-------------------|:--------------:|:---------------:|:----------------:|
| `/init` generated (200 lines) | ~1,500 | 75,000 | 30,000 |
| Typical hand-written (50 lines) | ~400 | 20,000 | 8,000 |
| Caveman-compressed (6 lines) | ~50 | 2,500 | 1,000 |
| **Savings (caveman vs /init)** | **1,450** | **72,500** | **29,000** |

---

**Next:** [MCP & Tool Costs →](08-mcp-tool-costs.md)
