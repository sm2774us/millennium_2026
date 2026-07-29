# Set 8 — Stakeholder Management & Communication (Behavioral)
**Total time budget: ~15 minutes** (all STAR-format; keep each to ~1.5–2 min so all 10 fit).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Describe a time you advised a portfolio manager on execution quality and they pushed back. How did you handle it?**](#q1-describe-a-time-you-advised-a-portfolio-manager-on-execution-quality-and-they-pushed-back-how-did-you-handle-it)
- [§2 · **Q2. Tell me about a time you had to translate a technical cost-analysis finding into a clear, actionable recommendation.**](#q2-tell-me-about-a-time-you-had-to-translate-a-technical-cost-analysis-finding-into-a-clear-actionable-recommendation)
- [§3 · **Q3. How do you balance being a subject-matter expert with being collaborative across trading, technology, and PM teams?**](#q3-how-do-you-balance-being-a-subject-matter-expert-with-being-collaborative-across-trading-technology-and-pm-teams)
- [§4 · **Q4. Describe a time you had to defend a research or analytical result under skeptical questioning.**](#q4-describe-a-time-you-had-to-defend-a-research-or-analytical-result-under-skeptical-questioning)
- [§5 · **Q5. How do you prioritize competing requests from multiple portfolio manager groups with limited time?**](#q5-how-do-you-prioritize-competing-requests-from-multiple-portfolio-manager-groups-with-limited-time)
- [§6 · **Q6. Tell me about a disagreement with a trading desk or technology partner on how to build an execution tool. How was it resolved?**](#q6-tell-me-about-a-disagreement-with-a-trading-desk-or-technology-partner-on-how-to-build-an-execution-tool-how-was-it-resolved)
- [§7 · **Q7. Describe how you've driven a roadmap or product decision that required aligning business priorities with technical implementation.**](#q7-describe-how-youve-driven-a-roadmap-or-product-decision-that-required-aligning-business-priorities-with-technical-implementation)
- [§8 · **Q8. How do you handle ambiguity when a PM gives you a vague request to "look into" their execution costs?**](#q8-how-do-you-handle-ambiguity-when-a-pm-gives-you-a-vague-request-to-look-into-their-execution-costs)
- [§9 · **Q9. Give an example of when you identified an opportunity to reduce trading costs and how you implemented the change.**](#q9-give-an-example-of-when-you-identified-an-opportunity-to-reduce-trading-costs-and-how-you-implemented-the-change)
- [§10 · **Q10. How do you approach knowledge transfer/documentation so your analysis is reproducible by teammates?**](#q10-how-do-you-approach-knowledge-transferdocumentation-so-your-analysis-is-reproducible-by-teammates)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Describe a time you advised a portfolio manager on execution quality and they pushed back. How did you handle it?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Would a technical pushback (disputing the methodology) or a personal/defensive pushback be more useful to hear about?"

**C) Detailed answer (STAR):**
> "**Situation:** At Highbridge, my execution monitoring flagged that a PM's mean-reversion strategy showed elevated slippage relative to peers trading similar futures products.
> **Task:** Communicate the finding and get buy-in for a change without the PM feeling audited or blamed.
> **Action:** Rather than opening with the conclusion, I opened with the decomposition (**[Set 3 → Q2. Explain implementation shortfall and how it decomposes into delay cost, market impact, and opportunity cost.](./ANSWER3.md#q2-explain-implementation-shortfall-and-how-it-decomposes-into-delay-cost-market-impact-and-opportunity-cost)** / **[Set 3 → Q8. How would you present a TCA finding that shows a PM's execution cost is worse than peers, without being adversarial?](./ANSWER3.md#q8-how-would-you-present-a-tca-finding-that-shows-a-pms-execution-cost-is-worse-than-peers-without-being-adversarial)**) — showed that the gap was almost entirely in the *delay cost* component, not market-impact skill, meaning the issue was upstream (signal-to-order latency), not the PM's trading judgment. The PM initially pushed back, arguing the comparison group wasn't a fair peer set. I re-ran the attribution controlling for order size/urgency (**[Set 3 → Q6. How would you attribute slippage across a portfolio manager group to identify a systemic cost driver?](./ANSWER3.md#q6-how-would-you-attribute-slippage-across-a-portfolio-manager-group-to-identify-a-systemic-cost-driver)**) specifically to address that objection, and showed the delay-cost gap persisted even after controlling for it.
> **Result:** The PM agreed to a change in signal-to-order pipeline latency, which measurably narrowed the gap in the following month's report — and the PM became one of the more receptive users of my monitoring going forward, because the first interaction established that I responded to legitimate methodological pushback with more rigor, not defensiveness."

**D) Feynman summary:** When a PM pushes back on a finding, the right response isn't to defend the conclusion — it's to treat the pushback as a testable hypothesis and re-run the analysis to check it; that's what actually earns trust, since it shows the finding wasn't fragile.

**E) Follow-ups:**
- *"What if the re-analysis had shown the PM was right?"* → I'd have said so directly and adjusted the finding — the credibility of the framework depends on it being falsifiable, not just defended.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Tell me about a time you had to translate a technical cost-analysis finding into a clear, actionable recommendation.

**A) Time budget:** 1.5–2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer (STAR):**
> "**Situation:** At BAM, a market-impact model fit (**[Set 3 → Q5. Describe how you would fit a market impact model using historical futures execution data.](./ANSWER3.md#q5-describe-how-you-would-fit-a-market-impact-model-using-historical-futures-execution-data)**) showed the pod's futures orders above a certain size threshold were incurring impact cost meaningfully above the fitted square-root-law curve, suggesting the desk's algo was too aggressive for large clips.
> **Task:** Turn a regression coefficient into something a PM/trader would act on.
> **Action:** Instead of presenting the $\eta,\beta$ coefficients, I translated the finding into a **concrete sizing rule**: 'orders above X contracts should be split into at least Y child slices over Z minutes to stay on the efficient part of the impact curve' — a specific, implementable instruction derived directly from the fitted model, with a before/after cost estimate in dollar terms attached.
> **Result:** The desk adopted the slicing guideline for large orders; the following quarter's TCA showed a measurable reduction in impact cost for large orders specifically, without materially increasing delay/opportunity cost."

**D) Feynman summary:** A regression coefficient means nothing to a trader; a specific instruction ('split orders bigger than X into Y slices') derived from that coefficient means everything — the job of translation is converting statistical output into an operational rule.

**E) Follow-ups:**
- *"How did you validate the recommendation actually worked, not just assume it?"* → Same out-of-sample validation discipline as **[Set 6 → Q10. Describe how you'd validate a trading-cost model out-of-sample before rolling it out firm-wide.](./ANSWER6.md#q10-describe-how-youd-validate-a-trading-cost-model-out-of-sample-before-rolling-it-out-firm-wide)** — measured the following period's realized impact cost against the pre-change baseline.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. How do you balance being a subject-matter expert with being collaborative across trading, technology, and PM teams?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> "My approach: be the most rigorous voice in the room on the *math*, but treat the *implementation* as genuinely collaborative — technology often knows constraints I don't (system latency, data availability), and traders know market-microstructure nuance from lived experience that a model can't fully capture. Concretely, when I've proposed a cost model or monitoring change, I've always run it past technology for feasibility and past traders for a sanity-check against their intuition *before* finalizing it — not because I doubt the math, but because their pushback often reveals an assumption I hadn't tested. Expertise earns you the right to have a strong opinion; collaboration is making sure that opinion gets pressure-tested before it becomes policy."

**D) Feynman summary:** Being the expert means owning the rigor, not owning the final word — the best version of my analysis is always the one that's survived contact with the people who'll actually implement and live with it.

**E) Follow-ups:**
- *"Give a specific example where a trader's pushback changed your model."* → The Q1 example above is a version of this — the "unfair peer group" objection led directly to a more rigorous control, changing the analysis, not just the communication.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Describe a time you had to defend a research or analytical result under skeptical questioning.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Skepticism from a PM, or from a more senior quant/risk reviewer?" (Changes what "defending" looks like — technical peer review vs. business stakeholder.)

**C) Detailed answer (STAR):**
> "**Situation:** At Millburn, a senior risk reviewer challenged one of my macro signals' validated Sharpe ratio, suspecting the Combinatorial Purged Cross-Validation (CPCV) methodology was still leaking information given the number of independent signal variants tested.
> **Task:** Defend the result or acknowledge a real overfitting risk — multiple-testing bias is a legitimate concern when many signal variants are explored.
> **Action:** Rather than simply asserting the CPCV was sufficient, I computed the **deflated Sharpe ratio** (accounting explicitly for the number of trials/variants tested, per the Bailey-López de Prado framework) to directly quantify how much of the raw Sharpe should be discounted for multiple testing, and presented both numbers side by side.
> **Result:** The deflated Sharpe remained comfortably above the deployment threshold, which resolved the reviewer's concern with a *stronger* standard than the one they'd raised, rather than just reasserting confidence in the original methodology — and it became a standard additional check I ran on future signals before presenting them."

**D) Feynman summary:** The best way to defend a result under skepticism isn't to argue harder for your original number — it's to apply an even stricter test that directly addresses the specific concern being raised, and let the result speak for itself.

**E) Follow-ups:**
- *"What if the deflated Sharpe had fallen below threshold?"* → I'd have shelved or reworked the signal — the whole point of the check is that it's a real gate, not theater.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. How do you prioritize competing requests from multiple portfolio manager groups with limited time?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed — mirrors **[Set 1 → Q7. How do you handle being one of a small group of technical execution quants supporting many PMs at once?](./ANSWER1.md#q7-how-do-you-handle-being-one-of-a-small-group-of-technical-execution-quants-supporting-many-pms-at-once)**, keep this answer focused on prioritization framework specifically rather than repeating the tooling-scale answer verbatim.

**C) Detailed answer:**
> "I triage on two axes: **urgency** (is a live decision blocked on this, e.g., a broker/algo choice for size going out today) and **leverage** (does solving this well for one PM generalize to many, e.g., a systemic cost driver vs. a one-off question). High-urgency/high-leverage gets immediate attention; high-urgency/low-leverage gets a fast, good-enough answer without over-investing; low-urgency/high-leverage gets scheduled as proper project work (the reusable tooling investment from **[Set 1 → Q7. How do you handle being one of a small group of technical execution quants supporting many PMs at once?](./ANSWER1.md#q7-how-do-you-handle-being-one-of-a-small-group-of-technical-execution-quants-supporting-many-pms-at-once)**); low-urgency/low-leverage gets queued and batched. I communicate this triage explicitly to requesters rather than silently reordering — 'I can give you a quick directional answer today, or a rigorous one by Thursday' — so PMs aren't left guessing about where they stand."

**D) Feynman summary:** Two questions sort almost any request queue: does this need an answer right now, and will solving it well help more than one person — combine those into a 2x2 and the priority order falls out on its own.

**E) Follow-ups:**
- *"What if two PMs both claim urgency simultaneously?"* → Escalate the tie-break to the desk head/manager rather than adjudicate PM seniority myself — that's a business-priority call, not an analytical one.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. Tell me about a disagreement with a trading desk or technology partner on how to build an execution tool. How was it resolved?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer (STAR):**
> "**Situation:** At Highbridge, technology wanted to build the execution-monitoring dashboard as a nightly batch job for simplicity/lower engineering cost; I wanted near-real-time monitoring so anomalies (**[Set 6 → Q6. What statistical approach would you use to detect a regime shift in trading costs (e.g., pre/post a liquidity event)?](./ANSWER6.md#q6-what-statistical-approach-would-you-use-to-detect-a-regime-shift-in-trading-costs-eg-prepost-a-liquidity-event)**) could be caught intraday rather than the next morning.
> **Task:** Resolve a genuine cost/benefit disagreement, not just a preference difference.
> **Action:** Instead of arguing in the abstract, I quantified the cost of the delay — estimated, from historical anomaly cases, how much additional cost accumulated in the hours between an issue starting and a next-morning batch report catching it. That gave both sides a shared number to reason about instead of competing intuitions about engineering effort vs. usefulness.
> **Result:** We compromised on a middle path — a lightweight intraday alert on a small set of high-priority anomaly checks (cheap to build, catches the costliest failure modes) alongside the full nightly batch report for comprehensive analysis — cheaper than full real-time, but addressing the core risk I was worried about."

