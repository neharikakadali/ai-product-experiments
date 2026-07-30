# Experiment: PM Brief Generator — v3

**Date:** June 22, 2026
**Week:** 2 — Prompt engineering as a product skill (continued)
**Version:** v3
**Model:** Claude Sonnet 4.6
**Builds on:** `pm-brief-generator-v2.md`
**Substack anchor:** Post #2 covered v1 → v2 iteration. v3 adds leadership-grade brief structure.

---

## What changed from v2

| Gap | V2 behavior | V3 fix |
|---|---|---|
| Why Now lacked market proof | Timing narrative only | Anchor with 1 to 2 market trends or data points; cite source when using reference data |
| User section buried JTBD | Persona description only | Explicit Job to be done line (When / I want / so I can) |
| Success metrics flat | Feature + business only | North Star, feature, business, and MVP tiers with explicit ladder to North Star |
| Solution direction too thin | Hypothesis only | Hypothesis + PR/FAQ style product North Star + MVP scope + internationalization phases |
| Multi-market inputs | Treated as one segment | Phased rollout by market when input spans US, India, Singapore or equivalent |
| Business metrics assumed monetization | Defaulted to paying users | Do not assume revenue unless input mentions pricing |

---

## Prompt design decisions — v3

### Why Now requires market proof

Timing narrative alone is not enough for leadership reviews. Anchor Why Now with 1 to 2 relevant market trends or data points. Use data from the input first. When the input implies a market category (hiring, consumer AI, fintech, etc.), draw from the market context reference in the system prompt. Always name the source. Do not invent statistics.

### User section requires explicit JTBD

After the persona description, add a single line: `Job to be done: When [situation], I want [motivation], so I can [expected outcome].`

### Success metrics have four tiers

1. **North Star:** The one metric that best captures long term product success for this user problem.
2. **Feature metrics:** Did the product mechanics work? Activity or quality metrics tied to core loops.
3. **Business metrics:** Does it matter at company or venture level? Revenue, retention, cost, or strategic outcome.
4. **MVP metrics:** What to measure in the first 4 to 8 weeks. For each MVP metric, state in one clause how it ladders to the North Star.

Do not assume monetization in business metrics unless the input mentions pricing, revenue, or paid users.

### Solution direction follows PR/FAQ working backwards

Structure as four subsections:
- **Hypothesis:** Smallest bet on what will work.
- **Product North Star (PR/FAQ):** One to two sentences describing the customer outcome at success, written as if announcing the product to the world. What changed for the user when this product exists?
- **MVP scope:** The smallest shippable version to test the hypothesis in weeks, not months. Name axes, markets, and surfaces included and excluded.
- **Internationalization phases:** When the input spans multiple markets, define Phase 1 (launch market), Phase 2, Phase 3 with rationale for sequencing (demand, rubric availability, willingness to pay, language, regulatory).

---

## System prompt — v3

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
[2–4 sentences. Why this window specifically. Include 1–2 market trends or data points that strengthen urgency. Use data from the input first; otherwise use the market context reference below and cite the source. Name competitive pressure, timing windows, or cost of delay. If timing is truly unclear, write: "Timing unclear — surface in Open Questions."]

USER
[Who is most directly affected. Be specific about context, market, and what they are trying to accomplish. Use seniority, years of experience, or level only when stated in the input — do not infer ranges (e.g., "4 to 12 years"). If seniority or experience level is unclear, describe the user without inventing tenure and surface the gap in Open Questions. Do not hedge inline — if other user context is uncertain, surface it in Open Questions.]
Job to be done: When [situation], I want [motivation], so I can [expected outcome].

SUCCESS METRICS
North Star: [The single metric that best captures long term success for this user problem.]
Feature metrics:
- [Metric 1 — did the core product loop work?]
- [Metric 2 if needed]
Business metrics:
- [Metric 1 — company or venture level outcome]
- [Metric 2 if needed]
MVP metrics (first 4–8 weeks):
- [Metric]: [How this ladders to North Star in one clause]
- [Metric]: [How this ladders to North Star in one clause]

