# Set 1 — Recruiter/HR Screen: Resume + Motivation
**Total time budget: ~8–10 minutes of a 60-minute screen** (this set is typically front-loaded and compressed; recruiter screens rarely burn more than 10–12 min on rapport/motivation before handing to the technical interviewer).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Walk me through your resume, focusing on your execution and TCA experience at BAM, Highbridge, and Millburn Ridgefield.**](#q1-walk-me-through-your-resume-focusing-on-your-execution-and-tca-experience-at-bam-highbridge-and-millburn-ridgefield)
- [§2 · **Q2. Why are you interested in the Execution Services team at Millennium specifically, versus staying in alpha research?**](#q2-why-are-you-interested-in-the-execution-services-team-at-millennium-specifically-versus-staying-in-alpha-research)
- [§3 · **Q3. What do you know about Millennium's central trading desk and how it partners with portfolio managers?**](#q3-what-do-you-know-about-millenniums-central-trading-desk-and-how-it-partners-with-portfolio-managers)
- [§4 · **Q4. Why leave a Senior Quantitative Researcher title to move into an execution-focused specialist role?**](#q4-why-leave-a-senior-quantitative-researcher-title-to-move-into-an-execution-focused-specialist-role)
- [§5 · **Q5. Describe your compensation expectations given the posted $145k–$200k base range.**](#q5-describe-your-compensation-expectations-given-the-posted-145k200k-base-range)
- [§6 · **Q6. What is your visa/work-authorization status and are you open to being in the New York office full-time?**](#q6-what-is-your-visawork-authorization-status-and-are-you-open-to-being-in-the-new-york-office-full-time)
- [§7 · **Q7. How do you handle being one of a small group of technical execution quants supporting many PMs at once?**](#q7-how-do-you-handle-being-one-of-a-small-group-of-technical-execution-quants-supporting-many-pms-at-once)
- [§8 · **Q8. Tell me about a time you had to say no to a portfolio manager's request. How did you frame it?**](#q8-tell-me-about-a-time-you-had-to-say-no-to-a-portfolio-managers-request-how-did-you-frame-it)
- [§9 · **Q9. What is your timeline and are you interviewing elsewhere right now?**](#q9-what-is-your-timeline-and-are-you-interviewing-elsewhere-right-now)
- [§10 · **Q10. What questions do you have about the team structure or reporting line for this role?**](#q10-what-questions-do-you-have-about-the-team-structure-or-reporting-line-for-this-role)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Walk me through your resume, focusing on your execution and TCA experience at BAM, Highbridge, and Millburn Ridgefield.

**A) Time budget:** 3–4 minutes (this is the "reverse chronological, 60-second-per-role" pattern — don't let it run long, it eats your technical time).

**B) Follow-ups to ask interviewer before answering:**
- "Would you like me to focus more on the execution/TCA angle specifically, or give the full research arc first?"
- "Is there a particular asset class (futures vs FX vs equities) you want me to emphasize given this is a futures-focused desk?"

**C) Detailed answer (interview commentary):**
> "Sure — I'll walk it in reverse chronological order and pull out the execution/TCA thread at each stop.
>
> At **Millburn Ridgefield** (14 years, 2008–2022), I built out a systematic macro signal library — 30+ signals across futures, equities, and FX — but the execution angle came in because every signal I shipped had to survive a **realistic cost model** before it went live. I built C++ signal engines with Mechanical Sympathy principles, and part of that discipline was measuring the *gap* between paper P&L and live P&L — which is really transaction cost analysis in disguise: slippage, market impact, and roll cost eating into a backtested Sharpe.
>
> At **Highbridge / JPM Asset Management** (2022–2025), the execution/TCA work became explicit. I designed execution-optimized mean-reversion and trend models across futures and FX, and I measured alpha quality using **Implementation Shortfall against VWAP and Arrival Price benchmarks** — the exact framework this Millennium role is built around. I also built adverse-selection models to detect informed order flow, which is directly relevant to understanding *why* slippage happens, not just measuring it.
>
> At **BAM** (current role, since May 2025), I lead **execution monitoring and transaction cost modeling** for a systematic macro pod trading equities, futures, FX, and derivatives — reconciling live execution performance against backtest expectations, which is the same reconciliation loop a dedicated Execution Services quant runs for PMs firm-wide, just scoped to one pod instead of many.
>
> So the throughline is: I've always been the person inside the research team who owns the 'does live match backtest' question — this role lets me do that as the primary mandate, across every PM group, instead of as one function within a broader research seat."

**D) Feynman-style summary — how this ties to systematic macro pod work:**
Think of a systematic pod as a factory that promises a certain output (a Sharpe ratio) based on a blueprint (the backtest). TCA is the quality-control inspector standing at the loading dock, checking whether what actually ships out the door matches the blueprint. I've been that inspector, informally, for 17 years, and formally for the last few — I'm just asking to make it my full job title.

**E) Anticipated follow-ups (condensed):**
- *"Which of those three roles most closely resembles this job?"* → Highbridge, because IS/VWAP/Arrival-Price benchmarking there is functionally identical to sell-side/execution-desk TCA methodology.
- *"Did you ever present a cost finding directly to a PM?"* → Yes, in TCM reviews at BAM; reframe as a Set 8 behavioral answer if asked to elaborate.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Why are you interested in the Execution Services team at Millennium specifically, versus staying in alpha research?

**A) Time budget:** 2 minutes.

**B) Follow-ups to ask:** "Is Execution Services here scoped mostly to futures, or cross-asset in practice day-to-day?" (Calibrates how much to lean into futures specifics vs. generalist framing.)

**C) Detailed answer:**
> "Two reasons, one structural and one personal. Structurally — at a multi-manager platform like Millennium, execution quality is *the* lever that's shared infrastructure across every pod; a basis point of slippage saved compounds across dozens of PMs, whereas an alpha signal only helps the one pod that owns it. That's a bigger-leverage seat.
>
> Personally — over 17 years I've noticed the part of research I enjoy most isn't finding the next signal, it's the rigor of proving whether something is *real*: out-of-sample validation, cost-adjusted P&L, distinguishing genuine edge from backtest artifacts. Execution Services is that discipline as the primary job, not a side function. I'm not leaving alpha research because I've soured on it — I'm moving toward the part of the discipline I was already gravitating to."

**D) Feynman summary:** Alpha research asks "can we find money?" Execution Services asks "are we keeping the money we found?" I've realized I like the second question better, and at a platform like Millennium the second question has firm-wide leverage.

**E) Follow-ups:**
- *"Won't you miss owning a P&L?"* → I'll own a cost-savings number instead — equally measurable, arguably more directly attributable to my own work.
- *"Isn't this a step down in seniority/scope?"* → Reframe: fewer signals owned, but broader surface area (every PM, every asset class) and more direct influence on realized returns firm-wide.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. What do you know about Millennium's central trading desk and how it partners with portfolio managers?

**A) Time budget:** 1.5–2 minutes.

