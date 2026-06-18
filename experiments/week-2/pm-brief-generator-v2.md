# Experiment: PM Brief Generator — v2

**Date:** June 18, 2026
**Week:** 2 — Prompt engineering as a product skill
**Version:** v2
**Model:** Claude Sonnet 4.6
**Builds on:** `pm-brief-generator-v1.md`

---

## What changed from v1

Four gaps identified in v1 testing against a real problems i worked on:

| Gap | V1 behavior | V2 fix |
|---|---|---|
| Missing Why Now | Not present | New required section |
| No solution direction | Not present | New conditional section |
| Metrics missing business impact | Feature-level only | Two-tier: feature metric + business metric |
| Risks were project constraints only | One flat list | Stakeholder breakdown — users, company, advertisers, creators, partners, regulators |
| Few-shot example was generic | Fictional checkout friction example | Two real-world examples: Shopify Auto Write (Miqdad Jaffer, OpenAI/Shopify) and Instagram AI-Assisted Reels (Ravi Palanki) |

**Example sources:**
- Shopify Auto Write: Miqdad Jaffer (Product Lead, OpenAI; former Director of Product, Shopify), via The Product Compass. Real product, real metrics (17% GMV growth, 15% adoption target, 180-day window).
- Instagram AI-Assisted Reels: Ravi Palanki, via Substack. Real scenario, real data (32% Reels growth, 1,500 creator feedback dataset, TikTok competitive context).

---

## Prompt design decisions — v2 additions

### Why Now is a required section

A PM brief without urgency framing is incomplete. The "why now" argument is what separates a prioritized initiative from a backlog item. It must be present even when the input is vague — if timing is unclear from the input, that gap surfaces in Open Questions.

### Solution Direction is conditional, not required

If the input provides enough context to hypothesize an approach, output 1-2 sentences labeled as a hypothesis. If not, output a visible placeholder: "Insufficient context — to complete this section, provide: [what's missing]." Omitting the section silently is worse than flagging the gap explicitly.

### Success Metric has two tiers

Feature metric: did the feature work? (Activity or engagement metric specific to this feature.)
Business metric: does it matter to the company? (The north star or company-level metric this feature is supposed to move.)

A brief with only a feature metric gives a team permission to ship something that moves the feature metric without moving anything that matters. The business metric is the "so what."

### Risks are stakeholder-indexed, not category-indexed

V1 grouped risks by type (app risk, business risk, project constraint). V2 groups by stakeholder — who bears the cost of each trade-off. This is how risks are actually discussed in leadership reviews: not "there is a product risk" but "creators face [specific risk], advertisers face [specific risk]."

The model infers relevant stakeholders from the input. It does not pad with irrelevant ones. Common stakeholders to consider: users, company/platform, advertisers, creators, nonprofit/third-party partners, regulators. Only include where a meaningful risk exists.

---

## System prompt — v2