**D) Feynman summary:** Disagreements about how much engineering effort something deserves get resolved by putting a number on what the *lack* of that effort actually costs — once both sides are arguing from the same quantified stakes, the compromise usually finds itself.

**E) Follow-ups:**
- *"What if you couldn't quantify the cost of the delay?"* → I'd propose a small, cheap pilot to generate real data on the question rather than debate an unquantifiable hypothetical indefinitely.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. Describe how you've driven a roadmap or product decision that required aligning business priorities with technical implementation.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer (STAR):**
> "**Situation:** At BAM, I identified that the pod's execution-monitoring tooling was built as several one-off scripts (**[Set 5 → Q4. Walk through your approach to building a reusable execution-analytics reporting library.](./ANSWER5.md#q4-walk-through-your-approach-to-building-a-reusable-execution-analytics-reporting-library)** or **[Set 5 → Q10. Describe a time you had to productionize a research notebook into a maintained tool.](./ANSWER5.md#q10-describe-a-time-you-had-to-productionize-a-research-notebook-into-a-maintained-tool)** 'notebook' problem at a team level, not just individual level).
> **Task:** Make the case for investing engineering time in a proper reusable library rather than continuing to patch individual scripts, competing against other, more visibly-urgent roadmap items.
> **Action:** I built the business case in terms decision-makers cared about: estimated the recurring time cost of maintaining fragmented scripts (hours/month across the team), and separately, the risk cost of inconsistent methodology across ad hoc scripts (two analysts computing 'slippage' slightly differently, undermining trust in numbers). I proposed a phased build — first consolidate the benchmark layer (highest reuse value, lowest effort) before the full attribution/reporting layers — so the roadmap showed incremental value rather than asking for a large upfront investment with no interim payoff.
> **Result:** The phased library became the standard tool used pod-wide, and it directly informed how I'd approach this exact Execution Services team's own reusable-analytics mandate at Millennium."

