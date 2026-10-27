# Enterprise Governance for Copilot Cost Control

[← Back to Guide](index.md)

---

Control Copilot spend with documented organization and enterprise controls.

## What Actually Controls Spend

Start with:

1. budget ceilings
2. user-level budget control
3. model availability

Prompt compression still matters, but it is a usage-efficiency lever. It is not an admin billing control.

![Enterprise governance control plane: budgets, user-level budgets, and model access feed usage reports, cohorts, and review loops.](assets/diagrams/enterprise-governance-control-plane.svg)

For Business and Enterprise after the June 1, 2026 billing cutover, manage AI-credit usage rather than premium-request counts.

## 1. Set Budgets First

Use GitHub's [usage-based billing guidance for organizations and enterprises](https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-organizations-and-enterprises) and [metered product budget setup docs](https://docs.github.com/en/billing/how-tos/budgets/setting-up-budgets-to-control-spending-on-metered-products).

Key points from GitHub docs for Business and Enterprise after June 1:

- budgets can be set at organization, enterprise, or cost-center scope
- you can enable stop usage when budget is reached
- user-level budgets exist, and a **$0** user budget means no access to usage-based features

For budgets and cost-center mappings that are hard to manage manually, consider [`amgdy/copilot-finops-automation`](https://github.com/amgdy/copilot-finops-automation). A version-controlled YAML file defines spend policies and team mappings. GitHub Actions validates it, previews changes, applies budgets idempotently, syncs membership, and writes audit and current-state reports.

This makes changes reviewable in pull requests and supports scheduled reconciliation. Use a private repo or fork for live configuration. Keep enterprise slugs, teams, cost centers, user logins, budget numbers, reports, logs, and tokens out of public branches.

Budgets cap spend; they do not reduce tokens per prompt.

Practical default:

1. set a nonzero test budget for rollout
2. enable alerts early
3. enable stop usage once reporting looks sane
4. review monthly, not just after overruns

## 2. Use User-Level Budgets for Per-User Tightening

For Business and Enterprise after June 1, the clean per-user cap is the **user-level AI credit budget** documented in GitHub's [usage-based billing guidance](https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-organizations-and-enterprises). GitHub's docs explicitly say a user-level budget of **$0** means that user gets no access to usage-based features.

Review model choice, chat depth, and agent duration alongside each user's AI-credit budget.

GitHub documents budgets, cost centers, allowance management, and org/enterprise model-access policy. The cited docs do not describe an arbitrary per-user, per-model access matrix within one org for IDE chat. Use user-level budgets for per-user spending limits.

Practical pattern:

1. baseline group gets a low user-level AI-credit budget
2. power users get more only with clear job need
3. monthly review: downgrade users whose usage does not justify the extra cost

Build cohorts from actual spend, not guesswork:

1. download historical Copilot billing or usage CSVs from GitHub
2. upload the CSV to [Copilot Billing Preview](https://copilot-billing-preview.github.com/)
3. inspect the usage bands it surfaces, such as power users, high-usage users, baseline users, and low-usage users
4. map those cohorts to different budget defaults, review cadence, and escalation paths

Use these cohorts to match budgets to actual work. Agent-heavy users may need more headroom; inactive or low-value usage needs review before it consumes pooled credits.

Post-June 1 admin tips:

1. watch long chat and agent sessions because repeated turns now accumulate AI-credit cost directly
2. monitor user-level budgets closely because **$0** removes access to usage-based features
3. remember code completions and next edit suggestions are not billed in AI credits, so do not treat all Copilot activity as equal cost
4. review model choice regularly because chat-style interactions now consume AI credits based on model and tokens
5. keep terse repo instructions in IDE workflows because lower output volume still reduces AI-credit burn even when billing moves off request counts

## 3. Review Models Before You Enable Them

Use [Configuring access to AI models in Copilot](https://docs.github.com/en/copilot/using-github-copilot/ai-models/configuring-access-to-ai-models-in-copilot).

GitHub documents that organization owners and enterprise owners can enable or disable access to AI models for members. Treat that as the supported model-governance control.

That is not the same thing as finely tuned per-user model toggles inside one org. For different org-level model policies or separate billing boundaries, separate organizations can still be useful. Use that only when the boundary is worth the overhead.

Practical review checklist:

1. which workflows actually need premium models
2. which teams produce measurable value from them
3. which users can stay on Auto or included models
4. what usage report you will inspect after enablement
5. what rollback rule you will use if spend jumps without quality gain

Approve model access in stages. Compare usage and output quality before expanding access beyond the teams that need it.

## 4. Keep Custom Instructions in the Right Scope

GitHub documents repository instructions for IDEs and GitHub.com in [Adding repository custom instructions for GitHub Copilot](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions) and the IDE-specific companion page linked there.

GitHub documents organization instructions in [Adding organization custom instructions for GitHub Copilot](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-organization-instructions).

Important scope rule from GitHub docs:

- repository custom instructions are valid repo-level guidance
- organization custom instructions are currently supported for Copilot Chat on GitHub.com, Copilot code review on GitHub.com, and Copilot cloud agent on GitHub.com
- do **not** assume organization instructions apply in IDEs

For terse behavior in VS Code or JetBrains, put the guidance in repository instructions, not only in org instructions.

Practical split:

- org instructions: broad policy tone, review reminders, cross-repo defaults for GitHub.com surfaces
- repo instructions: concrete coding, build, and terseness rules that must apply in IDE workflows
- path-specific instructions: narrow local rules where extra context pays for itself

## 5. Separate Orgs Can Work, but Treat It as a Caveated Workaround

Separate organizations can support different model policies or billing boundaries when user groups already align with org structure. Weigh that separation against:

- more admin overhead
- license-assignment complexity
- awkward fit if users span many orgs
- SCIM and cost-center structure may drive the answer more than Copilot alone

Use separate orgs only when the policy or billing boundary justifies that overhead.

## 6. Measure the Right Thing

Use GitHub usage reports and AI-credit reporting from the billing docs above. Ask four questions:

1. which teams exceed baseline spend most often
2. which users drive the most AI-credit usage
3. which models correlate with better outcomes vs simple habit
4. which agent workflows create large spend without commensurate delivery value

Supplemental benchmarking can help when comparing model quality and price posture. [llm-stats.com](https://llm-stats.com/) is useful as an independent reference point. Treat it as supplemental benchmarking, **not** official GitHub guidance and **not** a substitute for your own Copilot usage reports.

## 7. June 1 Cutover Checklist

For the June 1, 2026 billing model:

1. move admin guidance from request counters to AI-credit budgets
2. build spend cohorts from historical CSVs before assigning user-level or pooled budgets
3. decide which users need tight user-level budgets and which teams can share a broader pooled budget
4. review model availability before frontier models become direct AI-credit spend
5. remind teams that code completions and next edit suggestions stay outside AI-credit billing
6. watch long chat and agent workflows first because they become the fastest spend amplifiers

## Recommended Enterprise Default

1. use Auto as the default model path
2. set budgets before broad rollout
3. segment users into spend cohorts from historical CSVs, then tune budget defaults by cohort
4. use user-level AI credit budgets when you need tighter per-user control
5. consider FinOps-as-code automation when manual cost-center and budget edits become risky
6. review premium models before enablement
7. keep repo instructions small so IDE workflows inherit the right defaults
8. use separate org segmentation only if cost-center boundaries already support it

---

**Next:** [Outcome per Token →](13-outcome-per-token.md)
