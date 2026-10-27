# 2.1 Prompt Compression

[← Back to Guide](index.md)

Remove words that do not change the request. Keep requirements, exceptions, technical terms, and enough context to act correctly.

Shortening a prompt reduces that prompt's input size. It does not establish a percentage reduction in total task cost. File context, tools, history, output, and retries also count.

## 2.1.1 Start with clear, concise prose

| Verbose request | Concise request |
|---|---|
| "Could you please review this pull request and let me know if there are any issues?" | "Review PR. Flag issues." |
| "Can you explain what this error message means and how I should fix it?" | "Explain this error and how to fix it." |

These are writing examples, not measured model runs. Token counts depend on the tokenizer; quality depends on whether the rewrite preserves what the task needs.

Do not delete uncertainty that changes meaning. "May be null" is not the same as "is null." Keep negation, conditions, numbers, units, and step order.

## 2.1.2 Optional shorthand

![Prompt compression ladder: shorter forms require checking that technical terms and constraints survive.](assets/diagrams/prompt-compression-ladder.svg)

| Style | Use when | Watch for |
|---|---|---|
| Concise prose | Default for prompts and documentation | Unnecessary framing |
| Fragments / Caveman-style shorthand | Shared context makes the meaning clear | Missing actors, conditions, or step order |
| Abbreviations and arrows | A bounded technical expression needs them | Ambiguity and unfamiliar shorthand |

There is no general savings or quality guarantee for these levels. Start with ordinary concise language, not maximum compression. For the separate Caveman **response-style skill**, see [output evidence and limitations](05-output-control.md#caveman-optional-output-style).

## 2.1.3 Structured Format Over Prose

Use bullets or key-value pairs when a request has distinct requirements.

**Prose:**

```text
I need you to create a REST API endpoint that accepts POST requests at /api/users.
It should validate that the request body contains a name field (string, required)
and an email field (string, required, must be valid email format). If validation
fails, return a 400 status with error details. On success, save to the database
and return 201 with the created user object.
```

**Structured:**

```text
POST /api/users
Validate:
- name: string, required
- email: string, required, valid format
400 on validation fail (include errors)
201 on success (return created user)
Save to DB
```

The structure makes validation, storage, and response requirements easier to inspect. Do not infer a benchmark percentage from this hand-written pair.

## 2.1.4 Abbreviations and Shorthand

Use familiar domain terms such as API, HTTP, DB, and PR. Define project-specific shorthand before relying on it. A shorter spelling does not necessarily use fewer tokens.

Keep code, commands, paths, identifiers, and quoted errors exact.

## 2.1.5 Code-Centric Prompting

A reference to existing code can be clearer than restating its behavior:

```text
Like getUserById but for emails. Return 404 if missing.
```

Use this only when the agent can inspect the referenced function. State any differences explicitly.

## 2.1.6 Quality Over Quantity — Scope, Don't Pile On

When the model gets something wrong, check whether the task is clear before adding global rules.

| Symptom | Better response |
|---|---|
| Misses an edge case | Include that case in the task and its test |
| Produces unwanted explanation | Specify the needed response format |
| Explores unrelated files | Name the target and acceptance criteria |
| Misses a path-specific convention | Use [scoped instructions](04-context-management.md#scoping-guidance) |

## 2.1.7 Declarative Guardrails

State the required result when order does not matter. Use ordered steps when it does.

| Procedure-heavy request | Result-focused request |
|---|---|
| "First read the file, then identify all the public functions, then for each one check whether it has a JSDoc comment, and if it doesn't, add one." | "All exported functions: JSDoc required." |
| "Make sure that whenever you write a SQL query you always parameterize the values to prevent injection attacks and never use string concatenation." | "SQL: parameterized queries only. No concatenation." |

## 2.1.8 Keep reusable rules in the right place

Keep task-specific requirements in the task. For global, path-specific, and on-demand guidance, use [Context Management](04-context-management.md#scoping-guidance). The [starter template](10-practical-setup.md#step-1-set-a-concise-response-default) lives in Practical Setup.

---

**Next:** [Language Comparison →](03-language-comparison.md)
