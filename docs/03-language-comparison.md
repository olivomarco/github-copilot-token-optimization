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

BPE tokenizers are trained on data that is majority English. English text gets the best compression ratios because:

1. **Common English words → 1 token.** The tokenizer has dedicated entries for them
2. **CJK characters → ~1-1.4 tokens each** (not 2-3 as commonly claimed). Each CJK character is 3 UTF-8 bytes, and those byte sequences are less frequent in training data. That's still ~5-7x more expensive *per character* than English (~0.2 tokens/char), which is why 8 Chinese characters cost 11 tokens while 16 English characters cost only 5
3. **Non-Latin alphabets (Cyrillic, Hebrew, Arabic)** fare even worse — 2.5-3.2x English

## 2.2.3 Large-Scale Data

Across larger text samples (Capodieci/Castillo research):

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
| English (full caveman) | "Pool reuse open DB conn. Skip handshake → fast." | ~12 |
| Wenyan-full | "池reuse conn。skip handshake → fast。" | ~15 |
| Wenyan-ultra | "池reuse conn。skip→fast。" | ~12 |

At best, wenyan-ultra ties with terse English. At worst, it costs more. And the model understands English better because its training data is English-dominated.

Use wenyan for creative or educational work, not to save tokens. This guide recommends terse English for token optimization.

## 2.2.5 Practical Takeaways

1. **Always use English** for prompts and instructions. It's 1.5-3x more efficient than any other language
2. **Don't Google Translate your prompts** into CJK languages hoping to save tokens — you'll spend more
3. **If you're bilingual**, you might write more concisely in your native language, but the token cost will be higher
4. **Code output stays English** regardless of prompt language — variable names, comments, docs will be in English
5. **Transliteration helps** for non-Latin scripts — writing Russian in Latin characters cuts tokens ~50%

### The Transliteration Effect

Converting non-Latin scripts to Latin characters helps:

| Script | Sentence | Tokens | vs. Native |
|--------|----------|--------|-----------|
| Russian (Cyrillic) | Я встретил огромную собаку | 14 | baseline |
| Russian (Transliterated) | Ya vstretil ogromnuyu sobaku | 11 | 21% cheaper |
| Hebrew (Native) | פגשתי כלב ענק | 16 | baseline |
| Hebrew (Transliterated) | pgSti klv 3nq | 9 | 44% cheaper |

---

**Next:** [Context Management →](04-context-management.md)
