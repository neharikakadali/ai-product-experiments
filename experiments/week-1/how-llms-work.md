# Experiment: How LLMs actually work

**Date:** Week 1, Day 1
**Resources:** 3Blue1Brown — "But what is a GPT?" (25 min) · Andrej Karpathy — "Intro to LLMs" (1 hr)
**Quiz score:** 3/5

---

## Core concepts

### Tokens
The basic unit LLMs think in — roughly 3/4 of a word. "Unbelievable" might be 3 tokens. "AI" is 1. This matters for product design because:
- APIs charge by token, not word
- The context window is measured in tokens, not pages
- Long documents get expensive fast — you need to think about what you actually pass in

### Context window
How many tokens the model can see at once. A hard product constraint. Everything outside the context window doesn't exist to the model — it can't reference it, reason about it, or remember it.

Product implication: if your feature needs the model to "remember" something from earlier in a long session, you have to explicitly include it. The model has no implicit memory.

### Hallucinations
LLMs don't "know" facts. They predict the next most likely token based on patterns learned during training. When the training data doesn't contain a clear answer, the model generates a confident-sounding prediction anyway.

Fix: RAG (Retrieval-Augmented Generation) — feed the model real, current documents at query time. Don't just ask it to "be more accurate." That doesn't work.

### Temperature
Controls randomness in token selection. Low temperature = picks the most likely next token consistently (factual, predictable). High temperature = picks from a wider distribution (creative, varied, less reliable).

Match temperature to the task:
- Customer support bot → low temperature (consistent, accurate)
- Brainstorming tool → higher temperature (novel, varied ideas)
- Legal document review → low temperature (no creative surprises)

### System prompts
Instructions that run silently before every user message. They control: persona, constraints, output format, guardrails, tone. No retraining required — just edit the prompt.

Mental model: **a system prompt is a PRD baked into the model.** It's the most powerful product lever available to a PM building on top of an LLM. Everything the model does flows through it.

---

## The most important framework: RAG vs. fine-tuning

Most teams get this wrong. They reach for fine-tuning when they actually need RAG.

| Situation | Solution |
|---|---|
| Facts that change over time (pricing, policies, docs) | RAG |
| Behavior, style, or reasoning patterns you want the model to adopt | Fine-tuning |
| Stale knowledge / outdated answers | RAG |
| Model doesn't know your domain's jargon or format | Fine-tuning |

**Rule of thumb:** If the problem is "the model doesn't have the right information," use RAG. If the problem is "the model doesn't behave the right way," consider fine-tuning.

Most startups think they need fine-tuning. They usually need RAG. RAG is cheaper, faster to update, and solves the problem correctly for 80% of cases.

---

## Key mistake to avoid
Don't reach for fine-tuning when the problem is stale or missing facts. It's expensive, slow to iterate, and still won't give the model access to information that didn't exist when you trained it.

---

## PM implications
- **Pricing:** token-based APIs require you to think about cost per query. Passing in a 100-page document every call is a different cost structure than a 2-sentence query.
- **Product constraints:** context window limits define what features are even possible. A feature that needs to "remember everything" from a 3-hour session requires explicit context management design.
- **Hallucination mitigation:** RAG is not just a technical choice — it's a product decision about what sources of truth the model is allowed to use. That's a PM decision.
- **Temperature as UX:** the same model can feel very different to users depending on temperature. Treat it as a product tuning knob, not a technical default.