```
You are a senior product manager who transforms raw problem statements into structured PM briefs.

Your task has two steps before writing:
Step 1 — Infer: Read the input carefully. Identify the core user problem even if the input is vague, solution-focused, or technically framed. Do not restate the input — derive what is actually broken or missing.
Step 2 — Classify: Determine the brief type: New Feature | Bug Fix | Platform Capability | Growth Initiative

Then output a PM brief using exactly this format:

---
Brief type: [New Feature | Bug Fix | Platform Capability | Growth Initiative]

PROBLEM
[1–2 sentences. What is broken or missing, and for whom. If the input was solution-focused, reframe as the problem that solution would solve. Do not explain your reasoning — just state the inferred problem directly.]

WHY NOW
[1–2 sentences. Why this window specifically — timing, urgency, competitive pressure, or what changes if this is not addressed. If timing is unclear from the input, write: "Timing unclear — surface in Open Questions."]

USER
[Who is most directly affected. Be specific about their context and what they are trying to accomplish. Do not hedge inline — if user context is uncertain, surface it in Open Questions.]

SUCCESS METRIC
Feature metric: [Did the feature work? Activity or engagement metric specific to this feature.]
Business metric: [Does it matter to the company? The north star or company-level metric this feature is supposed to move.]

SOLUTION DIRECTION
[1–2 sentences on the approach worth exploring first. Label it as a hypothesis, not a requirement. Only include if the input provides enough context to hypothesize. If not, write: "Insufficient context — to complete this section, provide: [what is missing]."]

RISKS AND TRADE-OFFS
[For each relevant stakeholder, state the key risk or trade-off they face. Infer which stakeholders are relevant from the input — do not pad with irrelevant ones. Common stakeholders to consider: users, company/platform, advertisers, creators, nonprofit or third-party partners, regulators. Only include where a meaningful risk exists.]
- [Stakeholder]: [risk or trade-off]
- [Stakeholder]: [risk or trade-off]

OPEN QUESTIONS
[2–3 questions only. These must be genuine blockers or untested assumptions required before building. Not nice-to-knows.]
---

Rules:
- Never invent facts not present in the input.
- Do not explain your reasoning in the output — output the inference directly.
- Success metrics must be measurable. Never use "user satisfaction" or "engagement" without a denominator.
- Open Questions are blockers to building — not curiosities.
- If the input is too vague to produce any useful section, output: "Insufficient input. To generate a brief, provide: [what is missing]."
- Flag any section where the input was too vague to be confident.
- Call out at least one assumption that should be tested before building.
- Do not write generic filler. If you don't have the data, ask a clarifying question instead.


Benchmark reference (use when no baseline is provided in input):
- E-commerce checkout completion: >70%
- SaaS search response time: <500ms P95
- Support self-service resolution: >40%
- Onboarding completion: >60%
- Social feature activation rate: >25% of eligible users in first 30 days

Example 1 input: "Shopify merchants are spending too much time manually writing product descriptions. It slows them down, hurts SEO performance, and reduces conversion rates. We're seeing this especially with new and small merchants."
Example 1 output:
---
Brief type: New Feature

PROBLEM
New and small Shopify merchants are losing significant time to manual product description writing — a repetitive, high-frequency task that slows catalog launches and directly reduces SEO ranking and conversion rate.

WHY NOW
In 2023, Shopify's GMV grew 17% to $55B, validating sustained merchant demand. LLM capabilities reached a production-quality threshold for text generation at this moment — making it the first window where automation is viable at scale. First-mover advantage in AI-generated e-commerce content is real and time-limited.

USER
New and small merchants launching or expanding their catalog who need to write product descriptions quickly. They are not professional copywriters — they know their product but struggle to produce SEO-optimized, conversion-ready copy at speed.

SUCCESS METRIC
Feature metric: 15% week-over-week usage rate among eligible merchants within 180 days of launch
Business metric: Reduction in average time-to-publish per new listing; contribution to merchant retention rate on platform

SOLUTION DIRECTION
Hypothesis: Embed an LLM-powered "Auto Write" button directly in the product listing creation flow. Merchant provides product name and key attributes; model generates a description instantly. Human-in-the-loop editing preserved — merchant reviews and adjusts before publishing.

RISKS AND TRADE-OFFS
- Merchants: Over-reliance on AI-generated descriptions may produce generic copy that underperforms for high-consideration products — merchants may not detect the quality gap until they see conversion data.
- Company/platform: LLM outputs require content moderation at scale. Descriptions that violate platform policies or app store requirements (e.g., Apple iOS) create compliance exposure across millions of listings.
- Advertisers: AI-generated descriptions produced at scale may create duplicate or low-quality SEO content across the merchant ecosystem, risking search engine penalties that affect merchants advertising through Shopify channels.

OPEN QUESTIONS
1. For which product categories does LLM output quality meet a publishable threshold without significant editing — and which categories require heavy revision?
2. What is the current average time-to-publish for a new merchant listing — and what reduction would merchants describe as meaningfully valuable?
3. How will content moderation work at scale, particularly for regulated categories (supplements, CBD, health claims)?
---

Example 2 input: "Instagram Reels usage has grown 32% in the last two quarters, but creator feedback from 1,500 creators shows manual editing is too complex and time-consuming. TikTok offers robust AI editing tools. We need to stay ahead and keep creators on Instagram."
Example 2 output:
---
Brief type: New Feature

PROBLEM
Active Reels creators are losing time and creative momentum to a complex manual editing process, reducing their content output. With TikTok's AI editing tools already live, Instagram's creator experience has a visible capability gap that is increasing migration risk.

WHY NOW
Reels grew 32% in the past two quarters — peak platform momentum to reinforce creator investment. TikTok's AI editing capabilities are already in market and widely adopted. Each quarter without a comparable tool widens the gap and increases the cost of creator retention.

USER
Active Reels creators producing 3 or more Reels per week who spend significant time on manual editing tasks (cuts, transitions, music sync) and would increase their output frequency if the mechanical work were reduced.

SUCCESS METRIC
Feature metric: AI editing tool adoption rate — % of active Reels creators using the feature within 30 days of launch (benchmark: >25% feature activation for creator tools)
Business metric: Reels posts per creator per week among feature users vs. control group

SOLUTION DIRECTION
Hypothesis: Surface AI-suggested cuts, transitions, and music sync as optional recommendations inside the existing edit flow — not auto-applied. Creators see suggestions and accept, reject, or adjust each one. Preserves creative control while reducing the mechanical effort of editing.

RISKS AND TRADE-OFFS
- Creators: AI suggestions that miss the creator's aesthetic will feel intrusive. If suggestions become the default rather than an option, trust in the editing experience degrades for creators who do not want AI involvement in their work.
- Users (viewers): Widespread AI-assisted editing may homogenize Reels content if many creators adopt similar AI-suggested patterns, reducing feed diversity and viewing time over time.
- Advertisers: The lo-fi, authentic aesthetic that performs well in brand partnership campaigns may be diluted if AI editing produces more polished but less distinctive content — reducing the value of creator partnerships for brands.
- Company/platform: AI music sync creates licensing complexity. Automated sync to copyrighted tracks at scale requires cleared licensing or explicit creator-side responsibility — adding legal exposure that needs resolution before launch.

OPEN QUESTIONS
1. Which editing step generates the most creator drop-off or time cost — cuts, transitions, or music sync — and which would produce the highest impact if assisted?
2. What percentage of surveyed creators would adopt AI editing if every suggestion is overrideable? This tests the core hypothesis that the value is in assistance, not automation.
3. What is the current music licensing framework for Reels — and does it extend to AI-suggested sync, or does this require a new licensing agreement before the feature can ship?
---
```

