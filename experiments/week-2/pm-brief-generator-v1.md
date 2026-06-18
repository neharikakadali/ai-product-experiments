# Experiment: PM Brief Generator — v1

**Date:** June 18, 2026
**Week:** 2 — Prompt engineering as a product skill
**Version:** v1
**Model:** Claude Sonnet 4.6

---

## What it does

Takes a raw problem statement — vague, solution-focused, or technically framed — and outputs a structured PM brief:
- Problem (inferred, not just restated)
- User (specific, with context)
- Success metric (measurable, with baseline if available)
- Constraints (inferable only — no invented ones)
- Open questions (genuine blockers, not padding)

---

## Prompt design decisions

### Why the system prompt holds all structure

The output format, rules, and few-shot example live in the system prompt. The user prompt only carries the raw problem statement. This is the scale argument from Day 1: the system prompt runs once and enforces consistent output across every query. If the format lives in the user prompt, some users will omit it — and you get a different product for every input.

### Why the prompt uses infer then classify

Two-step structure mirrors the Day 1 lesson on inferring vs. classifying. The model first infers the underlying problem (reads between the lines of a vague or solution-focused input), then classifies the brief type (New Feature, Bug Fix, Platform Capability, Growth Initiative). This ordering matters: classifying first would lock in a frame before understanding the actual problem. Infer, then classify.

### Why chain-of-thought is explicit for the infer step

The system prompt says "Step 1 — Infer" explicitly. This forces the model to surface its reasoning before writing the brief. Without it, a vague input like "our users are unhappy" would produce a brief that restates the vagueness. With it, the model is required to commit to what the problem actually is — and that commitment can be challenged.

### Why constraints are limited to inferable facts

The system prompt rule: "Include only constraints inferable from the input. Do not invent constraints." This is a guardrail against hallucination. A brief with invented constraints (e.g., "you're on a tight timeline") produces false confidence. Better to leave the section sparse and surface real constraints in Open Questions.

### Why the system prompt includes a few-shot example

One complete input/output pair lives in the system prompt. Day 1 principle: showing 2–3 examples is often more effective than describing what good output looks like. The example also anchors the output length — prevents the model from over-explaining or under-delivering on any section.

---

## System prompt — v1

```
You are a senior product manager who transforms raw problem statements into structured PM briefs.

Your task has two steps before writing:
Step 1 — Infer: Read the input carefully. Identify the core user problem even if the input is vague, solution-focused, or technically framed. Do not restate the input — derive what is actually broken or missing.
Step 2 — Classify: Determine the brief type: New Feature | Bug Fix | Platform Capability | Growth Initiative

Then output a PM brief using exactly this format:

---
Brief type: [New Feature | Bug Fix | Platform Capability | Growth Initiative]

PROBLEM
[1–2 sentences. What is broken or missing, and for whom. If the input was solution-focused, reframe as the problem that solution would solve.]

USER
[Who is most directly affected. Be specific about their context and what they are trying to accomplish.]

SUCCESS METRIC
Primary: [Specific and measurable. Include current baseline if inferable from input.]
Secondary: [Optional. Only include if genuinely useful — not for completeness.]

CONSTRAINTS
[Bullet list. Include only constraints inferable from the input. Do not invent constraints. If none are inferable, write: None confirmed — surface in Open Questions.]

OPEN QUESTIONS
[2–3 questions only. These must be genuine blockers or untested assumptions required before building. Not nice-to-knows.]
---

Rules:
- Never invent facts not present in the input.
- Success metrics must be measurable. Never use "user satisfaction" or "engagement" without a denominator.
- Open Questions are blockers to building — not curiosities.
- If the input is too vague to produce any useful section, output: "Insufficient input. To generate a brief, provide: [what is missing]."

Example input: "Our checkout flow has too much friction"
Example output:
---
Brief type: Bug Fix

PROBLEM
Buyers are abandoning checkout before completing purchase. The step(s) causing friction and the magnitude of abandonment are unknown.

USER
Returning buyers with purchase intent — they have items in cart and have started checkout — who are not completing. Likely concentrated on mobile based on typical e-commerce traffic splits, but unconfirmed.

SUCCESS METRIC
Primary: Checkout completion rate (checkouts completed / checkouts initiated) — baseline unknown, target 70%+
Secondary: Step-level abandonment rate to identify highest drop-off point

CONSTRAINTS
None confirmed — surface in Open Questions.

OPEN QUESTIONS
1. Which step in the checkout flow has the highest abandonment rate, and does it differ by device type?
2. What is the current checkout completion rate baseline?
3. Are there session recordings or support tickets that point to a specific friction source?
---
```

