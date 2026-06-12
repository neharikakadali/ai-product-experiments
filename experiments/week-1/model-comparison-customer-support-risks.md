# Experiment: Model comparison — GenAI customer support risk assessment

**Date:** June 12, 2026
**Week:** 1, Day 4
**Prompt tested:** "What are 3 risks of building a GenAI customer support tool?"
**Models compared:** Claude · ChatGPT · Gemini
**Assessment dimensions:** Length/structure · Tone · Accuracy · Confidence signals

---

## What I tested

Ran the same prompt across three frontier models without role context or system prompt parity. Compared outputs across four dimensions to develop model selection intuition.

---

## Findings

### Length and structure
- Gemini: longest response, led with a framing sentence (TLDR), then broke each risk into sub-bullets covering definition, implication, and examples
- Claude: shortest, dove directly into risks without preamble
- ChatGPT: added mitigations unprompted — the question only asked for risks

ChatGPT's unprompted mitigations reflect a "helpful by default" behavior that goes beyond the literal ask. Whether that's good depends on the use case — for a first draft it's useful; for a structured audit it introduces noise.

### Tone and assumed audience
None of the three models asked clarifying questions, despite the prompt being underspecified (no role, no context, no goal). All three pattern-matched to "list of risks" and executed with a default audience assumption:

- ChatGPT assumed a product/CX owner
- Gemini assumed a business owner
- Claude assumed a builder/engineer

A PM-oriented model would have asked: "Are you a consultant, a PM building this, or an engineer anticipating risks?" before answering. Since it didn't, the right fix is to specify the role explicitly in the prompt.

**Key lesson:** If you want PM-level output, put PM-level context in. Models fill ambiguity with assumptions.

### Accuracy — what the third risk revealed
All three models agreed on hallucination and data privacy as risks 1 and 2. The third risk differed by model — and revealed their default stakeholder lens:

| Model | Third risk | Stakeholder lens |
|---|---|---|
| Claude | Training data drift | Engineering / systems |
| ChatGPT | Escalation failure / CX outcomes | Product / customer |
| Gemini | Unpredictable operational costs | Business / CFO |

None are wrong. They serve different audiences. Model choice should reflect who the output is for.

### Confidence signals — the system prompt problem
ChatGPT labeled risks as [Likely] or [Certain] — but this was due to a system prompt instructing it not to hallucinate and to signal confidence. Claude and Gemini had no equivalent system prompt.

This was not a clean model comparison. **System prompt parity is required for valid model benchmarking.**

Explicit confidence signals are useful as a prioritization input — they help distinguish which risks to address first. Worth building into any risk assessment system prompt regardless of model.

---

## Model selection recommendation (internal risk assessment)

| Use case | Recommendation |
|---|---|
| Pure risk identification (internal, not customer-facing) | Cheapest capable model — ChatGPT or Gemini |
| Risk identification + mitigation planning | Claude's engineering framing may be better suited — needs follow-up test |
| Any model, any use case | Always provide a system prompt and RAG context (past support data, product context) — this improves output quality more than model choice alone |

---

## Meta-lessons for PM practice

1. **Prompt parity is essential** — system prompts must be identical for valid model comparisons
2. **Underspecified prompts reveal model defaults, not model quality** — always specify role and goal
3. **Model selection is a cost-benefit decision** — don't over-engineer; match model to use case and cost constraints
4. **"Helpfulness" is defined differently by each model** — answering what was asked vs. anticipating what you need next is a product design choice, not a quality signal

---

## Open questions / follow-up experiments
- Run a follow-up prompt testing mitigation quality across all three models with identical system prompts — needed before making a definitive recommendation
- Test: does specifying "you are a PM advising a Series A startup" change the third risk across all three models?
- At what token volume does the cost difference between Claude and Gemini become decisive for a customer support use case?
