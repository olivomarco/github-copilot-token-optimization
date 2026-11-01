# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Developers using GitHub Copilot who want to reduce wasted tokens and rework. Team leads and enterprise admins are secondary readers.

## Product Purpose

A documentation-first resource for improving useful completed work per total cost. It includes a guide website and presentation decks, not an application UI.

Success means readers can identify waste, choose a relevant intervention, and evaluate its effect without sacrificing task quality.

## Positioning

The guide separates output length, loaded context, tool overhead, model costs, and rework. It evaluates token-saving claims against workload and billing evidence rather than treating shorter answers as proof of lower whole-task cost.

This is community guidance, not official GitHub or Microsoft policy.

## Operating Context

Readers start with Practical Setup, then follow links to the relevant topic. Why Tokens Matter explains the cost model.

The website uses MkDocs Material, configured in `mkdocs.yml`, with dependencies in `requirements-docs.txt`. The GitHub Pages workflow builds the guide and publishes the slide decks alongside it.

Presentations support two formats: an 18-slide practitioner briefing and a full 8-hour customer workshop.

## Capabilities and Constraints

Preserve existing chapter topics and both presentation formats. Topics cover requests and responses, loaded context, tools, workflows, rework, model selection and pricing, enterprise governance, and evidence.

Keep technical facts, terminology, and caveats accurate. Verify changing product behavior, pricing, and organization controls against official sources.

Do not invent savings claims or add percentages from different mechanisms. Measurements need a baseline, workload, model, and unit. Output-only reductions do not establish bill reductions.

Ordinary concise language is the guide's default recommendation. Caveman is optional; the guide has not run its own paid default/concise/Caveman benchmark.

## Brand Commitments

Name: Token Optimization Guide for GitHub Copilot.

Use clear, direct prose and simple practical guidance. Preserve necessary technical detail; avoid unnecessary deliverables and process. The saved project voice is Direct (`TONE-OF-VOICE.md`).

## Evidence on Hand

- `README.md` and `docs/index.md`: purpose, entry points, terminology, and reference links.
- `docs/`: thirteen topic chapters, practical setup, and evidence reviews.
- `docs/assets/diagrams/`: explanatory diagram assets.
- `slides/briefing.html`: practitioner briefing.
- `slides/index.html`: full customer workshop.
- `.github/workflows/deploy-pages.yml`: website and slide publishing workflow.

Existing source-linked guidance is evidence to assess, not permission to invent benchmarks or guarantee savings.

## Product Principles

- Lead with clear, practical guidance; keep detail that enables correct action.
- Judge optimization by useful completed work and total cost, not token counts alone.
- Choose interventions for identifiable waste rather than adding tools or rules by default.
- Keep evidence, limitations, and changing product terms explicit.
