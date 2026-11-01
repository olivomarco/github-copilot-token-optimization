# 2.5 Workflow-Specific Optimization

[← Back to Guide](index.md)

---

## 2.5.1 Commit Messages

Use Conventional Commits with a subject of ≤50 characters. Add a body when the reason for the change is not obvious.

**Verbose commit:**

```text
feat: Added a new feature to allow users to reset their passwords through
the settings page, which also sends a confirmation email
```

**Concise commit:**

```text
feat: add password reset via settings page

Sends confirmation email on reset.
```

Concise commits are easier to scan and use fewer tokens when an agent loads git history. Keep the reason when it matters.

## 2.5.2 PR Reviews

Name the issue, location, consequence, and fix. One line is enough when it preserves those details.

**Verbose review comment:**

```text
I noticed that on line 42, the user variable could potentially be null at this
point in the code, which would cause a NullPointerException when you try to
access the user's email property. You should add a null check before accessing
this property to handle this edge case properly.
```

**Concise review:**

```text
L42: 🔴 bug: user can be null here. Add null guard before .email access.
```

This hand-written pair shows a shorter comment, not a measured savings rate.

Severity labels can make priority easier to scan:

- 🔴 Bug / security issue — must fix
- 🟡 Suggestion — should fix
- 🔵 Nit — optional improvement
- ❓ Question — needs clarification

## 2.5.3 Ask Mode vs. Agent Mode

**Agent Mode** can make multiple model and tool calls to read, plan, edit, and verify. Each step adds token usage.

**Ask Mode** suits questions that can be answered from the supplied context without an implementation loop.

![Copilot mode decision tree: use Ask for questions, Edit for single-file changes, Agent for clear multi-file work, and clarify vague work before execution.](assets/diagrams/mode-decision-tree.svg)

| Task | Right Mode | Why |
|------|-----------|-----|
| "What does this function do?" | Ask | Single-shot answer. No tool use needed |
| "What's the TypeScript syntax for generics?" | Ask | Knowledge question |
| "Update validation in these known files" | Edit, where available | Bounded changes with explicit context |
| "Refactor this module to use dependency injection" | Agent | Multi-file changes, needs to read/write code |
| "Create a REST API with tests and docs" | Agent | Multi-step creation task |
| "Why is this test failing?" | Ask (usually) | Often needs just the error + context you provide |

Ask can avoid an unnecessary tool loop. The difference depends on what Agent would actually load and do; there is no fixed savings rate per question.

### Advanced Copilot CLI tactic: CodeAct