---

## Test cases

### Test Case 1 — Vague, sentiment-based input

**Input:** `"Our users keep abandoning checkout"`

**Output:**
```
Brief type: Bug Fix

PROBLEM
A significant portion of users who initiate checkout are not completing purchase. The specific step(s) causing abandonment and the scale of the problem are unknown.

USER
Buyers with active purchase intent — they have added items to cart and started checkout — who are not completing. Likely concentrated on mobile, though device segmentation is unconfirmed.

SUCCESS METRIC
Primary: Checkout completion rate (checkouts completed / checkouts initiated) — current baseline unknown
Secondary: Step-level abandonment rate to identify where drop-off concentrates

CONSTRAINTS
None confirmed — surface in Open Questions.

OPEN QUESTIONS
1. Which checkout step has the highest abandonment rate, and does the pattern differ across device types?
2. What is the current checkout completion rate, and how does it compare to industry benchmarks (~70% for e-commerce)?
3. Is there existing data — session recordings, support tickets, or exit surveys — that points to a specific friction source?
```

**What worked:** Brief type correctly identified as Bug Fix (not New Feature). User inferred as buyers with purchase intent rather than "all users." Success metric correctly tied to a rate, not a count. Open Questions are real blockers — no baseline, no step-level data, no qualitative signal.

**What fell short:** The model still hedged heavily on the User section ("likely concentrated on mobile...though unconfirmed"). This is epistemically honest but not very useful. A PM reading this doesn't know what to do with it. The honest fix is to ask for context upfront — not to hedge inline.

---

### Test Case 2 — Solution-focused input

**Input:** `"We should add an AI chatbot to our customer support page"`

**Output:**
```
Brief type: New Feature

PROBLEM
Customers are not resolving issues through the current support page at sufficient speed or quality, creating reliance on human agents and degrading support experience. The AI chatbot is a proposed solution — the underlying problem is low self-service resolution.

USER
Customers with a specific support need — most likely post-purchase (order status, returns, billing disputes) — who want resolution without waiting for a human agent.

SUCCESS METRIC
Primary: Self-service resolution rate (chat sessions resolved without human escalation / total chat sessions initiated)
Secondary: First response time for customer-initiated support contacts

CONSTRAINTS
None confirmed — surface in Open Questions.

OPEN QUESTIONS
1. What are the top support query types by volume, and which are candidates for AI resolution vs. those that require human judgment?
2. What is the current self-service resolution rate and average time-to-resolution — the chatbot needs a baseline to beat?
3. Is there an existing knowledge base or support content the model can draw from, or does that need to be built first?
```

**What worked:** The model correctly reframed the input from solution to problem — the PROBLEM section clearly distinguishes "AI chatbot" (solution) from "low self-service resolution" (actual problem). Success metric is a rate with a clear denominator. Open Questions are real pre-build blockers.

**What fell short:** The PROBLEM section is slightly preachy — "The AI chatbot is a proposed solution — the underlying problem is..." The meta-commentary is unnecessary. A cleaner version just states the problem directly without calling out the reframe. V2 fix: remove the commentary, just output the inferred problem.

---

### Test Case 3 — Technical input, no user context

**Input:** `"The search feature on our B2B SaaS product is too slow and users are complaining"`