**B) Follow-ups:** "Is the central desk purely agency/execution, or does it also run any principal risk?" (Shows awareness that multi-manager platforms vary here, invites correction rather than guessing.)

**C) Detailed answer:**
> "Millennium runs a multi-manager, pod-based model — dozens of independent PM teams generating signals, with a shared central trading desk that handles execution across asset classes so each pod doesn't need to rebuild broker relationships, algo access, and market-structure expertise from scratch. The desk's value proposition to a PM is: 'tell us what you want to trade, we'll get you the best risk-adjusted execution and give you the data to know it was good.' Execution Services quants sit inside that desk building the analytics — TCA, cost attribution, algo evaluation — that make the 'we got you good execution' claim provable rather than asserted. Given the futures-heavy framing of this specific req, I'd expect close partnership with the desk's futures execution traders on roll management, give-up/booking mechanics, and broker algo selection."

**D) Feynman summary:** Picture 40 small hedge funds sharing one very good trading floor instead of each building their own. The floor's currency of trust with the PMs is data — TCA is literally how the desk proves it's earning its keep.

**E) Follow-ups:**
- *"What would you want to know in your first week to understand this desk?"* → Broker/algo panel used, give-up structure, how PM cost feedback loops currently work, what dashboards already exist.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Why leave a Senior Quantitative Researcher title to move into an execution-focused specialist role?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed — answer directly, this is a title/ego-check question, hesitation reads badly.

**C) Detailed answer:**
> "Titles describe scope of ownership, not seniority of thinking. A 'Specialist' who is setting the cost-analysis standard that every PM on the desk is measured against has more platform-wide influence than a 'Senior Researcher' whose signal serves one pod. I've spent 17 years earning the technical depth — kdb+/q at scale, market-impact modeling, statistical rigor — that this role explicitly requires; I'd rather apply that depth where the blast radius of good work is the whole platform."

**D) Feynman summary:** I'm not downgrading my toolkit, I'm redirecting where it points — from one pod's alpha to every pod's execution quality.

**E) Follow-ups:**
- *"Are you worried about how this reads on your resume in 2 years?"* → No — "led TCA/cost framework across all PM groups at Millennium" reads as a broadening, not a narrowing, of scope.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. Describe your compensation expectations given the posted $145k–$200k base range.

**A) Time budget:** 1 minute — brief and non-defensive.

**B) Follow-ups:** "Can you share where in that band the role typically lands for someone with 17 years and a strong technical/kdb+ background, so I can give you a number that's realistic rather than just anchoring high?"

**C) Detailed answer:**
> "I'm comfortable in the posted range, and given my depth in market-impact modeling, kdb+/q at scale, and direct IS/VWAP/Arrival-Price TCA experience, I'd expect to land toward the upper end — but base is only one piece; I'd want to understand the full comp structure (bonus/PnL-linked component, if any) before fixating on a single number."

**D) Feynman summary:** Give a range, anchor near the top with a reason, defer the rest to total comp — don't negotiate against yourself in a screen.

**E) Follow-ups:**
- *"Is base the main driver for you or is bonus more important?"* → At a platform like Millennium, I'd expect bonus/performance component to matter more over time; I'm optimizing for the role fit first.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. What is your visa/work-authorization status and are you open to being in the New York office full-time?

