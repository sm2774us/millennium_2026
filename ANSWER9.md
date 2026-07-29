# Set 9 — Cross-Asset & Desk-Specific Depth
**Total time budget: ~15 minutes** (synthesis set — draws on Sets 2, 3, and 7; keep answers tightly cross-referenced rather than re-deriving from scratch).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. How would you adapt your futures TCA framework for cross-asset use across equities and FX?**](#q1-how-would-you-adapt-your-futures-tca-framework-for-cross-asset-use-across-equities-and-fx)
- [§2 · **Q2. Compare execution quality considerations for futures vs FX vs equities — what changes in your model?**](#q2-compare-execution-quality-considerations-for-futures-vs-fx-vs-equities--what-changes-in-your-model)
- [§3 · **Q3. How does queue position and passive order placement differ across futures exchanges vs equities venues?**](#q3-how-does-queue-position-and-passive-order-placement-differ-across-futures-exchanges-vs-equities-venues)
- [§4 · **Q4. Explain how you'd evaluate a broker's execution algorithm performance across different futures products.**](#q4-explain-how-youd-evaluate-a-brokers-execution-algorithm-performance-across-different-futures-products)
- [§5 · **Q5. What role does market commentary/content play in supporting PMs, and how would you contribute to it?**](#q5-what-role-does-market-commentarycontent-play-in-supporting-pms-and-how-would-you-contribute-to-it)
- [§6 · **Q6. How would you assess trading cost differences between electronic execution and voice/manual execution in futures?**](#q6-how-would-you-assess-trading-cost-differences-between-electronic-execution-and-voicemanual-execution-in-futures)
- [§7 · **Q7. Describe your understanding of how central trading desks interact with volatility/vol-risk businesses.**](#q7-describe-your-understanding-of-how-central-trading-desks-interact-with-volatilityvol-risk-businesses)
- [§8 · **Q8. How would you design a process to systematically assess trading costs across multiple PM groups?**](#q8-how-would-you-design-a-process-to-systematically-assess-trading-costs-across-multiple-pm-groups)
- [§9 · **Q9. What experience do you have advising on execution algorithms, market structure, and slippage across asset classes?**](#q9-what-experience-do-you-have-advising-on-execution-algorithms-market-structure-and-slippage-across-asset-classes)
- [§10 · **Q10. How would you keep a cross-asset TCA framework maintainable as new products/venues are added?**](#q10-how-would-you-keep-a-cross-asset-tca-framework-maintainable-as-new-productsvenues-are-added)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. How would you adapt your futures TCA framework for cross-asset use across equities and FX?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Cash equities specifically, or including equity derivatives/futures too?"

**C) Detailed answer:**
> "Building on the layered design (Set 3 Q1) and the core/extension schema (Set 7 Q9): the **benchmark layer** (IS, VWAP, TWAP) is genuinely asset-class-agnostic — the formulas don't change. What has to adapt is the **data layer's normalization** (Set 3 Q4) and the **attribution layer's asset-class-specific cost buckets**: futures need roll-cost separation, FX needs settlement/netting-convention awareness (Set 2 Q10) and a benchmark against dealer cover-price rather than a lit-book VWAP for RFQ-executed FX (Set 2 Q4), equities need corporate-action adjustment (splits/dividends) analogous to futures roll-adjustment. I'd implement this as a common `CostAttribution` interface with asset-class-specific adapter classes implementing the same `Protocol` (Set 5 Q4/Q6 pattern) — so the reporting layer consumes a uniform output regardless of which asset class produced it."

**D) Feynman summary:** The math of "how much did this cost me compared to when I decided to trade" doesn't care what you're trading — what differs across asset classes is *what other structural costs need to be separated out first* (rolls for futures, settlement conventions for FX, corporate actions for equities) before that clean comparison is fair.

**E) Follow-ups:**
- *"What's the hardest part of this generalization?"* → FX, because the lack of a consolidated tape/lit order book (Set 2 Q4) means the benchmark itself (arrival "price") is less unambiguous than in exchange-traded futures/equities.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Compare execution quality considerations for futures vs FX vs equities — what changes in your model?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — direct comparative question.

**C) Detailed answer:**
> "Three axes of difference: (1) **price discovery mechanism** — futures/equities are lit CLOBs, FX is largely RFQ/dealer-based (Set 2 Q4) — changing what 'good execution' even means (spread-to-mid capture vs. dealer-panel competitiveness); (2) **fragmentation** — equities are fragmented across many competing lit/dark venues (Reg NMS), futures are concentrated on one primary exchange per product (Set 2 Q8), FX liquidity is fragmented across many bank/ECN venues with no single consolidated view — my model's venue-normalization logic needs a genuinely different fragmentation assumption per asset class; (3) **explicit cost structure** — futures carry give-up/clearing/margin-funding costs (Set 3 Q10) that equities/FX largely don't have in the same form (equities have custodian/DVP costs, FX has settlement/CLS-related costs instead) — the explicit-cost waterfall needs asset-class-specific line items even though the implicit-cost math (impact, spread, delay) stays structurally similar."

**D) Feynman summary:** The invisible costs (impact, delay, spread) are computed the same way everywhere; the visible costs (fees, funding, settlement) and the market structure generating the prices in the first place are genuinely different per asset class — a good cross-asset model keeps those two halves cleanly separated.

**E) Follow-ups:**
- *"Which asset class's execution quality is hardest to benchmark and why?"* → FX, for the reasons in Q1 — no single authoritative "market price" reference the way a lit CLOB provides.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. How does queue position and passive order placement differ across futures exchanges vs equities venues?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed — extends Set 2 Q8.

**C) Detailed answer:**
> "Both use strict price-time priority within a price level, but the practical dynamics differ: futures queues at the best price in a liquid front-month contract can be extremely deep and dominated by high-frequency market-making activity, meaning genuine queue position for a slower participant is often poor unless orders are placed well ahead of need; equities queue dynamics vary enormously by venue — lit exchanges follow price-time priority, but the existence of multiple competing lit venues plus dark pools means a passive equities strategy is also making a **venue-selection** decision (where to rest the order), not just a queue-position decision, which futures largely avoids given single-venue concentration (Set 2 Q8). Also, futures order books commonly include **implied liquidity** synthesized from spread markets (Set 2 Q8) — a passive futures order can effectively gain queue depth/priority interactions with implied orders that have no equities analogue."

**D) Feynman summary:** In futures you're mostly deciding *when* to join one queue at one address; in equities you're deciding *which of several addresses* to send your order to *and* when — futures passive execution is a timing problem, equities passive execution is a timing-and-routing problem.

**E) Follow-ups:**
- *"How would queue-position analysis feed into your TCA framework?"* → As a component of the impact-cost attribution (Set 3 Q6) — orders that rested passively but didn't fill (contributing to opportunity cost, Set 3 Q2) can be diagnosed via queue-position analysis to see if placement, not market conditions, was the driver.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Explain how you'd evaluate a broker's execution algorithm performance across different futures products.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — extends Set 2 Q7 to explicitly cross-product comparison.

**C) Detailed answer:**
> "Building on the scorecard from Set 2 Q7, the key addition for *cross-product* comparison is **normalization before comparison** — raw bps slippage isn't comparable across products with very different liquidity/volatility profiles (e.g., a highly liquid index future vs. a thinner agricultural future). I'd normalize using the market-impact model itself (Set 3 Q5/Q9): compute each execution's slippage *relative to the product-specific expected impact* given its size/ADV/volatility, i.e., a **standardized residual** $z_i = (\text{realized impact}_i - \text{predicted impact}_i)/\hat\sigma_i$, then compare the broker's average $z$ across products — this answers 'is this broker better or worse than the product's own expected cost curve,' which is comparable across products in a way raw bps is not."