**D) Feynman summary:** Winning a roadmap argument for infrastructure work means translating "this is messy" into "this costs N hours/month and creates M dollars of trust risk," and then de-risking the ask itself by phasing it so the first deliverable pays for itself before asking for the rest.

**E) Follow-ups:**
- *"How did you handle the team members whose scripts you were replacing?"* → Involved them in defining the shared library's interface early, so it felt like consolidating their work into something more durable, not discarding it.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. How do you handle ambiguity when a PM gives you a vague request to "look into" their execution costs?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** "Would you like this as a general framework, or with a specific worked example?"

**C) Detailed answer:**
> "First move is always a short clarifying conversation, not diving straight into analysis: 'is there a specific trade/period/instrument that prompted this, or is this a general health-check?' — because 'look into my costs' after a specific bad trade needs a totally different investigation than a periodic review. Absent a clear answer, I default to a **standard triage pass**: pull the PM's recent TCA scorecard (Q1's decomposition — delay/impact/opportunity), scan for anything statistically outside their normal range (**[Set 6 → Q6. What statistical approach would you use to detect a regime shift in trading costs (e.g., pre/post a liquidity event)?](./ANSWER6.md#q6-what-statistical-approach-would-you-use-to-detect-a-regime-shift-in-trading-costs-eg-prepost-a-liquidity-event)**-style changepoint check), and come back with either 'here's a specific issue I found and a recommendation' or 'nothing stands out as anomalous — here's your current baseline for reference' rather than open-endedly digging with no defined stopping point."

