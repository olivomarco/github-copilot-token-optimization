# 2.2 Natural Language Choice — Checking the CJK Assumption

[← Back to Guide](index.md)

---

## 2.2.1 The Token Economics of Human Languages

Fewer characters do not necessarily mean fewer tokens. Compare "I met a huge dog" with its equivalents:

| Language | Sentence | Characters | Tokens | vs. English |
|----------|----------|-----------|--------|-------------|
| **English** | I met a huge dog | 16 | **5** | 1.0x (baseline) |
| **Spanish** | Conocí a un perro enorme | 24 | **8** | 1.6x |
| **Polish** | Spotkałem ogromnego psa | 23 | **8** | 1.6x |
| **Icelandic** | Ég hitti risastóran hund | 24 | **10** | 2.0x |
| **Chinese** | 我遇见了一只大狗 | 8 | **11** | 2.2x |
| **Japanese** | 大きな犬に出会った | 9 | **11** | 2.2x |
| **Russian** | Я встретил огромную собаку | 26 | **14** | 2.8x |
| **Hebrew** | פגשתי כלב ענק | 13 | **16** | 3.2x |

In this sample, Chinese uses 8 characters and English uses 16, but Chinese costs **11 tokens** against English's **5**.

## 2.2.2 Why This Happens

BPE compression depends on the tokenizer's training data. English-heavy data can favor English text:

1. **Common English words → 1 token.** The tokenizer has dedicated entries for them
2. **CJK text can use more tokens per character.** In the example above, 8 Chinese characters use 11 tokens; 16 English characters use 5. Character count alone misses that difference.
3. **Non-Latin scripts can also use more tokens.** The Russian and Hebrew examples cost 2.8x and 3.2x the English example.

## 2.2.3 Large-Scale Data

Reported larger-sample comparisons (Capodieci/Castillo research):

| Language | Avg Token Cost vs. English | Characters per Token |
|----------|---------------------------|---------------------|
| English | 1.0x | 4.75 |
| Spanish | ~1.3-1.6x | ~3.5 |
| German | ~1.4-1.6x | ~3.2 |
| Mandarin Chinese | ~1.76x | 1.33 |
| Japanese | ~2.12x | 1.41 |
| Korean | ~2.36x | ~1.2 |
| Russian | ~2.5-2.8x | ~2.0 |

## 2.2.4 What About Classical Chinese (Wenyan 文言文)?

Classical Chinese can express the same idea in fewer characters than modern Chinese. That does not guarantee fewer tokens: tokenization depends on byte sequences.

**Example — "Explain database connection pooling":**

| Mode | Text | ~Tokens |
|------|------|---------|
| Terse English | "Pool reuse open DB conn. Skip handshake → fast." | ~12 |
| Wenyan-full | "池reuse conn。skip handshake → fast。" | ~15 |
| Wenyan-ultra | "池reuse conn。skip→fast。" | ~12 |

In these illustrative counts, wenyan-ultra ties with terse English, while the longer version costs more. This does not establish a general token or comprehension advantage.

Use wenyan when the task calls for it, not on the assumption that fewer characters save tokens.

## 2.2.5 Practical Takeaways

Use the language that makes requirements clear. English uses fewer tokens in the reported comparisons, but these samples do not establish a universal advantage for every prompt or tokenizer.

Do not translate to CJK solely because it uses fewer characters. If language choice matters to cost, compare equivalent prompts with the target tokenizer. Keep code identifiers and required output language explicit.

### The Transliteration Effect

Transliteration reduces token counts in these two examples, by 21% and 44%. It is not a general 50% saving and may make text harder to read:

| Script | Sentence | Tokens | vs. Native |
|--------|----------|--------|-----------|
| Russian (Cyrillic) | Я встретил огромную собаку | 14 | baseline |
| Russian (Transliterated) | Ya vstretil ogromnuyu sobaku | 11 | 21% cheaper |
| Hebrew (Native) | פגשתי כלב ענק | 16 | baseline |
| Hebrew (Transliterated) | pgSti klv 3nq | 9 | 44% cheaper |

---

**Next:** [Context Management →](04-context-management.md)