**D) Feynman summary:** You can't fairly compare a broker's performance on a thin agricultural contract to their performance on a deep index future using raw cost numbers — you first have to ask "how much cost would we expect here anyway," and only then compare how much better or worse than expected the broker actually did.

**E) Follow-ups:**
- *"What if the impact model itself is miscalibrated for a thin product with little data?"* → Flag low-confidence normalization for thin products explicitly (wide CI, per Set 6 Q7) rather than presenting an equally-confident-looking $z$-score regardless of underlying data sufficiency.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. What role does market commentary/content play in supporting PMs, and how would you contribute to it?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** "Is this more about macro/market-structure commentary, or specifically execution-cost/liquidity-conditions commentary tied to the desk's mandate?"

**C) Detailed answer:**
> "Given this role's mandate, I'd expect the relevant commentary to be **liquidity/market-structure-conditions content** — e.g., a periodic note flagging upcoming roll windows and expected liquidity migration (Set 2 Q6), notable volatility-regime shifts affecting expected execution cost (Set 6 Q6), or venue/market-structure changes (new order types, position-limit changes) relevant to execution strategy. My contribution would be turning the quantitative monitoring I already do into a proactive, forward-looking note rather than only reactive reporting — e.g., 'the March roll window for [product] historically shows liquidity migrating by day X; plan large rolls accordingly' — content that helps PMs anticipate cost drivers rather than just explaining them after the fact."

