# Part 3: Comparisons & Data

[← Back to Guide](index.md)

---

## 3.1 Head-to-Head: Same Prompt, Different Techniques

**Task:** "Add error handling to this function"

| Technique | Prompt | ~Input Tokens | Output Quality |
|-----------|--------|--------------|----------------|
| Verbose English | "Hey, could you please add comprehensive error handling to this function? I'd like it to handle all edge cases including null inputs, invalid types, and network errors. Please explain your changes." | ~40 | Good, but verbose output |
| Caveman lite | "Add error handling to this function. Cover null inputs, invalid types, network errors." | ~16 | Good |
| Caveman full | "Add error handling. Cover: null input, bad type, network error." | ~12 | Good |
| Caveman ultra | "Error handling: null/bad-type/net-err." | ~7 | Good (may need context) |
| Structured | `fn: add error handling\n- null input\n- invalid type\n- network error` | ~12 | Good |
| Code-centric | `# TODO: handle None, TypeError, ConnectionError` | ~8 | Good |

The estimated prompt sizes range from 7 to 40 tokens, a **5.7x difference**. The shortest examples need enough surrounding context to remain clear.

## 3.2 Language Comparison Tables

### Single Sentence Comparison

Source: Ivan Krivyakov's analysis using HuggingFace tokenizer playground (GPT-4 setting).

| Language | Sentence | Characters | UTF-8 Bytes | Tokens | Cost vs. English |
|----------|----------|-----------|-------------|--------|-----------------|
| 🇬🇧 English | I met a huge dog | 16 | 16 | **5** | 1.0x |
| 🇪🇸 Spanish | Conocí a un perro enorme | 24 | 25 | **8** | 1.6x |
| 🇵🇱 Polish | Spotkałem ogromnego psa | 23 | 24 | **8** | 1.6x |
| 🇮🇸 Icelandic | Ég hitti risastóran hund | 24 | 26 | **10** | 2.0x |
| 🇨🇳 Chinese | 我遇见了一只大狗 | 8 | 24 | **11** | 2.2x |
| 🇯🇵 Japanese | 大きな犬に出会った | 9 | 27 | **11** | 2.2x |
| 🇷🇺 Russian | Я встретил огромную собаку | 26 | 49 | **14** | 2.8x |
| 🇮🇱 Hebrew | פגשתי כלב ענק | 13 | 24 | **16** | 3.2x |

### Large-Scale Averages

Source: Capodieci/Castillo research across larger text samples.

| Language | Avg Token Cost vs. English | Characters per Token | Verdict |
|----------|---------------------------|---------------------|---------|
| English | 1.0x | 4.75 | ✅ Best for prompts |
| Spanish | ~1.3-1.6x | ~3.5 | ⚠️ 30-60% more expensive |
| German | ~1.4-1.6x | ~3.2 | ⚠️ 40-60% more expensive |
| Mandarin Chinese | ~1.76x | 1.33 | ❌ 76% more expensive |
| Japanese | ~2.12x | 1.41 | ❌ 112% more expensive |
| Korean | ~2.36x | ~1.2 | ❌ 136% more expensive |
| Russian | ~2.5-2.8x | ~2.0 | ❌ 150-180% more expensive |

## 3.3 Technique-by-Technique Matrix

Compare techniques by the part of the token budget they affect:

| # | Technique | Input Savings | Output Savings | Quality Impact | Effort | Best For |
|---|-----------|:------------:|:--------------:|:--------------:|:------:|----------|
| **Communication Style** |
| A1 | Caveman-speak (full) | 30-50% | 40-55%† | Minimal | Low | All Copilot interactions |
| A2 | Intensity levels (lite→ultra) | 15-70% | 15-55%† | Varies by level | Low | Tuning compression |
| A3 | Wenyan modes | ❌ Negative | ❌ Negative | Degrades | Low | Demo only |
| A4 | Structured patterns | 20-40% | 30-50% | Improves | Low | Technical prompts |
| **Prompt Engineering** |
| B1 | Precise prompts | 30-60% | 30-60% | Improves | Low | Every interaction |
| B2 | Ask for diffs, not rewrites | — | 50-90% | Neutral+ | Low | Code modifications |
| B3 | One task per prompt | 20-40% | 20-40% | Improves | Low | Complex requests |
| B4 | Constrain output format | — | 40-80% | Depends | Low | Data extraction |
| B5 | System instructions for terseness | — | 30-60% | Good | Medium | All interactions |
| B6 | Few-shot vs zero-shot | +20-50% | -30-60% wasted | Improves | Medium | Novel patterns only |
| B7 | Retune prompts to target model guide | Indirect | Indirect | Improves | Low | Model upgrades, app prompts, agent profiles |
| **Context Management** |
| C1 | Limit context (file selection) | 50-90% | — | Varies | Medium | Large codebases |
| C2 | Compressed instructions file | 40-60% of file | — | None | Low | Every repo |
| C3 | Progressive on-demand guidance | 60-90% of optional guidance | — | Positive | High | Teams with reusable prompt files |
| C4 | Start new conversations | 80%+ | — | Lose context | Low | Long sessions |
| C5 | Convert non-text files to Markdown first | ~33% on cited PDF example; higher for noisy HTML | — | Improves structure | Low | DOCX, PDF, PPTX, XLSX, images, audio, RAG ingestion |
| C6 | Precompute codebase graph with [Graphify](https://github.com/Graphify-Labs/graphify) | 6.8×-71.5× claimed for structural navigation tasks | — | Improves targeting | Medium | Large codebases; repeated cross-file agent navigation |
| **Output Control** |
| D1 | Code-only responses | — | 40-70% | Good | Low | Code generation |
| D2 | Structured output (JSON/tables) | — | 30-60% | Depends | Low | Data tasks |
| D3 | Limit response length | — | Variable | Risk truncation | Low | Quick answers |
| **Agent-Specific** |
| E1 | `copilot-setup-steps.yml` | 10-30% | — | Improves | Medium | Coding Agent |
| E2 | Precise issue descriptions | 20-50% | — | Improves | Low | Coding Agent |
| E3 | Custom agent profiles | 10-30% | — | Improves | Medium | Coding Agent |
| E4 | Plan files | 15-40% | — | Depends | Medium | Complex agent tasks |
| **Memory/State** |
| F1 | Compress memory files | 40-60% per load | — | None | Low | Persistent contexts |
| F2 | Terse commit messages | ~5-15 tokens | — | None | Low | Agent reads git |
| F3 | Terse review comments | — | 60-80% | None | Low | PR workflows |
| **Model Selection** |
| G1 | Lower-cost models for simple tasks | N/A | N/A | Varies | Low | Simple tasks |
| G2 | Auto model selection | N/A | N/A | Good | None | Default choice |
| G3 | Draft cheap, polish premium | N/A | N/A | Good | Low | Iteration-heavy work |
| G4 | Model mixing by task type | 38-79% of requests | — | None (SWE-bench data) | Low | All workflows |
| G5 | Reasoning effort / thinking effort | Vendor-reported savings | — | Vendor-recommended, not benchmarked | Low | Supported reasoning models in Copilot, CLI, and API |
| **Session Management** |
| H1 | Ask Mode for simple questions | 60-90% | — | Good | Low | Simple questions |
| **Context File Management** (covers `copilot-instructions.md`, `AGENTS.md`, `CLAUDE.md` — same always-on context) |
| I1 | Prune always-on context to landmines only | Variable (file size) | — | Improves | Low | All agent workflows |
| I2 | Delete LLM-generated context files | 20-23% total | — | Improves | Low | Projects with /init output |
| I3 | Bug-tracker approach to context | Variable | — | Improves | Low | Living projects |
| I4 | Consolidate duplicate context files (one file, not two) | Duplicate cost | — | Neutral | Low | Repos with both AGENTS.md + copilot-instructions.md |
| **MCP & Tool Management** |
| J1 | Audit MCP servers (disable unused) | 5K-190K/task | — | None | Low | Agent mode users |
| J2 | Per-workspace MCP config | Variable | — | None | Medium | Multi-project setups |
| J3 | Minimize tool calls (instructions) | 10-30% | — | Neutral | Low | Agent mode |
| J4 | Compress tool output with [RTK](https://github.com/rtk-ai/rtk) | 60-90% of shell cmd output | — | None | Low | Agent / Coding Agent — any AI tool |
| J5 | Compress tool output with [snip](https://github.com/edouard-claude/snip) | 60-90% of shell cmd output; command-specific examples report higher | — | None | Low | Copilot CLI / agent shells where hook path is validated |
| J6 | Use minimal-context skills before more tools | 70-95% on targeted search/query patterns (tool-reported) | — | Improves targeting | Medium | Agent workflows that repeatedly search/read too broadly |
| J7 | Evaluate broad compression wrappers (Redcon / Headroom) | Tool-reported; varies by command/context pack | — | Depends | Medium | Teams willing to validate external wrappers and licenses |
| **Agent Mode Configuration** |
| K1 | Precise prompts + acceptance criteria | 30-60% | — | Improves | Low | Agent tasks |
| K2 | Plan files for complex tasks | 15-40% | — | Improves | Medium | Multi-step agent tasks |
| K3 | Cap agent maxRequests | Variable | — | Risk truncation | Low | All agent tasks |
| K4 | Mode selection (Ask/Edit/Agent) | 60-90% | — | Good | Low | Every interaction |
| K5 | CodeAct for long Copilot CLI tool chains | 49-69% input tokens in plugin benchmarks | — | Depends | Medium | Copilot CLI exploration/audit sessions |

> †A1/A2 output savings require system-level terse output instructions (see B5). Writing terse prompts alone saves input tokens; output tokens are only reduced if the model is instructed to respond tersely.

> C5 source: Marc Bara's [Your .docx Is Wasting 33% of Your AI Budget](https://medium.com/@marc.bara.iniesta/your-docx-is-wasting-33-of-your-ai-budget-86a3d229d042). Use [Microsoft MarkItDown](https://github.com/microsoft/markitdown) when non-text files need to enter an AI workflow.

> C6 source: [Graphify benchmarks](https://github.com/Graphify-Labs/graphify/blob/main/BENCHMARKS.md) and project documentation. Treat the high-end 71.5× number as structural-navigation best case on large codebases, not a universal savings guarantee.

> J5-J7/K5 sources are external project-reported numbers, not independent guide benchmarks. Treat them as candidates to pilot, not universal bill-reduction guarantees. Do not stack multiple shell-output filters on the same command path without measuring for lost detail.

### The Big Winners

Start with these techniques, ranked by impact relative to effort:

1. **Caveman-speak** — 30-50% input token savings; combine with B5 for 40-55% output savings
2. **Precise prompts** — 30-60% savings, just a habit change
3. **Code-only / constrain output** — 40-80% output savings, one instruction
4. **Shrink always-on context** (`copilot-instructions.md` + `AGENTS.md`) — compress filler, prune to landmines only, delete LLM-generated boilerplate. Compounds on every interaction and agent step; 20-23% agent-task reduction plus better correctness
5. **Ask Mode for simple questions** — 60-90% savings by avoiding Agent overhead
6. **Audit MCP servers** — disable unused servers, save 5K-190K tokens per agent task
7. **Convert non-text files to Markdown first** — avoid the DOCX/PDF/HTML format tax before content enters chat, agents, or RAG
8. **Precompute codebase graphs with Graphify** — replace repeated orientation reads with targeted graph queries on large repos
9. **Retune prompts to target model guide** — not a per-request shrink; improves first-pass quality and avoids rework after model changes

## 3.4 Quality Impact Assessment

Compression becomes less useful when it creates ambiguity. The guide's quality assessment:

| Compression Level | Quality Impact | Evidence |
|-------------------|---------------|----------|
| Lite (drop filler) | None | Models trained on diverse text, understand clean prose |
| Full (drop articles, fragments) | Negligible | Models handle fragments well; technical terms preserved |
| Ultra (abbreviations, arrows) | Minor risk | Complex multi-step instructions may be misread |
| Wenyan (Classical Chinese) | Moderate risk | Models understand wenyan less reliably than English |
| Extreme (single words only) | Significant risk | Ambiguity increases, model may misinterpret |

If you repeatedly need to explain the request again, use a less compressed version.

**Model-specific notes:** All major models (GPT-4, Claude, Gemini) handle caveman-full well. Ultra works for experienced users who know the domain. Wenyan is unreliable for code generation tasks.

### Diminishing Returns

Cut filler before shortening meaningful instructions. Fragments and abbreviations can save more, but they also make the reader infer more.

```text
Savings vs. Quality Risk:

Quality  ████████████████████████████████████░░░░░░░░░
Risk     ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░███████████████
         0%       20%      40%      60%      80%
                      Token Savings →
               lite     full      ultra    extreme
```

The guide recommends full caveman for technical prompts: **30-50% input token savings**, with **40-55% output savings** when terse system instructions are also set. Use a clearer version if shorthand causes mistakes.

---

**Next:** [Practical Setup →](10-practical-setup.md)