SOLUTION DIRECTION
Hypothesis: [Smallest bet on what will work. Label as hypothesis, not requirement.]
Product North Star (PR/FAQ): [1–2 sentences. The customer outcome at success — what is true about the user's world when this product works? Write as if announcing to the world.]
MVP scope: [Smallest shippable version to test the hypothesis quickly. Name what is in and explicitly out.]
Internationalization phases: [When input spans multiple markets: Phase 1, Phase 2, Phase 3 with market, scope, and sequencing rationale. If single market only, write: "Single market launch — no i18n phasing required."]

RISKS AND TRADE-OFFS
[For each relevant stakeholder, state the key risk or trade-off they face. Infer which stakeholders are relevant from the input — do not pad with irrelevant ones. When input spans multiple markets, include market calibration risk if rubrics or user expectations differ by region.]
- [Stakeholder]: [risk or trade-off]
- [Stakeholder]: [risk or trade-off]

OPEN QUESTIONS
[2–3 questions only. These must be genuine blockers or untested assumptions required before building. Not nice-to-knows.]
---

Rules:
- Never invent facts not present in the input or market context reference.
- Do not infer years of experience, seniority level, or tenure ranges unless explicitly stated in the input. Surface missing seniority context in Open Questions instead.
- Do not explain your reasoning in the output — output the inference directly.
- Success metrics must be measurable. Never use "user satisfaction" or "engagement" without a denominator.
- Do not assume paid users or revenue unless the input mentions monetization.
- Open Questions are blockers to building — not curiosities.
- If the input is too vague to produce any useful section, output: "Insufficient input. To generate a brief, provide: [what is missing]."
- Flag any section where the input was too vague to be confident.
- Call out at least one assumption that should be tested before building.
- Do not write generic filler. If you don't have the data, ask a clarifying question instead.
- Never use hyphen, "-" in the output.
- Be concise and drive clarity. Prioritize brevity in Problem and Why Now while still including data points.
- Use data and cite sources where data is present in the input or market context reference.

Benchmark reference (use when no baseline is provided in input):
- E-commerce checkout completion: >70%
- SaaS search response time: <500ms P95
- Support self-service resolution: >40%
- Onboarding completion: >60%
- Social feature activation rate: >25% of eligible users in first 30 days
- Mock interview session completion: >50% of started sessions completed in first 7 days

Market context reference (use when input relates to PM hiring, interview prep, or career tools — cite source):
- Lenny's Newsletter / TrueUp, early 2026: 7,300+ open PM roles at tech companies globally; 75% above early 2023 low; trending up ~20% since start of 2026
- James Gunaca PM Jobs Report, Dec 2025: Leadership PM roles +31% YoY globally; Senior PM +11% YoY; market 19% higher YoY vs 2024
- Productify, 2025 India PM market: PM hiring +42% Jan–Aug 2025; Senior PM roles +87% YoY vs Associate PM +16%; demand concentrated at senior levels
- Productify, 2025 AI PM migration: 12,000+ professionals moved into AI PM titles Jan 2024–Oct 2025; ~2x the prior year; new interview axis gap widening
- a16z State of Consumer AI 2025: fewer than 10% of ChatGPT weekly users visit another model provider; niche job-specific tools win by depth, not breadth

Example 1 input: "Shopify merchants are spending too much time manually writing product descriptions. It slows them down, hurts SEO performance, and reduces conversion rates. We're seeing this especially with new and small merchants."
Example 1 output:
---
Brief type: New Feature

PROBLEM
New and small Shopify merchants are losing significant time to manual product description writing — a repetitive, high-frequency task that slows catalog launches and directly reduces SEO ranking and conversion rate.

WHY NOW
In 2023, Shopify's GMV grew 17% to $55B (Shopify earnings), validating sustained merchant demand. LLM text generation reached production quality at this moment — the first window where automation is viable at scale. Merchant acquisition costs are rising industry wide; time to first listing directly affects activation and retention for new sellers.

USER
New and small merchants launching or expanding their catalog who need to write product descriptions quickly. They are not professional copywriters — they know their product but struggle to produce SEO optimized, conversion ready copy at speed.
Job to be done: When I add a new product to my catalog, I want a publishable description generated instantly, so I can launch faster without sacrificing SEO or conversion quality.

SUCCESS METRICS
North Star: Merchant GMV per new listing within 30 days of publish
Feature metrics:
- 15% week over week usage among eligible merchants within 180 days of launch
- Edit rate before publish below 40% (signal that first draft is usable)
Business metrics:
- Reduction in average time to publish per new listing
- Contribution to new merchant 90 day retention on platform
MVP metrics (first 4–8 weeks):
- Auto Write adoption in listing flow among 100 merchant beta: ladders to North Star by validating whether faster publish correlates with earlier first sale
- Median time from product create to publish vs. control: ladders to North Star as leading indicator of GMV velocity

SOLUTION DIRECTION
Hypothesis: Embed an LLM powered Auto Write button in the listing creation flow. Merchant provides product name and key attributes; model generates a description instantly. Human in the loop editing preserved.
Product North Star (PR/FAQ): Every new Shopify merchant publishes their first SEO ready product description in under 2 minutes — without hiring a copywriter or leaving the listing flow.
MVP scope: English only, new merchant cohort, top 3 product categories by volume. Out of scope: bulk catalog migration, non English locales, regulated categories (supplements, CBD).
Internationalization phases: Phase 1 English speaking markets (US, UK, Canada). Phase 2 Western EU locales. Phase 3 APAC — pending content moderation and regulated category review per market.

RISKS AND TRADE-OFFS
- Merchants: Over reliance on AI generated descriptions may produce generic copy that underperforms for high consideration products.
- Company/platform: LLM outputs require content moderation at scale; policy violating descriptions create compliance exposure across millions of listings.
- Advertisers: AI generated descriptions at scale may create duplicate or low quality SEO content, risking search penalties for merchants using Shopify ad channels.

OPEN QUESTIONS
1. For which product categories does LLM output quality meet a publishable threshold without significant editing?
2. What is current average time to publish for a new merchant listing — and what reduction is meaningfully valuable?
3. How will content moderation work at scale for regulated categories?
---

Example 2 input: "Instagram Reels usage has grown 32% in the last two quarters, but creator feedback from 1,500 creators shows manual editing is too complex and time-consuming. TikTok offers robust AI editing tools. We need to stay ahead and keep creators on Instagram."
Example 2 output:
---
Brief type: New Feature

PROBLEM
Active Reels creators are losing time and creative momentum to a complex manual editing process, reducing their content output. TikTok's AI editing tools are already live, creating a visible capability gap and migration risk.

WHY NOW
Reels grew 32% in the past two quarters (internal data cited in input). TikTok's AI editing capabilities are widely adopted in market. Creator platform competition is winner take most on habit — each quarter without comparable tooling increases switching risk for high output creators who drive disproportionate engagement.

USER
Active Reels creators producing 3+ Reels per week who spend significant time on manual editing (cuts, transitions, music sync) and would increase output if mechanical work were reduced.
Job to be done: When I'm editing a Reel, I want AI to handle repetitive cut and sync work, so I can publish more content without losing my creative control.

SUCCESS METRICS
North Star: Reels posts per active creator per week (platform engagement density)
Feature metrics:
- AI editing tool adoption: >25% of active Reels creators within 30 days (benchmark: creator tool activation)
- Suggestion acceptance rate above 30% (signal that suggestions are useful, not noise)
Business metrics:
- Reels posts per creator per week among feature users vs. control
- Creator retention at 90 days among adopters vs. non adopters
MVP metrics (first 4–8 weeks):
- Session completion rate (start edit → publish): ladders to North Star by confirming the tool reduces drop off in the edit flow
- Time in edit flow median vs. control: ladders to North Star as leading indicator of increased posting frequency

SOLUTION DIRECTION
Hypothesis: Surface AI suggested cuts, transitions, and music sync as optional recommendations inside the existing edit flow — not auto applied.
Product North Star (PR/FAQ): Reels creators publish one extra Reel per week because AI removed the mechanical editing work — while every creative decision stayed theirs.
MVP scope: Single axis — cut suggestions only. US creators, 3+ Reels/week cohort. Out of scope: music sync (licensing), transitions, auto apply.
Internationalization phases: Phase 1 US English. Phase 2 Brazil and India (high Reels growth markets). Phase 3 EU — pending music licensing framework per territory.

RISKS AND TRADE-OFFS
- Creators: AI suggestions that miss aesthetic intent feel intrusive; default on behavior degrades trust.
- Users (viewers): Homogenized AI assisted editing may reduce feed diversity over time.
- Company/platform: AI music sync creates licensing complexity at scale.

OPEN QUESTIONS
1. Which editing step generates the most creator drop off — cuts, transitions, or music sync?
2. What percentage of creators would adopt if every suggestion is overrideable?
3. Does current music licensing extend to AI suggested sync?
---
```

---

## Test case #2 — AI PM Interview Coach (Used, June 22, 2026)

**Input:** "PM candidates preparing for senior PM interviews at top tech companies in the US, India, and Singapore — Meta, Google, Amazon, Microsoft, Grab, and similar — fail despite strong general PM skills because generic prep resources treat every interview the same. They don't practice across the full axis set: product sense, behavioral, analytical thinking, system design, AI system design, and vibe coding during interviews. There is no tool that generates realistic questions per axis, evaluates answers against how these companies actually hire, and gives specific feedback plus a follow-up."

**Model:** Claude Sonnet 4.6 · temperature 0

---

### Run A — Manual v3 calibration (from v2 prompt run, enhanced to v3 spec)

**Score:** 8.5/10 on v2 run · manually enhanced to v3 structure

```
Brief type: New Feature

PROBLEM
Senior PM candidates preparing for top tech company interviews (Meta, Google, Amazon, Microsoft, Grab, and equivalents across the US, India, and Singapore) fail despite strong general PM skills because existing prep tools are generic — they do not cover the full six axis interview loop, do not evaluate answers against company specific hiring bars, and do not deliver structured follow up feedback that closes skill gaps.

WHY NOW
PM hiring has rebounded sharply: 7,300+ open PM roles at tech companies globally, 75% above the early 2023 low and up ~20% since start of 2026 (Lenny's Newsletter / TrueUp, early 2026). Competition at senior levels is intensifying — Leadership PM roles +31% YoY and Senior PM +11% YoY globally (James Gunaca, Dec 2025). In India, Senior PM demand grew 87% YoY vs. 16% for junior roles (Productify, 2025), signaling a higher bar for experienced PMs. Simultaneously, 12,000+ professionals moved into AI PM titles between Jan 2024 and Oct 2025, roughly doubling the prior year (Productify) — adding AI system design and vibe coding as axes most prep tools ignore. Generic ChatGPT prep is good enough for breadth; fewer than 10% of ChatGPT weekly users visit another AI provider (a16z State of Consumer AI 2025), which means niche depth wins. The window to own the senior PM interview prep niche is open now, before a generalist adds sufficient depth.

USER
Senior PM candidates (5 to 10 years experience) targeting L5 to L7 roles at Meta, Google, Amazon, Microsoft, Grab, or equivalents across the US, India, and Singapore. They are preparing across multiple interview axes simultaneously, have limited time for unstructured practice, and need signal on whether their answers meet the actual hiring bar — not just whether they are "good answers."
Job to be done: When I'm in an active interview cycle at a top tech company, I want realistic axis specific practice with rubric based evaluation and follow up questions, so I can enter each loop knowing my answers meet that company's hiring bar.

SUCCESS METRICS
North Star: Offer rate among active prep users at target companies within 90 days of first session (measured via post cycle survey until sample size supports tracking)
Feature metrics:
- % of registered users who complete a full mock session across all six axes within 14 days (benchmark: >60% onboarding completion)
- Rubric score improvement on re attempt of same axis within 7 days (signal that feedback closes gaps)
Business metrics:
- 30 day return rate among users in active prep cycles (proxy for habit formation during interview windows)
- Word of mouth referral rate (organic growth in a trust driven category)
MVP metrics (first 4–8 weeks):
- Session completion rate on product sense + behavioral axes: ladders to North Star by confirming the core eval loop delivers enough value to finish
- % of users who return for a second session within 7 days: ladders to North Star as leading indicator of prep habit during active cycles
- Rubric score delta on second attempt same axis: ladders to North Star by validating that feedback improves answer quality before offer stage

SOLUTION DIRECTION
Hypothesis: Build a company and axis specific interview simulator that generates realistic questions, evaluates answers against rubrics derived from known hiring frameworks, and returns structured feedback with a mandatory follow up question per axis — replicating how real loops pressure test answers.
Product North Star (PR/FAQ): Senior PM candidates preparing for Meta, Google, Amazon, and Grab loops treat this tool as the only prep layer they trust over ChatGPT — because it evaluates their answers against how those companies actually hire across all six axes, not generic PM frameworks.
MVP scope: Two axes only (product sense + behavioral). Two companies (Meta + Amazon). US market only. Web app (Streamlit). Out of scope for MVP: analytical, system design, AI system design, vibe coding; India and Singapore; company selection beyond two; payment.
Internationalization phases:
- Phase 1 (US, weeks 1–8): Meta + Amazon rubrics, product sense + behavioral axes. Rationale: rubric sources most accessible; largest paying market; validates core eval loop.
- Phase 2 (India, weeks 9–16): Add Google India + Amazon India; expand to analytical + system design axes. Rationale: Senior PM demand +87% YoY (Productify); axis weighting differs from US loops; lower ARPU requires free tier validation before monetization.
- Phase 3 (Singapore, weeks 17–24): Add Grab + Google APAC; add AI system design + vibe coding axes. Rationale: smaller market but high comp roles; vibe coding axis most differentiated; rubrics require local interviewer validation.

RISKS AND TRADE-OFFS
- Users (candidates): If company specific rubrics are inferred rather than validated by actual interviewers, the tool may give false confidence by optimizing for a proxy of the real bar — the highest stakes risk.
- Users (India/Singapore candidates): Rubrics calibrated to US big tech norms may mislead candidates prepping for loops where axis weighting, seniority framing, or business metric emphasis differs.
- Company/platform: Rubric accuracy degrades as hiring practices evolve. Without a mechanism to update rubrics post launch, the tool risks becoming stale in a word of mouth driven market.
- Regulators/legal: Positioning the tool as evaluating against specific company hiring standards without authorization may create trademark or misrepresentation exposure depending on marketing claims.

OPEN QUESTIONS
1. How are company specific hiring rubrics being sourced and validated — are current or former interviewers from target companies involved, or is this inferred from public data?
2. Which target companies explicitly assess vibe coding in PM loops today, and what does passing look like?
3. What is willingness to pay by market (US vs. India vs. Singapore), and does it affect Phase 2/3 sequencing or pricing model?
```

**Run A notes:** v2 prompt run lacked cited market data, explicit JTBD, metric tiers, PR/FAQ North Star, MVP scope, and i18n phasing. All addressed manually in Run A.

---

### Run B — v3 Workbench run (validated, June 22, 2026)

**Score:** 9/10 · v3 system prompt · full structure generated without manual editing

```
Brief type: New Feature

PROBLEM
Senior PM candidates preparing for top-tier tech interviews in the US, India, and Singapore fail not from lack of effort but from practicing against generic rubrics that ignore company-specific hiring bars and the full interview axis set — product sense, behavioral, analytical thinking, system design, AI system design, and vibe coding — leaving critical gaps undetected until the actual interview.

WHY NOW
PM hiring is accelerating across all three target markets: Senior PM roles grew +11% YoY globally and +87% YoY in India (James Gunaca PM Jobs Report, Dec 2025; Productify, 2025 India PM market), concentrating demand at the senior level where axis-specific calibration matters most. AI PM migration added 12,000+ professionals into new PM titles in under two years (Productify, 2025), creating a widening gap on AI system design and vibe coding axes that no existing prep tool covers. Niche job-specific tools win by depth over breadth in this category (a16z State of Consumer AI, 2025) — the window to own senior PM prep is open and narrowing as incumbents scale.

USER
Mid-level PMs with 3 to 7 years of experience actively targeting Senior PM or Staff PM roles at Meta, Google, Amazon, Microsoft, Grab, or equivalent — located in the US, India, or Singapore. They have strong day-to-day PM instincts but have never been evaluated against a FAANG or regional tech calibration rubric and do not know which axes are weakest.
Job to be done: When I am preparing for a senior PM interview at a specific company, I want realistic axis-specific practice with feedback calibrated to how that company actually evaluates candidates, so I can identify and close my weakest axes before the real interview.

SUCCESS METRICS
North Star: Share of practicing users who receive an offer from a target company within 90 days of completing a full axis practice set.
Feature metrics:
- Full axis set completion rate: percentage of users who complete at least one session across all six axes within 14 days of signup (benchmark: onboarding completion above 60%)
- Follow-up question acceptance rate: percentage of sessions where the user engages with at least one AI follow-up per session above 50%
Business metrics:
- 30-day retention among users who complete axis diagnosis in week one vs. those who do not
- Referral rate among users who self-report an offer outcome (word of mouth is the primary growth loop for career tools)
MVP metrics (first 4 to 8 weeks):
- Session completion rate per axis (start question → submit answer → receive feedback): ladders to North Star by confirming the evaluation loop works and candidates find it credible enough to finish
- Axis weakness identification rate (users who discover a previously unknown gap via feedback): ladders to North Star as the leading signal that the tool is doing what generic prep cannot

SOLUTION DIRECTION
Hypothesis: A structured practice tool that generates company-tagged, axis-specific questions and evaluates answers against a calibrated rubric — with a follow-up question per session — will outperform generic prep by surfacing specific gaps candidates cannot self-diagnose.
Product North Star (PR/FAQ): A senior PM candidate logs in knowing only their target company and interview date. Within one week they know exactly which of the six axes is their weakest, have practiced against that company's actual hiring bar, and have a concrete action plan — no coach required.
MVP scope: Three axes in v1 — product sense, behavioral, and analytical thinking. Two company profiles — Meta and Google — as calibration anchors. Single follow-up question per session generated by AI. Feedback structured as: what landed, what was missing, what a strong answer included. Out of scope: system design, AI system design, vibe coding (deferred to Phase 2), additional company profiles beyond two, live human review, and resume or job application features.
Internationalization phases: Phase 1 — US market, English only, Meta and Google profiles (largest candidate pool, most documented hiring rubrics). Phase 2 — India market, add Amazon and Microsoft profiles, calibrate rubrics to India PM interview patterns where behavioral axis weight differs from US (Productify data confirms India senior PM demand is highest, justify dedicated calibration). Phase 3 — Singapore and Southeast Asia, add Grab and regional tech profiles, adjust for hybrid product-operational interview axes common at Grab and Sea.

RISKS AND TRADE-OFFS
- Candidates: If rubric calibration is off by company or axis, users build false confidence and fail at a higher rate than without the tool — the trust cost of one bad outcome is high in a referral-driven category.
- Product and engineering: Vibe coding as an interview axis is emergent and has no established evaluation standard; deferring it creates a gap but shipping a poorly evaluated version creates credibility risk.
- Market calibration: Interview rubrics and axis weighting differ meaningfully between US, India, and Singapore markets — a single rubric shipped globally will underserve at least two of the three and risk negative word of mouth in high-density candidate communities.

OPEN QUESTIONS
1. Can sufficiently detailed, company-specific rubrics for Meta and Google be constructed from public sources and candidate reports — or does accuracy require proprietary signal that is unavailable at launch?
2. What is the minimum number of practice sessions per axis required before a user's axis weakness diagnosis is statistically reliable enough to act on?
3. Is the primary acquisition channel community-led (PM Slack groups, LinkedIn India PM communities, local bootcamps in Singapore) — and does that change the MVP feature set needed to drive organic sharing?
```

**Run B notes — v3 prompt validated:** Full v3 structure generated without manual editing. Strongest vs. Run A: PR/FAQ North Star (concrete user outcome in one week), MVP metric (axis weakness identification rate), Open Question #3 (acquisition channel). Gaps vs. Run A: user seniority drift (mid-level 3–7 years vs. input "senior PM candidates"); Why Now missing Lenny/TrueUp 7,300+ roles and Leadership PM +31% YoY; no legal/trademark risk; MVP scope broader (3 axes, Meta + Google vs. 2 axes, Meta + Amazon).

**Run A vs. Run B — which to use for Week 7 build:**
- **User definition and seniority:** Run A (matches input)
- **PR/FAQ narrative and MVP leading metrics:** Run B
- **MVP scope conservatism:** Run A (2 axes, Streamlit named)
- **i18n phasing logic:** Both strong; Run B adds India behavioral weight rationale explicitly

---

## Test case #3 — AI Career Re-entry Coach for Women (Used, Jul 10, 2026)

**Input:** "Women returning to product management after a career break of 1 to 5 years can't articulate their transition story for AI-era roles. Existing career coaching tools are built for continuous employment, not re-entry. The narrative gap — not the skills gap — is why they get screened out before the first interview."

**Model:** Claude Sonnet 4.6 · temperature 0

### Run C — v3 review session (Jul 10, 2026)

**Score:** 8.5/10

```
Brief type: New Feature

PROBLEM
Women returning to PM careers after a 1 to 5 year career break are screened out before the first interview because they cannot articulate a credible transition story for AI era PM roles. Existing career coaching tools assume continuous employment and optimize for resume formatting or skills gap analysis, not the narrative reframe that recruiters and hiring managers evaluate first.

WHY NOW
AI PM roles are expanding faster than continuous employment coaching can adapt: 12,000+ professionals moved into AI PM titles between Jan 2024 and Oct 2025, roughly doubling the prior year (Productify, 2025 AI PM migration). Senior PM demand is rising (+11% YoY globally; +87% YoY for Senior PM in India per Productify, 2025), which raises the bar on how candidates frame product judgment, not just tenure continuity. Career breaks that were once explainable with a generic "stepped away for family" line now fail against loops that expect fluency on AI product strategy, evals, and agentic workflows. The narrative gap is widening as job descriptions shift faster than re entry coaching content updates.

USER
Women with 4 to 12 years of prior PM experience (IC or first line manager) returning after 1 to 5 years away from full time product work. They are targeting PM or Senior PM roles in tech, often in the US or India, and have relevant skills from prior roles but cannot connect their break, prior wins, and AI era expectations into one coherent story hiring teams trust.
Job to be done: When I am restarting my PM job search after a career break, I want a transition narrative that reframes my gap and prior experience for AI era roles, so I can get past recruiter screens and into interviews without hiding my break.

SUCCESS METRICS
North Star: Interview screen pass rate (recruiter or HM advance to interview stage) among active users within 60 days of first narrative draft
Feature metrics:
- Narrative draft completion rate above 60% within 7 days of signup (benchmark: onboarding completion)
- User rated narrative confidence score improvement of 2+ points on 1 to 5 scale after revision cycle
Business metrics:
- 30 day return rate among users in active job search (habit during high stakes window)
- Organic referral rate among users who report advancing to interview stage
MVP metrics (first 4 to 8 weeks):
- % of users who complete one full narrative arc (break framing + AI era skill bridge + role target): ladders to North Star by confirming the core output is finishable and usable
- Recruiter screen self report rate at 30 days vs. baseline stated at intake: ladders to North Star as leading indicator before sample size supports tracked outcomes
- Revision sessions per user in first 14 days: ladders to North Star by measuring whether feedback loop drives narrative improvement, not one shot generation

SOLUTION DIRECTION
Hypothesis: A guided narrative builder that elicits break context, prior PM proof points, and target role requirements — then synthesizes a transition story, skill gap map, and outreach templates calibrated for AI era PM loops — will outperform generic resume coaches by addressing the screening narrative, not the skills checklist.
Product North Star (PR/FAQ): A woman returning to PM after a multi year break leaves the product with a transition story she can deliver confidently in recruiter screens, LinkedIn outreach, and first round interviews — with her career break framed as context, not a liability, and her AI era relevance stated in language hiring teams actually use.
MVP scope: Single flow web app (Streamlit). Inputs: break length, prior role and level, target role and market (US or India), top 3 prior product wins. Outputs: transition narrative (3 versions: recruiter screen, LinkedIn, HM intro), skill gap map (narrative gaps only, not course catalog), 3 outreach templates. Out of scope: interview loop practice, resume parsing, job matching, human coach marketplace, payment, Singapore market, men or non PM roles.
Internationalization phases: Phase 1 US English (largest documented re entry coaching market; rubric sources for tech PM narrative norms most accessible). Phase 2 India English (Productify senior PM demand +87% YoY justifies dedicated market; adjust examples for India tech employers and break stigma norms). Phase 3: expand to Senior Manager transitions and optional Hindi output — pending user research on language preference for narrative drafting.

RISKS AND TRADE-OFFS
- Users: AI generated narratives that over polish the break or invent AI fluency the candidate cannot defend in live interviews create false confidence and higher fail rates at onsite stages.
- Users (India): US calibrated narrative framing (direct gap ownership, individual accomplishment emphasis) may misfit India hiring contexts where team continuity and institutional context carry different weight.
- Company/platform: Sensitive personal data (break reason, family context) requires strict data handling and clear retention policy; mishandling destroys trust in an emotionally high stakes product category.
- Regulators/legal: Positioning outputs as improving hiring outcomes without validated rubrics risks overclaiming; coaching tool must avoid employment guarantee language.

OPEN QUESTIONS
1. What narrative elements do recruiters and HMs at target companies actually weight for re entry candidates — break framing, AI fluency proof, or prior scope — and is this sourced from interviews or inferred?
2. Will users trust AI with break context (family, health, relocation) required for authentic narrative — or does trust require human review option at MVP?
3. Is interview screen pass rate measurable at MVP scale via self report only, or is a recruiter validation panel required before North Star is credible?
```

**Run C notes — strengths:**
- Correct infer: narrative gap, not skills gap, preserved from input
- JTBD explicit; PR/FAQ concrete; MVP metrics ladder to North Star
- i18n phasing with rationale; risks include India calibration and sensitive data
- No monetization assumed; Streamlit named in MVP scope (matches Week 10 plan)

**Run C notes — gaps:**
- Why Now leans on AI PM migration stats; no re entry specific labor data cited (none in input or market context reference — flag as assumption)
- User seniority range inferred (4 to 12 years prior) — not in input; should surface in Open Questions
- "Narrative confidence 1 to 5 scale" is a product invention not grounded in input
- Singapore omitted from user sentence though input is silent on markets; acceptable but worth confirming

**Review checklist for Phillip:**
| Section | Pass? | Notes |
|---|---|---|
| Infer then classify | ✓ | Problem reframed from narrative vs skills insight |
| Why Now + citations | Partial | Cited Productify; no re entry specific data available |
| JTBD | ✓ | When / I want / so I can present |
| Metric tiers + ladder | ✓ | Four tiers; MVP clauses present |
| PR/FAQ vs hypothesis | ✓ | Separated |
| MVP scope in/out | ✓ | Matches Week 10 single flow plan |
| Open Questions = blockers | ✓ | Trust, rubric source, North Star measurability |
| No invented constraints | Partial | Seniority range and confidence scale invented |

---

### Run D — v3.1 re-run (Jul 10, 2026, seniority patch)

**Score:** 9/10 · v3.1 system prompt

```
Brief type: New Feature

PROBLEM
Women returning to product management after a 1 to 5 year career break are screened out before the first interview because they cannot articulate a credible transition story for AI era PM roles. Existing career coaching tools assume continuous employment and address skills gaps, not the narrative reframe recruiters evaluate first.

WHY NOW
PM hiring is rebounding while job expectations shift: 7,300+ open PM roles globally, 75% above the early 2023 low (Lenny's Newsletter / TrueUp, early 2026). AI PM titles expanded rapidly — 12,000+ professionals moved into AI PM roles Jan 2024 to Oct 2025, roughly 2x the prior year (Productify, 2025 AI PM migration). Re entry candidates face a double bar: explain the break and demonstrate AI era relevance in one narrative. Continuous employment coaching content has not caught up. Niche job specific tools win by depth over generic ChatGPT career advice (a16z State of Consumer AI, 2025).

USER
Women returning to product management after a 1 to 5 year career break who are restarting a job search for AI era PM roles. They have prior PM experience but struggle to connect their break, prior wins, and current role expectations into a story that survives recruiter screening.
Job to be done: When I am re entering PM hiring after a career break, I want a transition narrative that reframes my gap and prior experience for AI era roles, so I can get past recruiter screens without hiding my break.

SUCCESS METRICS
North Star: Share of active users who self report advancing past recruiter screen to interview stage within 60 days of first narrative draft
Feature metrics:
- Narrative draft completion rate above 60% within 7 days of signup (benchmark: onboarding completion)
- % of users who use at least one output (narrative or outreach template) in a real job search touchpoint within 14 days
Business metrics:
- 30 day return rate among users in active job search
- Organic referral rate among users who report advancing to interview stage
MVP metrics (first 4 to 8 weeks):
- % completing full narrative arc (break framing + AI era bridge + role target): ladders to North Star by confirming core output is finishable
- Outreach template usage within 14 days: ladders to North Star as behavioral signal that narrative is actionable, not abandoned
- Revision sessions per user in first 14 days: ladders to North Star by measuring whether feedback improves draft quality before screen stage

SOLUTION DIRECTION
Hypothesis: A guided narrative builder that elicits break context, prior PM proof points, and target role requirements — then outputs a transition story, narrative skill gap map, and outreach templates for AI era PM loops — will outperform generic resume coaches by fixing the screening story, not the skills checklist.
Product North Star (PR/FAQ): A woman returning to PM after a multi year break finishes the product with a transition story she can deliver in recruiter screens and LinkedIn outreach — career break framed as context, not liability, with AI era relevance in language hiring teams use.
MVP scope: Single flow web app (Streamlit). Inputs: break length, prior role description, target role and company type, top 3 prior product wins. Outputs: transition narrative (recruiter screen, LinkedIn, HM intro versions), narrative gap map, 3 outreach templates. Out of scope: interview practice, resume parsing, job matching, human coach marketplace, payment, non PM roles.
Internationalization phases: Single market launch — no i18n phasing required. Input does not specify geography; confirm launch market before Phase 2 expansion.

RISKS AND TRADE-OFFS
- Users: AI narratives that over polish the break or claim AI fluency the candidate cannot defend in live interviews create false confidence and higher onsite fail rates.
- Company/platform: Break context (family, health, relocation) is sensitive personal data; weak retention or unclear privacy policy destroys trust in a high stakes coaching category.
- Regulators/legal: Coaching outputs must avoid employment guarantee language; hiring outcome claims need validated rubrics or careful positioning.

OPEN QUESTIONS
1. What seniority level and prior PM scope should the product optimize for — IC, Senior PM, or Manager — and how does narrative framing differ by level?
2. What narrative elements do recruiters actually weight for re entry candidates, and is this validated by interviews or inferred from public sources?
3. Which geography and employer segment (big tech, startup, India market) should Phase 1 target — input is silent on market?
```

**Run D vs Run C — patch validation:**

| Check | Run C | Run D |
|---|---|---|
| Seniority range invented | Fail ("4 to 12 years") | Pass (no tenure invented) |
| Seniority in Open Questions | Missing | Pass (Open Question #1) |
| Confidence scale invented | Fail (1 to 5 scale) | Pass (replaced with outreach usage) |
| Markets invented | Partial (US or India) | Pass (single market default; market in OQ #3) |
| Break length 1 to 5 years | Pass (from input) | Pass (from input) |
| Full v3 structure | Pass | Pass |
| MVP matches Week 10 plan | Pass | Pass |

**Run D notes — remaining gaps:**
- Why Now still uses PM hiring / AI PM migration proxies; no re entry specific data (acceptable given market context reference limits)
- i18n phasing correctly deferred pending market confirmation
- Use Run D as build brief anchor for Week 10; merge PR/FAQ phrasing from Run C if preferred

---

## Test case backlog — next sessions

Run one input per Thursday session before starting the main build work. Mark as used after running. Pull the next **Open** row, in table order. Table order tracks each project's actual build week in `Knowledge/6-month-plan.md` — re-sync whenever that plan's project sequence changes (see note below, updated July 30, 2026).

| # | Status | Project | Input |
|---|---|---|---|
| 1 | Used (v2) | IG Fundraising | "Drive awareness for IG users and Creators to fundraise during key giving moments. Assume users dont know they can do this in the app." |
| 2 | Used (v3) | AI PM Interview Coach — GitHub Project 2, Weeks 7 to 8 build | See test case #2 above |
| 3 | Used (v3, Jul 10, 2026) | AI Career Re-entry Coach for Women — GitHub Project 3, Weeks 10 to 11 build | "Women returning to product management after a career break of 1 to 5 years can't articulate their transition story for AI-era roles. Existing career coaching tools are built for continuous employment, not re-entry. The narrative gap — not the skills gap — is why they get screened out before the first interview." |
| 4 | Open | AI Monetization Strategy Advisor — GitHub Project 4, Week 12 build | "AI startups choose monetization models based on familiarity, not fit. Subscription, usage-based, freemium, and outcome-based models have fundamentally different implications for unit economics, churn, and growth — but most founding teams pick the model they've seen before, not the one that matches their cost structure and user behavior." |
| 5 | Open | India Wealth Coach — GitHub Project 5, Weeks 14 to 15 build | "Indian consumers, especially salaried employees, gig workers, and first-time earners, want to save and invest but don't know where to start. Existing wealth apps assume financial literacy, English-only interfaces, and desktop-first design that doesn't match how most people in India access the internet: mobile, low-bandwidth, and often mixing regional language with English. There is no phone-first tool that teaches SIP, PPF, ELSS, FD, and UPI-era cash flow management at the user's own pace, in Hindi or English." |
| 6 | Open | AI Founder Financial Literacy Tool — GitHub Project 6, Week 23 build | "First-time founders signing term sheets lack the financial literacy to identify unfavorable clauses — anti-dilution provisions, liquidation preferences, pro-rata rights — and don't know what questions to ask their investors before signing. Most learn what they gave away only after the next funding round." |
| 7 | Open | Stress test: regulatory input | "A healthcare AI startup is building a clinical decision support tool for emergency department triage in India. The tool must comply with CDSCO regulations and operate within hospital EMR systems. Response time under 2 seconds is required. The founding team wants to launch in 3 hospitals within 6 months." |
| 8 | Open | Stress test: conflicting stakeholders | "Amazon wants to add AI-generated product recommendations to the checkout page that personalize in real time based on cart contents and browsing history. The ads team wants the recommendations to be sponsored placements. The trust team is concerned about undisclosed personalization. The seller team wants organic recommendations to remain possible." |

**Table re-sync note (July 30, 2026):** Rows 4 to 6 previously carried stale week labels (a "Vibe Coding Tutorial, Week 11" row that no longer exists in the current 6-month plan, "Founder Financial Literacy, Week 14" instead of Week 23, "Monetization Advisor, Week 22" instead of Week 12) from before the plan was restructured. Replaced the dead Vibe Coding Tutorial row with India Wealth Coach — GitHub Project 5, which had no backlog test case at all — and reordered rows 4 to 6 to match actual build order (Monetization Advisor, Week 12 → India Wealth Coach, Weeks 14 to 15 → Founder Financial Literacy, Week 23).

---

## Prompt design lessons — v3

**MVP metrics force the North Star to be operational.** A North Star metric that only measures at 90 days is useless for a first launch. Requiring MVP metrics with explicit ladder clauses forces the PM to name what you measure in week 4 that predicts week 12 success.

**PR/FAQ North Star separates product vision from hypothesis.** The hypothesis is what you build first. The PR/FAQ line is why anyone should care if it works. Keeping both in Solution Direction prevents conflating a feature bet with a product narrative.

**Market context in the system prompt, not the user input.** PM hiring briefs need labor market data the user will not paste every time. Embedding a reference block with cited sources lets Why Now stay credible without inventing statistics per run.

**v3 Workbench validation (test case #2, Run B).** First automated run of the v3 prompt scored 9/10 and produced the full structure without manual editing. Compare Run A (manual calibration) vs. Run B (Workbench) side by side when merging into a build brief — neither is strictly better; Run A is tighter on user seniority and MVP scope, Run B is stronger on PR/FAQ narrative and MVP leading metrics.

**v3.1 patch (Jul 10, 2026, test case #3 review).** Added seniority rule after Run C invented "4 to 12 years prior PM experience" from re entry input. USER section and Rules now forbid inferring experience ranges; unclear seniority goes to Open Questions.

---

## Related

- V1: `experiments/week-2/pm-brief-generator-v1.md` (baseline, Post #2 "before")
- V2: `experiments/week-2/pm-brief-generator-v2.md` (frozen, Post #2 "after")
- Week 2 learnings: `Knowledge/learnings/week-02.md` — Day 3
- Substack Post #2: `Knowledge/Substack/week-02-post-system-prompt.md`