**D) Feynman summary:** Market commentary at its best is TCA turned forward-looking — instead of only explaining what already happened to your costs, use the same data to tell PMs what's likely to happen to costs in the coming days/weeks so they can plan around it.

**E) Follow-ups:**
- *"How often would you produce this?"* → Event-driven (ahead of major roll windows, known volatility events like FOMC) rather than a fixed calendar cadence, since the value is timeliness relative to an actual upcoming decision point.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. How would you assess trading cost differences between electronic execution and voice/manual execution in futures?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Are voice trades typically large blocks/less-liquid products by nature here, or is there genuine overlap in order profile with electronic execution to compare against?" (Important — naive comparison is usually confounded.)

**C) Detailed answer:**
> "This comparison is almost always **confounded by selection** — voice execution is typically used precisely for large, illiquid, or complex (e.g., multi-leg spread) orders where electronic execution would incur worse impact, so a naive average-cost comparison will make voice look artificially favorable or unfavorable depending on which side of that selection dominates. I'd handle it the same way as the algo-comparison problem (Set 6 Q1) — control for order characteristics (size relative to ADV, product liquidity, complexity) in a regression before comparing the voice/electronic effect, or better, look for cases where the *same* type of order was executed via both channels in different instances (a natural quasi-experiment) to get a cleaner read. I'd also account for costs that don't show up in price-based slippage at all — voice execution often carries different information-leakage risk (broker sales-trader awareness of the order) that a pure price-based TCA metric doesn't directly capture but that's worth flagging qualitatively."

**D) Feynman summary:** Comparing voice and electronic execution costs naively is like comparing surgery outcomes between two hospitals without asking whether one hospital simply takes harder cases — you have to control for the fact that voice trades are usually already the harder orders before concluding anything about voice execution quality itself.

**E) Follow-ups:**
- *"What's an example of a 'natural quasi-experiment' here?"* → A period where a normally-voice-executed order type became electronically tradable (e.g., a new algo/venue capability launching) — comparing cost before/after that capability change, for similar order profiles, isolates the channel effect more cleanly than a cross-sectional comparison.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. Describe your understanding of how central trading desks interact with volatility/vol-risk businesses.

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** "Are you asking about execution of options/vol-instrument hedges specifically, or the desk's role in managing the platform's aggregate vol exposure more broadly?"

**C) Detailed answer:**
> "My understanding: a central execution desk typically supports vol-focused pods by executing the underlying/futures legs of options-related strategies (e.g., delta-hedging flows) efficiently, since vol books often need to trade the underlying futures frequently and mechanically as part of maintaining a hedge — meaning execution quality for that flow has a direct, measurable effect on the realized cost of running a vol strategy (hedging slippage erodes the edge a vol signal is trying to capture, similar to how execution cost erodes any signal's Sharpe, per the Set 1 Q1 framing). From an Execution Services quant's side, I'd expect this to mean building TCA specifically tuned to systematic, high-frequency delta-hedging flow — which looks different from typical directional order flow (much more frequent, smaller, mechanically triggered rather than PM-discretionary) and needs its own benchmark/attribution treatment rather than being lumped into general order-flow TCA."

**D) Feynman summary:** A vol book's hedging trades are mechanical and frequent rather than occasional and discretionary — execution cost analysis for that flow needs to be built around that different rhythm, since the standard "per-parent-order" TCA framework assumes a more occasional, larger-order pattern.

**E) Follow-ups:**
- *"Would you build a separate TCA pipeline for delta-hedging flow, or extend the existing one?"* → Extend the existing layered framework (Set 3 Q1) with a specialized benchmark/attribution module for high-frequency mechanical flow, rather than a fully separate pipeline — keeps data/reporting infrastructure shared while allowing methodology to differ where it genuinely needs to.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. How would you design a process to systematically assess trading costs across multiple PM groups?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — synthesizes Set 3 Q1/Q6 and Set 1 Q7 at process level.