**A) Time budget:** 30 seconds.

**B) Follow-ups:** none — answer factually and immediately.

**C) Detailed answer:**
> "I'm a U.S. work-authorized [citizen/permanent resident/etc. — state actual status], no sponsorship required, and I'm based in Somerset, NJ, so NYC on-site full-time is straightforward — that's effectively my current commute pattern already."

**D) Feynman summary:** This is a logistics gate, not a values question — answer it like one: fast, factual, no hedging.

**E) Follow-ups:** none typically follow.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. How do you handle being one of a small group of technical execution quants supporting many PMs at once?

**A) Time budget:** 1.5–2 minutes.

**B) Follow-ups:** "Roughly how many PM groups would this seat be supporting concurrently, and is there existing tooling/self-serve dashboards, or would a lot of the load be bespoke requests?"

**C) Detailed answer:**
> "The lever is **leverage through tooling, not hours**. My approach: (1) build a shared, reusable TCA library — one codebase, parameterized by PM/desk/asset-class — rather than bespoke one-off scripts per request, so the marginal cost of a new PM's question drops over time; (2) triage requests into a few standard categories (routine cost review, algo comparison, ad-hoc anomaly investigation) with different SLAs, so urgent things get urgent attention and routine things get self-serve dashboards; (3) push a standard, digestible reporting cadence (e.g., monthly cost scorecards) so most PMs get their answer before they even have to ask. I did exactly this at BAM building execution monitoring across an entire multi-asset pod — the same pattern scales to many pods, it just needs more automation and less bespoke analysis per unit of coverage."

**D) Feynman summary:** You can't scale by working more hours per PM; you scale by turning each new PM's question into a parameter of a system you already built, not a new project.

**E) Follow-ups:**
- *"Give an example of a request you'd push back on or deprioritize."* → An ad-hoc "why did my one trade cost X" one-off with no pattern behind it gets a quick manual answer, not a new pipeline; a recurring pattern across multiple PMs gets the tooling investment.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. Tell me about a time you had to say no to a portfolio manager's request. How did you frame it?

**A) Time budget:** 2 minutes (STAR format).

**B) Follow-ups:** "Would you like a technical example (e.g., a request for a signal/analysis I judged not statistically sound) or a resourcing/prioritization example?"

**C) Detailed answer:**
> "**Situation:** At Highbridge, a PM wanted me to fast-track a signal into live trading off an in-sample backtest that looked exceptional, ahead of the normal validation cycle, because a competitor pod was reportedly running something similar.
> **Task:** Decide whether to comply or hold the line on our CPCV/out-of-sample validation standard.
> **Action:** I said no to the *timeline*, not the *idea* — I ran the standard combinatorial purged cross-validation in parallel on an accelerated but not skipped schedule, and I showed the PM the specific overfitting risk (parameter count relative to independent trials) that made the in-sample Sharpe unreliable.
> **Result:** The accelerated-but-rigorous validation surfaced a real degradation in the walk-forward Sharpe — protecting against what would have been a live drawdown. The PM's trust in my judgment increased, not decreased, because the 'no' came with a faster 'yes, but properly' alternative, not a flat refusal."

**D) Feynman summary:** Never say a bare "no" to a PM — say "not yet, and here's the fastest safe path to yes," backed by a concrete, quantifiable reason.

**E) Follow-ups:**
- *"What if the PM had overruled you?"* → Escalate to documented risk/compliance sign-off rather than silently comply — the record matters as much as the outcome.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. What is your timeline and are you interviewing elsewhere right now?

**A) Time budget:** 30–45 seconds.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> "I'm being thoughtful rather than rushed — I have a few early-stage conversations in parallel, but Millennium's Execution Services mandate is the closest match to where I want my next seat to be, so I'd prioritize moving efficiently through your process if there's mutual interest."

**D) Feynman summary:** Signal genuine interest and some optionality, without either bluffing urgency or sounding indifferent.

**E) Follow-ups:** *"What other types of roles are you looking at?"* → Similar execution/TCA-adjacent seats at other multi-manager platforms — consistent story, no contradiction.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. What questions do you have about the team structure or reporting line for this role?

**A) Time budget:** 1–1.5 minutes (2–3 good questions, not a list-dump).

**B) Follow-ups:** N/A — this *is* your follow-up turn.

**C) Detailed answer (questions to ask):**
> 1. "Who does this role report to day-to-day — the central desk head, or a dedicated quant-research lead within Execution Services?"
> 2. "How many PM groups / pods does the Execution Services quant team currently support, and is the team growing to keep pace?"
> 3. "Is the current tech stack primarily kdb+/q for tick data with a Python layer on top, or is there a migration underway I should know about?"

**D) Feynman summary:** Good closing questions do double duty — they gather real information *and* signal that you already understand the shape of the role (team scaling, tech stack) well enough to ask precise questions instead of generic ones.

**E) Follow-ups:** Typically none — this is the close of the segment; expect a handoff to the technical interviewer here.

[🔝 Back to Top](#-table-of-contents)

---