**D) Feynman summary:** Ambiguous requests get a quick clarifying question first, and failing that, a bounded standard-triage pass with a defined output — never open-ended exploration with no natural stopping point, which is how vague requests turn into unbounded time sinks.

**E) Follow-ups:**
- *"What if the PM genuinely doesn't know what they're looking for either?"* → That's fine — the standard triage pass output ('here's your baseline, here's anything anomalous') gives them something concrete to react to and refine the ask from.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. Give an example of when you identified an opportunity to reduce trading costs and how you implemented the change.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — reuses Q2's example structure; provide a distinct example to avoid repetition if this set is asked back-to-back with Q2.

**C) Detailed answer (STAR):**
> "**Situation:** At Highbridge, roll-cost attribution ( **[Set 2 → Q6. What operational considerations arise when rolling a futures position near contract expiry?](./ANSWER2.md#q6-what-operational-considerations-arise-when-rolling-a-futures-position-near-contract-expiry)**, **[Set 3 → Q4. How would you adapt an equities TCA framework to account for futures-specific costs (roll cost, spread cost)?](./ANSWER3.md#q4-how-would-you-adapt-an-equities-tca-framework-to-account-for-futures-specific-costs-roll-cost-spread-cost)** ) revealed that futures rolls executed in the final 1-2 days before expiry consistently showed worse calendar-spread slippage than rolls executed earlier in the roll window, across multiple PM groups.
> **Task:** Turn that pattern into an actual process change, not just a reported observation.
> **Action:** I proposed and helped implement a **standardized roll-window guideline** — recommending rolls begin by a specific day-count before expiry based on the historical liquidity-migration curve (**[Set 2 → Q6. What operational considerations arise when rolling a futures position near contract expiry?](./ANSWER2.md#q6-what-operational-considerations-arise-when-rolling-a-futures-position-near-contract-expiry)**) for each product, rather than leaving roll timing purely to individual trader discretion, and built the roll-cost dashboard so traders could see real-time roll-spread levels against historical norms while deciding when to execute.
> **Result:** Average roll-cost (in bps of spread) improved measurably across the affected products in subsequent quarters, and the roll-cost dashboard became a standing tool referenced during every roll window."

**D) Feynman summary:** The pattern ("last-minute rolls cost more") only became useful once it turned into a specific guideline plus a real-time tool that let traders act on it in the moment — a finding in a quarterly report doesn't change behavior; a live dashboard checked during the decision does.

**E) Follow-ups:**
- *"How did you get buy-in for a firm-wide guideline rather than leaving it as PM discretion?"* → Showed the cross-PM-group consistency of the pattern (**[Set 3 → Q6. How would you attribute slippage across a portfolio manager group to identify a systemic cost driver?](./ANSWER3.md#q6-how-would-you-attribute-slippage-across-a-portfolio-manager-group-to-identify-a-systemic-cost-driver)** — Set 3 Q6's systemic-driver logic) — it wasn't one trader's issue, it was structural, which made the case for a standard rather than individual coaching.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. How do you approach knowledge transfer/documentation so your analysis is reproducible by teammates?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> "Three habits: (1) every reusable calculation lives in the tested library ( **[Set 5 → Q4. Walk through your approach to building a reusable execution-analytics reporting library.](./ANSWER5.md#q4-walk-through-your-approach-to-building-a-reusable-execution-analytics-reporting-library)** / **[Set 5 → Q7. How would you structure unit tests for a transaction-cost calculation function?](./ANSWER5.md#q7-how-would-you-structure-unit-tests-for-a-transaction-cost-calculation-function)** ), not a one-off notebook, with docstrings that state the exact formula/methodology, not just what the function does mechanically; (2) every non-obvious methodological choice (e.g., why Newey-West with a specific lag length, why a particular peer-group definition) gets a short written rationale alongside the code, so a teammate encountering it later understands *why*, not just *what*; (3) for larger findings, a short write-up following the same structure I'd use in a PM presentation (**[Set 3 → Q8. How would you present a TCA finding that shows a PM's execution cost is worse than peers, without being adversarial?](./ANSWER3.md#q8-how-would-you-present-a-tca-finding-that-shows-a-pms-execution-cost-is-worse-than-peers-without-being-adversarial)**) — finding, methodology, validation, caveats — so a teammate could pick it up and defend it to a skeptical stakeholder without needing me in the room."

**D) Feynman summary:** Good documentation isn't a description of what the code does — code already says that — it's a record of *why* the choices were made, so someone else can extend or defend the work without having to reverse-engineer your reasoning from scratch.

**E) Follow-ups:**
- *"How do you keep documentation from going stale as the code evolves?"* → Keep the rationale next to the code it justifies (docstrings/inline comments) rather than in a separate wiki that's easy to forget to update, and treat documentation updates as part of the definition-of-done for any change to shared library logic.

[🔝 Back to Top](#-table-of-contents)

---