For **tool-heavy Copilot CLI sessions**, the optional external [`copilot-codeact-plugin`](https://github.com/jsturtevant/copilot-codeact-plugin) can combine tool operations. Instead of alternating model and tool calls across many turns, the agent writes a Python program and runs the sequence in one sandboxed execution.

Why this can save tokens:

- Fewer turns can mean less replay of prompts, history, and loaded tool definitions, including MCP catalogs.
- One consolidated result is often shorter than narrating every intermediate `grep` / `view` / `bash` hop.

When to use it:

- CLI-heavy exploration or audit tasks that would otherwise bounce through many small tool calls
- MCP-loaded sessions where schema replay is already expensive
- Repeatable analysis tasks such as TODO sweeps, function indexes, coverage checks, or cross-reference gathering

When not to use it:

- Simple one-shot Ask questions
- Normal IDE chat/edit workflows where the plugin does not apply
- Teams that do not want an external plugin in the workflow

The plugin README reports savings on its own benchmark prompts, including MCP-loaded cases. This guide has not benchmarked CodeAct independently; those results do not establish savings for every task.

### Complementary: RTK or snip for tool output compression

CodeAct reduces the *number* of tool calls. [**RTK (Rust Token Killer)**](https://github.com/rtk-ai/rtk) and [`snip`](https://github.com/edouard-claude/snip) reduce the *size* of each shell tool result. They address different sides of the same problem and can be used together.

These tools filter `git`, test runners, `grep`, `ls`, and other command results before they reach the agent. Reductions depend on the command and output. Unlike CodeAct, this is not Copilot CLI only; it can help where the shell hook is reliable. Treat Windows and preview hook paths as pilots. See [MCP & Tool Costs §2.7.7](08-mcp-tool-costs.md#277-compress-tool-output-at-the-source-rtk) and [§2.7.8](08-mcp-tool-costs.md#278-rtk-alternative-snip) for setup.

## 2.5.4 Default to Auto Model Selection

Use [Model Selection & Pricing](11-models-and-pricing.md#good-default-heuristics) for model and effort choices. Follow [Context Management](04-context-management.md#235-caching-store-and-reuse-context-within-prompts) for cache stability and handoffs.

## 2.5.5 Retune Prompts to the Target Model

Retuning a prompt may not shorten each request. Its purpose is to improve the first result and avoid follow-up turns, repeated clarification, and rework.

Model providers publish prompting guides that change with model versions. When an upgrade changes prompt behavior, consult the provider's migration or prompting guide. Adapt only the instructions affected.

**Workflow:**

```text
Open official prompting guide for target model.
Paste URL into Copilot chat.
Ask: "Adapt these target files to this guide. Keep behavior same. Reduce rework."
Target files: .github/copilot-instructions.md, .github/instructions/*.instructions.md, agents/*.md, app prompt files.
Review diff. Keep only measurable, model-relevant changes.
```

Official starting points:

| Provider | Model family | Prompting guide |
|---|---|---|
| Anthropic | Claude Sonnet / Opus / Haiku | [Prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview) and [Claude latest-model best practices](https://platform.claude.com/docs/en/docs/build-with-claude/prompt-engineering/claude-4-best-practices) |
| OpenAI | GPT-5.5 / GPT-5 | [GPT-5.5 prompting guide](https://developers.openai.com/api/docs/guides/prompt-guidance) and [GPT-5 prompting guide](https://cookbook.openai.com/examples/gpt-5/gpt-5_prompting_guide) |
| Google | Gemini | [Gemini prompt design strategies](https://ai.google.dev/gemini-api/docs/prompting-strategies) |

### Example: one base instruction, tuned three ways

Base instruction:

```markdown
You are a coding assistant. Help with implementation. Be concise. Ask questions if needed. Follow repo style. Run tests.
```

Model-specific rewrites:

| Target model | Tuned instruction | Why it fits |
|---|---|---|
| **Claude Sonnet** | `Role: senior repo engineer.\nUse XML-ish sections when helpful: <task>, <constraints>, <done>.\nBefore edits: inspect relevant files only. Preserve existing style.\nFor ambiguous requests: ask only if choice changes implementation.\nDone = patch applied + existing targeted tests pass or blocker named.` | Claude guides emphasize clear success criteria, examples/structure, explicit tool/use boundaries, and calibrated effort. XML-style delimiters often help separate task, context, and constraints. |
| **GPT-5.5** | `Outcome: correct repo change with minimal churn.\nSuccess: target behavior works, diff scoped, tests or exact blocker reported.\nChoose efficient path; do not over-spec process.\nStart tool-heavy work with one short progress update.\nAsk only for missing info that changes outcome or safety.` | GPT-5.5 guidance favors outcome-oriented prompts, concise personality/collaboration rules, efficient solution paths, visible preambles for multi-step work, and avoiding legacy over-specification. |
| **Gemini** | `Task: implement requested repo change.\nContext: use referenced files, nearby tests, and repo conventions.\nConstraints: concise output, scoped diff, no unrelated rewrites.\nFormat final: changed files + behavior impact + test result/blocker.\nIf input is incomplete, state one needed detail.` | Gemini guidance stresses clear task/input/constraints/response-format structure. Explicit format and context boundaries reduce interpretive drift. |

### Example user prompt for Copilot

```text
Target model: GPT-5.5.
Guide: https://developers.openai.com/api/docs/guides/prompt-guidance
Files: .github/copilot-instructions.md, agents/token-saver.agent.md
Adapt prompts to guide. Preserve behavior. Cut repeated clarification. Keep concise.
Show diff only.
```

Use this when:

- You upgrade or change default model.
- A prompt worked on one model but becomes verbose, lazy, over-eager, or too literal on another.
- Agent keeps making the same wrong assumption after a model change.
- You maintain app prompts, agent profiles, or reusable instruction files.

Avoid this when prompt behavior is already measured and stable. Changing instructions without a failure signal can add noise.

## 2.5.6 When NOT to Compress

Compression has limits. Some situations demand full clarity:

- **Security warnings** — "This will delete all user data" must not be abbreviated to "del usr data"
- **Irreversible operations** — Confirmation prompts must be unambiguous
- **Onboarding contexts** — New team members need the "why", not just the "what"
- **Complex multi-step instructions** — When fragment order could cause misreading
- **Regulatory/compliance text** — Legal requirements demand precision

Tell terse templates or agent profiles to use full, unambiguous wording for security warnings and irreversible-action confirmations.

## 2.5.7 Close the Loop with `/chronicle`

Repeated misunderstandings can waste more tokens than a long prompt. Copilot CLI's [`/chronicle`](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/chronicle) command analyzes local session history for confusion, repeated corrections, and other patterns worth fixing.

> **Scope:** `/chronicle` is a **Copilot CLI** feature, backed by local session history in `~/.copilot/session-state/`. It runs in Copilot CLI interactive sessions — and inside **JetBrains IDEs** via interactive Copilot CLI sessions. It is **not** available in **VS Code**; for VS Code usage analytics, see [AI Engineering Coach](#258-vs-code-usage-analytics-ai-engineering-coach) below.
>
> **Availability:** `/chronicle` is currently experimental. Enable it with `/experimental on` in an interactive Copilot CLI session, or pass `--experimental` on the command line.

The subcommands are `standup`, `tips`, `cost tips`, `search`, `improve`, and `reindex`. For cost and workflow review:

| Command | What it does | Token-saving payoff |
|---------|--------------|---------------------|
| **`/chronicle cost tips`** | Reviews prompt length, tool-call frequency, and continuation steps in recent sessions | Direct cost recommendations |
| **`/chronicle improve`** | Finds repeated corrections and **generates custom-instruction snippets** | May prevent recurring misunderstandings |
| **`/chronicle tips`** | Suggests features and workflow changes based on your usage | May identify better mode, model, or context choices |
| **`/chronicle standup`** | Summarizes branches, PRs, and status from session data | Saves manual summary work, not tokens directly |

### The `cost tips` workflow

Review where tokens went:

```text
/chronicle cost tips
```

Use its session-specific recommendations to choose which habits to change.

### The `improve` workflow

Use this when the same misunderstanding recurs:

```text
/chronicle improve
```

Copilot CLI proposes instructions from recurring confusion in recent sessions, such as assuming the wrong test framework. Accept project-specific corrections; skip boilerplate. See [Part 7: The AGENTS.md Problem](07-agents-md-problem.md) for what to keep.

Corrections add follow-up input, replayed history, and new output. Add reusable guidance only when it addresses a recurring failure.

### The `tips` workflow

Look for workflow suggestions:

```text
/chronicle tips
```

Keep suggestions that fit your workflow, such as using Ask Mode for explanations, scoping instructions with `applyTo`, or disabling unused MCP servers.

All session data lives locally in `~/.copilot/session-state/` and on your machine only. This is **Copilot CLI session data**, not a general Copilot Chat history store. Standard model interactions still apply when you run a `/chronicle` command (the data is sent to the model to generate the summary), but nothing is uploaded for storage.

## 2.5.8 VS Code Usage Analytics: AI Engineering Coach

For VS Code history, [**AI Engineering Coach**](https://github.com/microsoft/AI-Engineering-Coach) reads local AI session logs to identify recurring mistakes, token patterns, context issues, and reusable skills.

> **Privacy:** Local analysis is read-only and does not send session data off-machine. Optional AI features, such as rule compilation and context review, use the VS Code built-in Copilot model API when explicitly invoked; do not assume those features are offline.

Key capabilities relevant to token efficiency:

| Feature | What it surfaces |
|---------|-----------------|
| **Anti-Patterns** | 45 editable rules across prompt quality, session hygiene, code review, tool mastery, and context management — with severity ratings and concrete fix actions |
| **Context Health** | Agentic readiness checklist, workspace context map, and instruction-file audit |
| **Skill Finder** | Detects repeated prompt patterns in your history and matches them to reusable skills from the open-source catalog |
| **Output / Burndown** | AI-generated code volume by language and model; token budget progress with projections |

**Quick start:**

```bash
git clone https://github.com/microsoft/ai-engineering-coach.git
cd ai-engineering-coach
npm install && npm run package
code --install-extension ai-engineer-coach-*.vsix
```

Then `Cmd+Shift+P` → **AI Engineer Coach: Open Dashboard**.

## 2.5.9 Plan First, Then Execute (and Route the Phases)

For complex work, agree on acceptance criteria before implementation. The full [plan/execute/verify workflow](13-outcome-per-token.md#plan-first-then-execute-cheaply) explains when to separate sessions and use a cheaper execution model.

## 2.5.10 Layer Tooling on Top of the Copilot Harness

Copilot CLI and VS Code Copilot already optimize parts of the agent loop. Treat that as the baseline before adding external tools:

![MCP and tooling stack: scope MCP schemas, use CodeAct for long tool chains, filter command output with RTK or snip, and use Graphify for repeated orientation.](assets/diagrams/mcp-tooling-stack.svg)

- **Prompt/cache layer:** keep `{model, active MCP set, active agent/profile}` stable so cached prefixes stay reusable.
- **Tool-schema layer:** prefer built-in tools and scoped MCPs; Copilot can defer or route some tool definitions, but extra servers and extensions still add surface area.
- **Transport/session layer:** WebSocket reuse and automatic compaction help long agent runs, but compaction summarizes what the agent already saw.
- **Terminal-output layer:** built-in truncation is a safety net, not a semantic filter.

Add third-party tools only for the layer they actually improve:

| Layer | Tooling | What it reduces |
|-------|---------|-----------------|
| Workflow turns | CodeAct | Repeated replay from many small tool calls in Copilot CLI |
| Command output | RTK or snip | Verbose `git`, test, grep, build, and infra command output |
| Command choice | minimal-context-tools | Broad file reads and iterative searching by steering toward `rg`, `fd`, `jq`, `ast-grep` |
| Codebase orientation | Graphify | Repeated structural file reads across sessions |
| Visibility/audit | [Tokentop](https://github.com/tokentopapp/tokentop), Tokalator, token-optimizer | Tokentop gives live local session, model, token, cost, and burn-rate visibility; monitoring does not compress by itself |

Avoid duplicate filters. CodeAct and one output filter address different costs; RTK and snip on the same command path can double-truncate results and hide failures.

Use [Practical Setup](10-practical-setup.md#step-3-choose-mode-model-and-context) to apply these choices. Tool installation lives in [MCP & Tool Costs](08-mcp-tool-costs.md), not in this workflow summary.

---

**Next:** [The AGENTS.md Problem →](07-agents-md-problem.md)
