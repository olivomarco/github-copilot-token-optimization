# 2.4 Output Control — Tell the Model What NOT to Say

[← Back to Guide](index.md)

Output can cost more per token than input. The Anthropic examples in [Model Selection & Pricing](11-models-and-pricing.md) have a **5× output rate**; do not treat vendor API rates as Copilot billing rates. Asking for code without unwanted explanation is a low-effort way to cut output. The guide estimates **40–70% fewer output tokens** for code-generation tasks.

---

## 2.4.1 Request Code-Only Responses

A request for code can return 50 lines of code plus 200 tokens of explanation. If you only need the implementation, say so.

**Add to your prompt or `copilot-instructions.md`:**

```text
Code only, no explanation.
```

or in your prompt:

```text
Add input validation to processOrder(). Code only.
```

**Savings: 40-70% output tokens** for code generation tasks.

**Trade-off:** Keep explanations when learning or debugging. Use "code only" when you understand the task and need the implementation.

## 2.4.2 Constrain Response Format

Tell the model exactly what format to use:

| Instruction | Effect | Output Savings |
|-------------|--------|----------------|
| "Answer in one sentence" | Caps verbosity | ~60-80% |
| "3 bullet points max" | Hard limit on items | ~50-70% |
| "Reply as JSON" | Structured, no prose | ~30-60% |
| "Table format" | Compact for comparisons | ~40-60% |
| "Yes or no, then one line why" | Minimal response | ~70-90% |

## 2.4.3 System-Level Terse Output

Set terse output as a project default via `copilot-instructions.md`:

```text
Be concise. No explanations unless asked.
Code only for generation tasks.
Bullets over paragraphs.
```

The project default saves you from repeating "code only" in each prompt.

**Savings: 30-60% output tokens on every interaction.**

**When to override:** When you need explanations, just ask: "Explain why this approach is better than X." The model will expand when explicitly asked, even under terse instructions.

---

**Next:** [Workflow Optimization →](06-workflow-optimization.md)