**C) Detailed answer:**
> "A recurring, standardized process with three tiers: (1) **automated daily pipeline** (Set 7 Q7) producing per-PM-group scorecards with statistically-validated flags (Set 6), requiring no manual analyst time under normal conditions; (2) **periodic (monthly/quarterly) cross-PM review** — the systemic-driver attribution analysis (Set 3 Q6) run across the full PM population to catch patterns that only show up in aggregate (e.g., a broker/algo issue affecting several PMs slightly, invisible in any single PM's report); (3) **event-triggered deep dives** — the anomaly-detection layer (Set 6 Q6) escalating specific PM/period combinations for manual investigation when statistically flagged. The systematic part is ensuring tiers 1 and 2 run automatically and consistently regardless of which PMs happen to be squeaky wheels that quarter — so quiet PM groups get the same rigor of assessment as vocal ones, not just whoever asks the most questions."

**D) Feynman summary:** Systematic assessment means the quiet PM who never asks a question gets exactly as much scrutiny and support as the PM who emails every week — that only happens if the core assessment is automated and applied uniformly, with manual attention reserved for genuinely flagged issues, not for whoever's loudest.

**E) Follow-ups:**
- *"How do you avoid this process becoming just noise/alert fatigue?"* → Statistical significance and economic-magnitude thresholds (Set 6 Q1/Q7) gating what actually escalates to a human, consistent with the "don't alert on noise" discipline from Set 6 Q6.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. What experience do you have advising on execution algorithms, market structure, and slippage across asset classes?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — direct resume-tie-in question, answer from experience.

**C) Detailed answer:**
> "Across my three most recent roles I've built and used exactly this kind of cross-asset execution judgment: at Millburn, signal deployment decisions were inseparable from execution feasibility across global macro futures, equities, and liquid FX pairs — a signal that looked good gross of costs but wasn't executable at scale wasn't a real signal. At Highbridge, I explicitly measured alpha quality using Implementation Shortfall against VWAP/Arrival Price benchmarks across futures, FX, and equities market microstructure, and built adverse-selection models identifying informed flow — directly relevant to understanding *why* slippage happens, not just measuring it. At BAM currently, I lead execution monitoring and transaction cost modeling across the pod's full multi-asset book (equities, futures, FX, derivatives). The throughline across all three: I've never treated execution cost as someone else's problem to hand off — it's been a first-class input to every research and portfolio decision I've made, across every asset class I've traded."

**D) Feynman summary:** I haven't been an alpha researcher who occasionally checks in on execution — execution cost has been baked into how I evaluate whether any signal is real, across every asset class, for the whole of my career; this role just makes that the explicit job title instead of an implicit discipline.

**E) Follow-ups:**
- *"Which asset class do you feel least expert in, execution-wise?"* → Be honest — likely FX market-structure specifics (RFQ dealer-panel dynamics, Set 2 Q4) relative to futures/equities, where lit-book mechanics are closer to my daily practice; frame as an area I'd ramp quickly given the adjacent skill set, not a gap I'm unaware of.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. How would you keep a cross-asset TCA framework maintainable as new products/venues are added?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — synthesizes Set 5 Q4 and Set 7 Q9's extensibility design.

**C) Detailed answer:**
> "The maintainability discipline is the same architectural pattern used throughout my design choices in this interview: (1) **stable core, extensible edges** — the benchmark/attribution core (Set 3 Q1, Set 7 Q9) stays asset-class-agnostic; a new product/venue only requires a new data-adapter implementing the shared `Protocol` interface (Set 5 Q4/Q6), not changes to core logic; (2) **configuration over code** — instrument reference data, roll calendars, session times, fee schedules live in configuration/reference tables (Set 7 Q9's `instrument_ref`), not hardcoded — a new product is a new row, not a new code path; (3) **regression-test gate on every addition** — adding a new asset class/venue must pass the existing test suite (Set 7 Q10) unchanged plus new tests for the addition, ensuring the core framework's behavior for existing products never silently drifts; (4) **versioned, documented rationale** (Set 8 Q10) for every asset-class-specific adaptation, so five years from now someone can understand why FX's benchmark construction differs from futures' without reverse-engineering it from code alone."

**D) Feynman summary:** A maintainable cross-asset framework is really a small, stable core surrounded by a growing ring of well-documented, independently-testable adapters — adding a new product should feel like plugging in a new adapter, never like performing surgery on the core.

**E) Follow-ups:**
- *"What's the biggest maintainability risk you'd watch for as the framework grows?"* → Asset-class-specific logic gradually leaking into the supposedly-generic core (e.g., a futures-specific roll-adjustment quietly hardcoded into a benchmark function) — the discipline of catching this is exactly the code-review/architecture-review habit of asking "would this line make sense for an asset class that doesn't exist yet?"

[🔝 Back to Top](#-table-of-contents)

---