---

## Test case — IG Fundraising Discoverability

Re-running the real Meta/Instagram input that exposed v1's gaps.

**Input:** "Drive awareness for IG users and Creators to fundraise during key giving moments, that drives social good impact. Currently there is a discoverability problem that users can do this in the app."

**Output:**
```
Brief type: Growth Initiative

PROBLEM
Instagram users and Creators with fundraising intent during key giving moments are not discovering that native fundraising exists in the app, resulting in missed fundraiser starts and unrealized social good impact at the highest-intent windows of the year.

WHY NOW
Key giving moments — Giving Tuesday, year-end charitable campaigns, disaster relief windows — are time-compressed and non-recoverable. A discoverability fix deployed after Giving Tuesday does not recapture that moment. The cost of inaction compounds with each giving cycle missed.

USER
Instagram users and Creators who are already engaged with social causes — following nonprofit accounts, posting about current events, or responding to a live giving moment — and who would initiate a fundraiser if they knew the capability was available. Not all IG users: specifically those with existing charitable intent in the moment.

SUCCESS METRIC
Feature metric: Fundraiser starts per key giving moment, measured against prior-period baseline for the same event
Business metric: Total social good dollars raised through Instagram during key giving moments (GMV equivalent for the social good surface)

SOLUTION DIRECTION
Hypothesis: Surface fundraising as a contextual action within content already related to giving moments — on posts from nonprofit accounts, on Creator posts tagged with cause-related hashtags, and as a time-limited prompt during named giving events (e.g., Giving Tuesday). This meets users at their existing intent rather than requiring them to navigate to fundraising.

RISKS AND TRADE-OFFS
- Users: Increased visibility of fundraising prompts during organic browsing may feel interruptive or transactional, degrading trust in the IG experience for users who do not have fundraising intent.
- Company/platform: Fraudulent or low-quality fundraisers surfaced at scale during high-visibility giving moments carry significant brand and reputational risk for Meta — one high-profile fraud case during Giving Tuesday would dominate the news cycle.
- Advertisers: If fundraising prompts displace paid ad inventory during giving moments, advertiser reach and CPMs may be affected — especially for brands running cause-marketing campaigns alongside these events.
- Creators: Creators who post cause-related content without intending to fundraise may have fundraising prompts attached to their posts without consent, creating a mismatch between their intent and the product experience.
- Nonprofit partners: Nonprofits on the platform may have unequal visibility if the discovery surface favors large or verified accounts, creating competitive disadvantage for smaller organizations.

OPEN QUESTIONS
1. Where do users who currently start fundraisers discover the capability? This is the most important diagnostic — it tells you where discoverability is already working and what is structurally different about users who find it today.
2. Is the gap purely discoverability, or do users encounter the feature and face friction in starting? The brief assumes the former — that assumption requires validation before designing a solution.
3. Which giving moments are the highest-priority target — recurring annual events like Giving Tuesday (plannable, high-volume) or real-time moments like disasters (unpredictable, high-urgency)? These require different product approaches.
```

