# 2.3 Context Management — Control What Gets Sent

[← Back to Guide](index.md)

---

## 2.3.1 System Instruction Compression

Copilot loads `.github/copilot-instructions.md` on every interaction. Shortening it reduces repeated input.

> `.github/copilot-instructions.md` is Copilot's repo-level instruction file. `AGENTS.md` is a cross-tool convention that Copilot also reads. Both can coexist: shared rules in one, Copilot-specific rules in the other. Avoid repeating guidance because both files contribute to context when loaded. See [Part 2.6](07-agents-md-problem.md) for what to keep.

A 500-word instruction file at ~700 tokens adds that input to every interaction, before your question.

Remove duplicate and irrelevant rules before shortening individual sentences. Use the [starter template](10-practical-setup.md#step-1-set-a-concise-response-default) for response preferences and the [research filter](07-agents-md-problem.md#265-what-to-actually-put-in-context-files) for project guidance.

## 2.3.2 Memory File Compression

Projects also accumulate `CLAUDE.md`, project notes, coding guidelines, and `.cursorrules`. When these files enter context, they add input tokens. Remove duplicate guidance before shortening individual files.

Delete stale notes and repeated rules. Preserve commands, paths, negation, conditions, and step order. Keep occasional checklists outside the prompt. A smaller file helps only if that file would otherwise be loaded.

## 2.3.3 Strategic File Organization

Copilot automatically includes context from your workspace — open files, imported modules, nearby files. You can control what gets included.

**The official exclusion mechanism is Content Exclusion**, configured by repository / organization / enterprise admins via GitHub settings or the REST API. It's only available on **Copilot Business and Enterprise**, and it does **not** apply to Copilot CLI, the Copilot cloud agent, or Agent Mode in Copilot Chat in IDEs. Treat it as a privacy / policy control ("don't read this file"), not a per-developer token-saving knob. See [GitHub Docs — Excluding content from GitHub Copilot](https://docs.github.com/en/copilot/how-tos/configure-content-exclusion/exclude-content-from-copilot).

To reduce auto-included context:

- **Close files you're not working on.** Open tabs contribute to auto-included context. Close large files unrelated to the task.
- **Keep files focused and small.** If a 1,000-line file is open, Copilot may send large chunks of it as context. Break large files into focused modules.
- **Use `.gitignore` for build output, vendor dirs, and generated files.** Anything Git ignores is generally not pulled in as auto-context, and it keeps your repo clean as a side effect.
- **Use `applyTo:` scoped instruction files** (see § 2.3.4 below) so per-domain context only loads when relevant files are open — the most reliable way to shrink per-interaction context.
- **For Business/Enterprise customers:** ask your repo / org admin to configure **Content Exclusion** for genuinely sensitive paths (secrets, generated bundles, large data files). Primary purpose is policy, but it also keeps that content out of Chat / completions.

Check these sources of large context when open or referenced:

- Large README files
- Generated files (build output, bundled CSS, minified JS)
- Vendor directories
- Data files (CSVs, JSON fixtures)
- Archived documentation
- Rich document formats (`.docx`, `.pdf`, `.pptx`, `.xlsx`, HTML exports, scanned images, audio/video transcripts)

Every file that enters context costs tokens. Be intentional about what's open and what's referenced with `#file`.

### Normalize non-text inputs to Markdown first

Convert Word files, PDFs, PowerPoint decks, spreadsheets, images, audio, and exported HTML to clean Markdown before sending their content to the model.

![Format tax pipeline: rich formats create layout noise, MarkItDown normalizes them to clean Markdown, and rich delivery formats are generated only at the end.](assets/diagrams/format-tax-pipeline.svg)

Marc Bara calls this the **format tax** in [Your .docx Is Wasting 33% of Your AI Budget](https://medium.com/@marc.bara.iniesta/your-docx-is-wasting-33-of-your-ai-budget-86a3d229d042). Rich formats carry font data, XML, page positions, embedded objects, and markup that the model rarely needs. In the article's example, a 10-page PDF report used roughly 12,400 tokens; clean Markdown used about 8,350, a 33% reduction with the same information. HTML exports can add even more layout markup around the content.

The rule: use Markdown as the **working format** for AI interaction, and treat Word/PDF/PowerPoint as delivery formats. Draft, review, summarize, chunk, and retrieve from Markdown. Generate `.docx` or `.pdf` at the end only when a client, regulator, or internal process needs that artifact.

[Microsoft MarkItDown](https://github.com/microsoft/markitdown) is a Python converter for LLM and text-analysis pipelines. It preserves headings, lists, tables, links, and extracted metadata without reproducing the visual layout. Converters include PDF, Word, PowerPoint, Excel, images with EXIF/OCR support, audio with transcription support, HTML, CSV/JSON/XML, ZIP contents, YouTube URLs, EPUBs, and more.

Fast path:

```bash
pip install 'markitdown[all]'
markitdown report.docx -o report.md
markitdown deck.pptx -o deck.md
markitdown source.pdf > source.md
```

Use narrower extras when you control the workflow and want fewer dependencies:

```bash
pip install 'markitdown[pdf,docx,pptx,xlsx]'
```

Security note: MarkItDown reads files, streams, and URLs with the privileges of the current process. For untrusted inputs, validate paths and URLs first, and prefer the narrowest conversion API that fits the workflow.

<span id="scoping-guidance"></span>

## 2.3.4 Scope Context Intentionally — Conditional Over Always-On

Keep path-specific rules out of always-on context. A React component task does not need database migration guidelines. Load those rules conditionally.

![Context loading boundaries: keep the always-on core tiny, move path-specific guidance to scoped instructions, and load skills on demand.](assets/diagrams/context-loading-boundaries.svg)

### Use `applyTo:` paths in custom instructions

Custom instruction files in `.github/instructions/*.instructions.md` accept an `applyTo` frontmatter field that scopes the file to matching paths. Copilot only loads it when the conversation involves files matching the glob.

```markdown
---
applyTo: "src/api/**/*.ts"
---
API conventions:
- Routes in src/api/routes/. Handlers thin, logic in services/.
- Validate with zod. Errors via Result<T,E>, never throw.
- All endpoints return { data, error } envelope.
```

Without `applyTo`, that's ~30 tokens loaded on every Copilot call across the whole repo. With it, the cost is paid only when you're actually editing API code.

Split a large `copilot-instructions.md` into a small always-on core and scoped files such as `api.instructions.md`, `ui.instructions.md`, `tests.instructions.md`, and `migrations.instructions.md`. Give each its own `applyTo` glob so only relevant guidance loads.

### Leverage on-demand guidance and MCP tool discovery

Workflow-specific guidance should follow the same rule as MCP tools: keep it off the always-on prompt and bring it in only when the task needs it. A PR-review checklist, release template, or debugging playbook does not belong in `copilot-instructions.md` if you only use it occasionally.

Treat small prompt files, slash-command snippets, or saved checklists as the home for *workflow-specific* guidance. They stay out of every-day context until you invoke them.

### The rule

- **Always-on:** the few rules that genuinely apply to every interaction, such as project-wide conventions.
- **Conditional (`applyTo`):** anything path-specific (per-language, per-module, per-layer).
- **On-demand note / MCP:** anything workflow-specific that's invoked occasionally.

The reduction depends on which files would otherwise load. Keep the rules each task needs; compare the actual loaded context before and after scoping.

> **Measure loaded context, not file size on disk.** Skills can contribute discovery metadata before their full instructions load. See [MCP & Tool Costs §2.7](08-mcp-tool-costs.md) for the loaded/deferred distinction.

## 2.3.5 Caching — Store and Reuse Context Within Prompts

Copilot can cache stable context, including the system prompt, instruction files, and recently loaded files, rather than processing it at the full input rate on each turn.

In long sessions, cached input can make a substantial difference. Discounts can reach ~90%, depending on the provider, model, Copilot surface, and billing rules.

Three ways to preserve reusable context:

![Cache stability: stable threads reuse the same model, MCP set, and agent profile; cache-busted threads switch controls mid-session and should use a fresh handoff instead.](assets/diagrams/cache-stability.svg)

**1. Stable instructions at the top, volatile work at the bottom.** Cached context only works if the prefix of your conversation is stable. Don't reshuffle your `copilot-instructions.md` or rotate which files are open between every prompt — keep the stable layer stable, and let only the most recent message change.

**2. Reuse named context via slash commands and saved snippets.** When you frequently ask about the same domain, define it once and reference it. For example: keep a short customers-schema note or slash-command snippet, load it once for the session, then keep follow-up prompts anchored to that shared summary instead of re-pasting the schema every time.

**3. Keep the request prefix stable.** The prefix contains the system prompt, tool/MCP definitions, and instruction files (the `System/Tools` baseline; see [MCP & Tool Costs §2.7](08-mcp-tool-costs.md)). Changes can invalidate cached context:

| Mid-session change | Effect on cache & context |
|--------------------|---------------------------|
| **Switch model or reasoning effort** | Caches are typically per-model, and changing model configuration can discard the cached prefix. Carried history may be processed again under the new configuration. See [model and effort selection](11-models-and-pricing.md#good-default-heuristics). |
| **Switch custom agent** | Swaps the system prompt, tool set, and instructions — the prefix changes, so the cache is invalidated and the prior accumulated context becomes pollution under the new agent. |
| **Toggle MCP servers, tools, or loaded skills** | Changes the harness and its tool/instruction definitions in the prefix — cache invalidated, plus you add or remove always-loaded tokens on every later turn. |
| **Churn large context** | Attaching/detaching big files or pasting large blobs reshuffles context and undercuts prefix stability. Choose your files and context up front. |

Cached prefixes can reduce both processing time and input cost.

```text
Cache-stable long thread

Turn 1     Turn 2     Turn 3     Turn 4
|          |          |          |
v          v          v          v
[same model + same reasoning effort + same skills/MCP set + same agent/profile]
[stable prompt prefix reused from cache          ]  -> lower effective input cost
[only newest user request changes               ]

Cache-busted long thread

Turn 1     Turn 2        Turn 3
|          |             |
v          v             v
[model A + MCP set A + agent A]
[cached prefix builds         ]
                  |
                  v
          switch model / reasoning effort / skills / MCP / agent
                  |
                  v
[large prefix invalidated or repriced] -> cache discount lost; start fresh instead
```

### Safe handoff pattern when a switch is required

Choose model, effort, agent, and tools before long work. When a different setup is needed, start fresh with relevant context rather than carrying the full thread. Billing behavior varies by surface and plan; this is not guaranteed repricing math.

1. Summarize current thread in 5-10 bullets (decisions, constraints, open tasks).
2. Start a new chat with the new model/agent/MCP setup.
3. Paste only the summary + required files, not the entire old transcript.

This keeps old, irrelevant context out of the new session.

## 2.3.6 Start Fresh Conversations

Conversation history accumulates. After 20+ messages, you might have 50K+ tokens of history sent with every new message.

**When to start fresh:**

- Topic change — new question, new file, new concern
- After getting the answer you needed
- When responses start getting slow (context window filling up)
- When you notice quality dropping (model confused by old context)

**How to preserve continuity:** Summarize key decisions in your new prompt. "Continuing from auth refactor — we chose JWT over sessions. Now implement refresh tokens."

## 2.3.7 Persistent Graphs — Replace Per-Session File Reads

If repeated structural file reads dominate context, consider a persistent codebase map. [Graphify setup, outputs, and limitations](08-mcp-tool-costs.md#2711-persistent-codebase-maps-graphify) live with the other tools.

---

**Next:** [Output Control →](05-output-control.md)
