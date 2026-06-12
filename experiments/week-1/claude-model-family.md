# Experiment: Claude model family — when to use which model

**Date:** June 10–11, 2026
**Week:** 1 — How LLMs actually work
**Source:** anthropic.com (Haiku, Sonnet, Opus, Fable pages)

---

## What I explored

Mapped Anthropic's full Claude model family — Haiku, Sonnet, Opus, Fable, and Mythos — to understand the cost/capability tradeoffs and build a decision framework for which model to use in a given product context.

---

## The spectrum

| Model | Price (input/output per 1M tokens) | Built for |
|---|---|---|
| Haiku 4.5 | $1 / $5 | High-volume, time-sensitive, well-defined tasks |
| Sonnet 4.6 | $3 / $15 | Most things — the smart default |
| Opus 4.8 | $5 / $25 | Complex work requiring reliability, minimal oversight |
| Fable 5 | $10 / $50 | Days-long, asynchronous, frontier-level tasks |
| Mythos | Restricted | Frontier research — vetted partners only |

---

## Key concepts learned

**Hybrid reasoning / extended thinking**
The model can switch between fast responses and deliberate step-by-step thinking before answering. More thinking = slower, pricier, more accurate. Use when accuracy beats speed. Expose as "careful mode vs. quick mode" in product design.

**Model autonomy**
How long a model can work independently without human check-ins. Distinct from extended thinking. Haiku = low autonomy (one task, waits). Fable = high autonomy (runs for hours, self-corrects).

**Vision**
Models can see and reason about images — charts, screenshots, PDFs with tables, wireframes. Enables document-heavy workflows. Fable can also check its own visual outputs against a design goal.

**1M context window**
~750,000 words held in memory at once. Entire codebases, long documents, extended conversation histories. A 250x expansion from early models.

**Multi-model architecture**
Production AI systems often use multiple models simultaneously. Haiku for fast, parallelized edges. Sonnet or Opus for the high-stakes core. This is the most cost-efficient and sophisticated pattern.

---

## Decision framework

**Default to Sonnet.** Drop to Haiku when tasks are high-volume, well-defined, and time-sensitive. Upgrade to Opus when removing humans from the review loop. Reach for Fable when the task runs for hours or days.

**The 30-second pitch:**
"Anthropic's Claude models are a spectrum. Haiku is fast and cheap — built for high-volume, well-defined tasks at scale. Sonnet is the smart default for most applications. Opus is for complex work where you need reliability without hand-holding. Fable is for the hardest, longest-running tasks — hours or days of autonomous work. In a real product, you often use multiple models: Haiku for the edges, Sonnet or Opus for the core."

---

## Applied example: AI research assistant for a hedge fund

The system has three jobs:
1. Monitor hundreds of data sources continuously → **Haiku** (high-volume, simple per-task)
2. Flag anomalies in real time → **Haiku** (fast, time-sensitive, well-defined)
3. Weekly 6-hour synthesis into investment thesis → **Fable** (sustained hours-long reasoning, high stakes)

Key insight: different parts of the same product use different models. Cheaper models for the edges; frontier models for the work that really counts.

---

## Applied example: Legal document review tool

**Actions:** read document, review against legal standards, flag language changes, answer questions on legal viability.

**Decision:**
- Accuracy matters more than speed (cost of a miss = lawsuit)
- Humans are reviewing every output initially → Sonnet is sufficient
- Upgrade trigger: move to Opus when removing lawyers from the review loop
- Add extended thinking selectively for highest-stakes clauses (indemnification, liability caps)

---

## Responsible AI connection

Mythos is Anthropic's most powerful model — and they chose not to release it publicly. Queries in cybersecurity and biology on Fable auto-route to Opus instead. This is responsible AI as a product and pricing decision, not just a policy document. Directly relevant to Week 5.

---

## What this means for my projects

- **AI Ads Creative Evaluator (Week 10):** Haiku for high-volume ad scoring at scale; Sonnet or Opus for deep creative analysis on high-value campaigns
- **AI Monetization Strategy Advisor (Week 16):** Sonnet as the default; Opus when the startup needs a production-grade output they'll act on without review
- **LinkedIn Post #2 angle:** "Which AI model should your product use?" — this experiment is the source material

---

## Open questions
- How does Sonnet's extended thinking compare to Opus in practice on the same task? (Experiment idea: run the same legal clause through both and compare)
- At what volume does Haiku's cost advantage become decisive vs. Sonnet?