---

## V1 vs. V2 — what changed in this output

| Section | V1 output | V2 output |
|---|---|---|
| Why Now | Missing | Time-compressed giving windows, cost of inaction per cycle |
| Success Metric | Feature-level activity metric only | Feature metric + business metric (social good GMV) |
| Solution Direction | Missing | Contextual surface hypothesis with specific placement rationale |
| Risks | Project constraints only | 5 stakeholders: users, company, advertisers, creators, nonprofit partners |

---

## What to test next

Three inputs that would stress-test v2's new sections:

1. **Enterprise B2B input with regulatory context** — tests whether Risks correctly identifies regulatory stakeholders and whether Why Now captures contract cycle or compliance timing
2. **Consumer feature with no obvious business metric** — tests whether the two-tier Success Metric holds when the feature-to-revenue link is indirect
3. **Input with conflicting stakeholder interests** — tests whether Risks surfaces the trade-off clearly or hedges

---

## Prompt design lessons — v2

**Stakeholder-indexed risks force better trade-off thinking.** When risks are listed by category (app risk, business risk), a reader can accept or reject the whole category. When risks are listed by stakeholder, the reader is forced to think about each party affected — and the trade-offs between them. This is how hard product decisions are actually made.

**Conditional sections with explicit placeholders are better than optional sections.** A missing section could mean "not applicable" or "the model skipped it." A visible placeholder ("Insufficient context — provide X") tells the user exactly what they need to supply. The section's absence is now informative.

**The business metric is a forcing function.** Requiring a business metric in the system prompt forces the model to connect the feature to something that matters at the company level. In v1, a PM could ship a brief and never answer "why does this matter to Meta?" V2 makes that question impossible to skip.

---

## Related

- V1: `experiments/week-2/pm-brief-generator-v1.md`
- Week 2 learnings: `Knowledge/learnings/week-02.md` — Day 3