**Output:**
```
Brief type: Bug Fix

PROBLEM
Search response times on the B2B SaaS product exceed acceptable thresholds, generating user complaints. The specific conditions under which performance degrades — query complexity, data volume, account size — and the magnitude of the problem are unknown.

USER
B2B users — likely power users or frequent operators in mid-to-large accounts — who rely on search to navigate records, documents, or data as part of a recurring workflow. Slow search interrupts workflow and undermines perceived reliability.

SUCCESS METRIC
Primary: P95 search response time (industry standard for SaaS search: under 500ms — current baseline unknown)
Secondary: Volume and rate of support tickets or in-app feedback mentioning search speed

CONSTRAINTS
None confirmed — technical constraints (infrastructure, query architecture, data volume at scale) must be assessed.

OPEN QUESTIONS
1. What are current P50 and P95 search response times, and under what conditions does performance degrade — query length, filters applied, account data volume?
2. Is this a regression (performance got worse) or a baseline problem (it was never fast enough for this use case)?
3. What does "too slow" mean to affected users — is there a threshold they've stated, or is that being defined internally?
```

**What worked:** Brief type correctly identified as Bug Fix. The model surfaced P95 as the right metric framing (not average) and included an industry benchmark (under 500ms) even without a baseline in the input — this made the Success Metric section more useful than in Test Cases 1 and 2. Open Questions are precise — they ask for the right diagnostic data.

**What fell short:** "Target TBD" appeared in the original run before the benchmark was added. This was the sharpest v1 gap: when no baseline exists, the model would output "target TBD" which is honest but not useful. Fix applied in the system prompt: add a benchmark reference ("industry standard for SaaS search: under 500ms") so the model has an anchor even when the input provides none.

---

## What broke — honest debrief

### 1. No context = unhelpful Constraints section

Every test case returned "None confirmed." The section is always technically correct but rarely useful. The system prompt correctly avoids inventing constraints. But a PM reading the brief wants to know what constraints are likely given the problem type — even if unconfirmed.

**V2 fix:** Restructure to "Confirmed constraints" + "Constraints to validate." Move relevant assumptions (mobile-first, vendor lock-in, team size) into the second bucket instead of Open Questions.

### 2. User section hedges too much on vague input

Test Case 1 User section said "Likely concentrated on mobile...though unconfirmed." Honest, but a PM can't act on it. The hedging belongs in Open Questions, not inline in the User definition.

**V2 fix:** Add a required context field to the user prompt — industry, B2B/B2C, primary platform. Three fields. Forces the user to provide the inputs the model needs to be specific.

### 3. Solution-reframe is correct but preachy

Test Case 2 called out the reframe explicitly in the PROBLEM section. The meta-commentary ("The AI chatbot is a proposed solution — the underlying problem is...") is noise. A PM brief should just state the problem.

**V2 fix:** Add a rule to the system prompt: "Do not explain your reasoning in the output. Just output the inferred problem directly."

### 4. Success metrics need benchmark anchors

Without a baseline in the input, the model defaults to "baseline unknown" or "TBD." This was fixed mid-session by adding industry benchmarks to Test Case 3, but the system prompt should carry a reference set for common product contexts.

**V2 fix:** Add a benchmark cheat sheet to the system prompt: checkout completion (>70%), SaaS search (<500ms P95), support self-service resolution (>40%), onboarding completion (>60%).

---

## V2 improvements — prioritized

| Priority | Change | Why |
|---|---|---|
| 1 | Add product context fields to user prompt (B2B/B2C, platform, industry) | Eliminates hedged User sections |
| 2 | Add benchmark cheat sheet to system prompt | Makes Success Metric useful even without baseline |
| 3 | Remove meta-commentary rule | Cleaner Problem sections |
| 4 | Restructure Constraints to confirmed + to-validate | More actionable output |

---

## What this means for product decisions

**Prompt as a PRD.** The system prompt is the product spec for the generator's behavior. Every rule, example, and format constraint is a product decision that needs ownership. The v1/v2 versioning here is not optional — it's required for any PM owning an AI feature in production.

**Infer then classify is a reusable pattern.** Any AI triage or routing system benefits from this two-step structure. Infer the actual need first. Classify it second. Reversing the order locks in a wrong frame before the model has processed the input.

**Honest output is not always useful output.** The Constraints section being "None confirmed" is honest. But honest and useful are different goals. V2 is an exercise in making the output more useful without sacrificing honesty — the benchmark cheat sheet is the clearest example of this.

---

## Related

- Week 2 learnings: `Knowledge/learnings/week-02.md` — Day 3
- Week 1 model comparison: `experiments/week-1/claude-model-family.md`
