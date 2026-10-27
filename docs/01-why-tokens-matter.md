# Part 1: Why Tokens Matter

[← Back to Guide](index.md)

---

## 1.1 What Are Tokens?

Large language models read and write tokens. A token can represent a word, part of a word, or punctuation. Byte-Pair Encoding (BPE) builds these units from recurring byte sequences.

### How BPE works

1. Start with raw bytes (256 possible values)
2. Find the most frequent byte pairs in the training data
3. Merge those pairs into new tokens
4. Repeat until the vocabulary reaches ~100K tokens

The result: common English words become single tokens, while rare or long words get split into pieces.

### Examples

| Text | Tokens | Count |
|------|--------|-------|
| `hello` | `hello` | 1 |
| `unhappiness` | `un` + `happiness` | 2-3 |
| `authentication` | `authentication` | 2-3 |
| `I met a huge dog` | `I` `met` `a` `huge` `dog` | 5 |
| `Sure, I'd be happy to help you with that!` | (each word/punctuation) | ~10 |

The last example spends about 10 tokens on an opener without answering the question. Cutting that kind of filler is a simple way to reduce token use.

In the Claude 3 tokenizer, 8,311 of the 10,000 most common English words tokenize as a single token. Common words are cheap individually, but unnecessary words still add up.

## 1.2 Why You Should Care

Every token you send or receive has a cost. Here's how:

| Dimension | Impact |
|-----------|--------|
| **Cost** | API pricing is per-token, and GitHub Copilot usage limits are token-based. In the Anthropic examples, **output costs 5× as much per token as input**: $1/$5 for Haiku, $3/$15 for Sonnet, and $5/$25 for Opus per MTok. Copilot UBB also separates token buckets; see [Model Selection & Pricing](11-models-and-pricing.md) before applying vendor rates to Copilot spend |
| **Rate limits** | Fewer tokens per request = more requests per minute before hitting caps |
| **Context window** | Finite space shared between input (your prompt + context) and output (the response). Waste input tokens → less room for output |
| **Speed** | Fewer input tokens reduce time-to-first-token |
| **Billing** | As of June 1, 2026, GitHub Copilot bills usage as **usage-based billing (UBB)**. Each interaction is metered in three buckets — input tokens (prompt + context + history), output tokens (the response), and cached tokens (reused context, billed at ~10% of input rate). Cost = (input × input rate) + (cached × cache rate) + (output × output rate). Business and Enterprise seats include pooled AI credits ($30/seat for CB, $70/seat for CE) shared across the org. Code completion stays unlimited |

Most input tokens come from file context, conversation history, the system prompt, and MCP tool schemas. Your typed prompt is typically 5–100 tokens within a total input that can reach 5,000–50,000+ in agent mode. To reduce input, focus on instruction size, open tabs, and context scope. To reduce output, ask for only the response you need.

## 1.3 How GitHub Copilot Uses Tokens

Copilot's input includes:

![Token cost anatomy: input tokens include hidden context, output tokens are the visible answer, and stable cached tokens get cheaper.](assets/diagrams/token-cost-anatomy.svg)

- **System prompt:** Copilot's own instructions (you can't control this)
- **`copilot-instructions.md`:** Your project-level instructions — loaded on **every** interaction
- **File context:** Files you reference with `#file`, plus files Copilot auto-includes (open tabs, imports, nearby files)
- **Conversation history:** All previous messages in the current conversation
- **Your prompt:** What you actually type

Copilot also adds repo structure, file contents, and import graphs. A 20-word prompt can therefore consume 2000+ input tokens.

The GitHub Coding Agent reads files, makes decisions, and writes code across multiple steps. A single issue-to-PR session can consume tens of thousands of tokens, so reducing repeated context matters throughout the session.

## 1.4 Who This Guide Is For

- **GitHub Copilot Chat users** — the primary audience. Most techniques apply here
- **GitHub Copilot Inline/Edits users** — some techniques apply (limited prompt control)
- **GitHub Coding Agent users** — high-value audience. Token savings compound across long sessions
- **Anyone paying attention to efficiency** — even if you're not hitting limits, faster responses and cleaner interactions are worth it

---

**Next:** [Prompt Compression →](02-prompt-compression.md)
