# Set 3 — Transaction Cost Analysis (TCA) Deep Dive
**Total time budget: ~15 minutes** (this is the core technical/domain set — expect this to be a dedicated 15-min interview segment on its own, likely the highest-weighted set for this role).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Walk through how you would design a TCA framework for futures execution from scratch.**](#q1-walk-through-how-you-would-design-a-tca-framework-for-futures-execution-from-scratch)
- [§2 · **Q2. Explain implementation shortfall and how it decomposes into delay cost, market impact, and opportunity cost.**](#q2-explain-implementation-shortfall-and-how-it-decomposes-into-delay-cost-market-impact-and-opportunity-cost)
- [§3 · **Q3. How do VWAP, TWAP, and arrival-price benchmarks differ, and when would you use each for futures?**](#q3-how-do-vwap-twap-and-arrival-price-benchmarks-differ-and-when-would-you-use-each-for-futures)
- [§4 · **Q4. How would you adapt an equities TCA framework to account for futures-specific costs (roll cost, spread cost)?**](#q4-how-would-you-adapt-an-equities-tca-framework-to-account-for-futures-specific-costs-roll-cost-spread-cost)
- [§5 · **Q5. Describe how you would fit a market impact model using historical futures execution data.**](#q5-describe-how-you-would-fit-a-market-impact-model-using-historical-futures-execution-data)
- [§6 · **Q6. How would you attribute slippage across a portfolio manager group to identify a systemic cost driver?**](#q6-how-would-you-attribute-slippage-across-a-portfolio-manager-group-to-identify-a-systemic-cost-driver)
- [§7 · **Q7. What data quality issues typically arise in tick-level futures execution data, and how do you address them?**](#q7-what-data-quality-issues-typically-arise-in-tick-level-futures-execution-data-and-how-do-you-address-them)
- [§8 · **Q8. How would you present a TCA finding that shows a PM's execution cost is worse than peers, without being adversarial?**](#q8-how-would-you-present-a-tca-finding-that-shows-a-pms-execution-cost-is-worse-than-peers-without-being-adversarial)
- [§9 · **Q9. Explain Almgren-Chriss market impact modeling and its assumptions relative to futures liquidity.**](#q9-explain-almgren-chriss-market-impact-modeling-and-its-assumptions-relative-to-futures-liquidity)
- [§10 · **Q10. How do give-up fees, exchange fees, and clearing costs factor into total cost of trading?**](#q10-how-do-give-up-fees-exchange-fees-and-clearing-costs-factor-into-total-cost-of-trading)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Walk through how you would design a TCA framework for futures execution from scratch.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Should I assume we already have clean tick-level fills and market data, or should the design include the data pipeline itself?"

**C) Detailed answer:**
> "I'd design it in four layers:
> 1. **Data layer** — tick-level trades/quotes per venue (kdb+ splayed/partitioned by date), fills/order events from the OMS, reference data (contract specs, roll calendars, session times).
> 2. **Benchmark layer** — compute Arrival Price, Interval VWAP/TWAP, and Implementation Shortfall components for every parent order, joined via as-of joins (`aj`) against market data at decision time, execution time, and completion time.
> 3. **Attribution layer** — decompose costs into delay cost, market impact, spread cost, and opportunity cost (Q2); further split into components attributable to instrument (roll cost vs. outright), broker/algo, and PM/strategy.
> 4. **Reporting/statistics layer** — aggregate to PM/desk/asset-class level with confidence intervals (not just point estimates — see Set 6), surfaced via a dashboard and automated daily/monthly reports, with drill-down to the trade level for anomaly investigation.
> The design principle throughout: every number in the top-level dashboard must be traceable back to the raw fill it came from — no black-box aggregation — because the moment a PM disputes a number, you need to reconstruct it trade-by-trade in minutes, not days."

**D) Feynman summary:** A TCA framework is a pyramid: clean data at the base, benchmarks in the middle, attribution and statistics at the top — and the whole thing only works if you can walk back down the pyramid from any headline number to the exact trades that produced it.

**ASCII diagram:**
```
        +---------------------------+
        |   Reporting / Statistics  |   <- PM dashboards, CI's
        +---------------------------+
        |     Attribution Layer     |   <- delay/impact/spread/opp cost
        +---------------------------+
        |      Benchmark Layer      |   <- IS, VWAP, TWAP, Arrival
        +---------------------------+
        |         Data Layer        |   <- ticks, fills, ref data (kdb+)
        +---------------------------+
```

**E) Follow-ups:**
- *"What's the hardest layer to get right?"* → The data layer — garbage in/garbage out; most TCA disputes trace back to a bad as-of join or a missed corporate/roll event, not the math.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Explain implementation shortfall and how it decomposes into delay cost, market impact, and opportunity cost.

**A) Time budget:** 3 minutes (this is the single most important formula in the interview — write it out).

**B) Follow-ups:** "Do you want the classic Perold (1988) decomposition, or the version adapted for partial fills common in futures execution?"

**C) Detailed answer — write on mathcha:**
> Let $P_D$ = decision price (price when the order is created), $P_A$ = arrival price (price when the order actually reaches the market), $P_i$ = execution price of the $i$-th fill, $Q_i$ = quantity of the $i$-th fill, $X$ = total intended quantity, $X_{exec} = \sum_i Q_i$ = executed quantity, $P_{end}$ = price at the end of the trading horizon (cancellation/benchmark completion price).
>
> **Total Implementation Shortfall:**
> $$IS = X \cdot (P_{end} - P_D) - \sum_i Q_i \cdot (P_{end} - P_i)$$
>
> Rearranged into the classic decomposition (for a buy order, cost is positive when price rises against you):
>
> $$IS = \underbrace{X_{exec}\,(P_A - P_D)}_{\text{Delay Cost}} + \underbrace{\sum_i Q_i\,(P_i - P_A)}_{\text{Market Impact (Execution) Cost}} + \underbrace{(X - X_{exec})\,(P_{end} - P_A)}_{\text{Opportunity Cost}}$$
>
> - **Delay cost**: price drift between the *decision* to trade and the order *arriving* at the market — reflects latency/decision-to-desk friction, not the trader's execution skill.
> - **Market impact (execution) cost**: the cost of the fills themselves relative to arrival price — this is what most people mean by 'slippage' and is the piece attributable to the algo/broker/venue choice.
> - **Opportunity cost**: the cost of *not* filling the full intended size — if the order was never fully executed, the unfilled portion is penalized at the price it would have cost to complete at the end-of-horizon price. This captures the cost of being too passive and missing the market.
>
> The elegance of IS is that it's the *only* benchmark that penalizes both being too aggressive (impact) and too passive (opportunity cost) in the same unit — most other benchmarks (VWAP) only capture one side."

**D) Feynman summary:** Implementation shortfall answers one plain question: "compared to the price the moment you decided to trade, how much did the *entire process* of getting the trade done actually cost you — including the parts you delayed on, the parts you pushed the market with, and the parts you never even got done?" Delay cost is the tax on hesitation, impact cost is the tax on aggression, and opportunity cost is the tax on timidity — good execution minimizes the sum, not any single piece.

**E) Follow-ups:**
- *"Why not just use VWAP as the sole benchmark?"* → VWAP only measures cost relative to the day's volume-weighted price *during the trading window you chose* — it says nothing about the cost of *when* you decided to start trading, nor about unfilled size; it can be gamed by choosing a favorable window.
- *"How would you compute this from a fills DataFrame in Python?"* → Group fills by parent order id, join against timestamped decision/arrival prices via merge_asof, compute each term vectorized — happy to write that live (see Set 5/7).

[🔝 Back to Top](#-table-of-contents)

---

## Q3. How do VWAP, TWAP, and arrival-price benchmarks differ, and when would you use each for futures?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> $$VWAP = \frac{\sum_t V_t P_t}{\sum_t V_t}, \qquad TWAP = \frac{1}{N}\sum_{t=1}^{N} P_t$$
> where $V_t, P_t$ are traded volume and price in interval $t$.
>
> - **VWAP** weights by volume — appropriate when the goal is to participate proportionally with the market and minimize footprint; standard for large orders where impact is the primary concern, and it's the natural benchmark in liquid front-month futures where intraday volume profiles are stable and predictable.
> - **TWAP** weights every time interval equally — appropriate when you want to avoid concentrating in any particular volume spike (e.g., avoid chasing a news-driven volume burst) or when volume data is unreliable/thin (e.g., a back-month or spread contract), since it doesn't require a volume profile to target.
> - **Arrival Price** benchmarks against the price at order inception, capturing the *full* cost including delay and opportunity cost (Q2) — the right benchmark whenever you care about total cost of the *decision*, not just execution style, which is why it underlies Implementation Shortfall rather than VWAP/TWAP alone.
> For futures specifically: VWAP dominates for liquid, high-volume front-month contracts with reliable intraday volume curves; Arrival Price dominates for less liquid contracts (back months, spreads) or urgent/informed orders where minimizing total shortfall matters more than tracking a volume curve.

**D) Feynman summary:** VWAP asks "did I trade at a fair average price relative to everyone else's activity?" TWAP asks the same question but pretends every minute of the day matters equally regardless of how busy it was. Arrival Price asks a harder, more honest question: "compared to the moment I decided to act, what did the whole journey cost me?"

**E) Follow-ups:**
- *"Can VWAP be gamed?"* → Yes — a trader can concentrate fills near a self-created volume spike to move the realized VWAP benchmark itself, which is why serious frameworks pair VWAP with Arrival Price and impact-vs-reversion checks.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. How would you adapt an equities TCA framework to account for futures-specific costs (roll cost, spread cost)?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Should the adaptation assume roll trades are tagged/flagged separately in the OMS already, or do we need to infer them from trade patterns?"

**C) Detailed answer:**
> "Three specific adaptations beyond a standard equities IS/VWAP framework:
> 1. **Separate the roll-cost bucket** — tag calendar-spread/roll trades distinctly in the data model so their cost is measured against a *spread* benchmark (VWAP of the calendar spread, per Set 2 Q6) rather than contaminating the outright-instrument's slippage number — otherwise a PM's 'execution quality' looks artificially worse every roll quarter for structural, not skill-related, reasons.
> 2. **Multiplier/tick normalization** — futures P&L and cost need to be normalized by contract multiplier and tick value to compare cost in consistent bps-of-notional terms across contracts with very different dollar-per-tick values (e.g., ES vs. ZN vs. CL) — an equities framework, where $1 share = 1 share, doesn't need this step.
> 3. **Session/liquidity-curve awareness** — futures trade near-24-hour in some products with very different liquidity profiles (Asia/London/NY sessions) — the volume-profile assumptions baked into an equities VWAP benchmark (single regular trading session) need to be replaced with a futures-specific, session-aware intraday volume curve, or VWAP benchmarks become meaningless outside the primary session."

**D) Feynman summary:** Equities TCA assumes one instrument, one trading session, one unit of measure — futures breaks all three assumptions (rolling instruments, near-continuous sessions, wildly different contract economics), so the framework has to explicitly carry instrument identity, session, and normalization as first-class dimensions rather than assuming they're constant.

**E) Follow-ups:**
- *"How do you normalize across contracts with different tick values?"* → Convert cost to basis points of traded notional: $\text{cost}_{bps} = \dfrac{\text{cost in ticks} \times \text{tick value}}{\text{notional value}} \times 10^4$.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. Describe how you would fit a market impact model using historical futures execution data.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Are you looking for the Almgren-Chriss square-root-law style specification, or an empirical/ML-driven fit without assuming a functional form first?"

**C) Detailed answer:**
> "I'd start with a theory-motivated functional form and let the data confirm/adjust it, rather than pure black-box ML, because interpretability matters for a cost model PMs need to trust.
>
> **Specification** (square-root law, widely validated empirically):
> $$\text{Impact}_{bps} = \eta \cdot \sigma \cdot \left(\frac{Q}{ADV}\right)^{\beta}$$
> where $Q$ = order size, $ADV$ = average daily volume, $\sigma$ = daily volatility, $\eta$ = a calibrated impact coefficient, and $\beta \approx 0.5$ is the empirically common 'square-root' exponent (vs. a naive linear-in-size model).
>
> **Fitting procedure:**
> 1. Build a panel of historical parent orders with observed size, participation rate, realized volatility at execution time, and realized impact (execution price vs. arrival price, in bps).
> 2. Take logs: $\ln(\text{Impact}) = \ln(\eta) + \ln(\sigma) + \beta \ln(Q/ADV)$ — turns it into a linear regression in $\beta$ and $\ln \eta$.
> 3. Fit via OLS with Newey-West-corrected standard errors (heteroskedasticity/autocorrelation in trading-cost data — Set 6 Q2), or robust regression if outliers (bad fills, data errors) are present.
> 4. Validate out-of-sample by holding out a recent time window (not random rows — trading cost data is time-series, so a random split leaks information) and checking prediction error on genuinely future orders.
> 5. Add temporary-vs-permanent impact decomposition (Set 6 Q5) if the horizon of interest requires it."

**D) Feynman summary:** The square-root law captures a simple physical intuition: pushing more size into the market costs more, but with diminishing marginal pain per unit size — like trying to walk faster through a crowd; the first extra bit of speed is easy, but each additional bit costs disproportionately more effort as you push further into the crowd's resistance.

**E) Follow-ups:**
- *"Why square-root and not linear?"* → Empirically robust across asset classes/venues (Almgren, Kissell, and many market-impact studies); linear models systematically under-predict cost for large orders and over-predict for small ones.
- *"How do you handle multicollinearity between size, ADV, and volatility?"* → Set 6 Q4.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. How would you attribute slippage across a portfolio manager group to identify a systemic cost driver?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Is the driver hypothesis something specific you have in mind (e.g., broker choice, order timing, urgency), or fully exploratory?"

**C) Detailed answer:**
> "I'd treat this as a variance-decomposition/regression-attribution problem:
> 1. Build a panel of all trades for the PM group with candidate explanatory factors: order size (relative to ADV), urgency/algo type, broker, time-of-day, instrument, volatility regime.
> 
> 2. Regress realized slippage (bps) on these factors:
> 
> $$\text{slippage}_i = \beta_0 + \beta_1 \text{size}_i + \beta_2 \text{urgency}_i + \gamma_{\text{broker}} + \gamma_{\text{time-of-day}} + \varepsilon_i$$
> 
> , using fixed effects for categorical drivers (broker, time bucket).
> 
> 3. Check which coefficient is both statistically significant *and* economically large (not just significant given large N) — e.g., if one broker's fixed effect is a persistent +3bps versus peers after controlling for size/urgency, that's the systemic driver, not noise.
> 
> 4. Cross-validate the finding by checking if it holds across sub-periods (not a one-off event) and across other PM groups using the same broker (isolating broker-attributable vs. PM-attributable cost)."

**D) Feynman summary:** You're doing the same thing a doctor does isolating what's making a patient sick when there are multiple plausible causes — control for the known, benign explanations (size, urgency) statistically, and see what's left over that's systematically, not randomly, elevated.

**E) Follow-ups:**
- *"What if broker and time-of-day are correlated (e.g., one broker only trades in Asia session)?"* → Exactly the multicollinearity issue from Set 6 Q4 — need interaction terms or a design that separates them, e.g., comparing the same broker across sessions if enough data exists.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. What data quality issues typically arise in tick-level futures execution data, and how do you address them?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Are we talking issues from the exchange feed itself, or issues introduced by our own OMS/fill reporting?"

**C) Detailed answer:**
> "Common issues and fixes:
> - **Out-of-order/late ticks** — network/feed handler delays cause ticks to arrive after later ones; fix by sorting/re-sequencing on exchange timestamp, not receipt timestamp, and flagging large sequence gaps.
> - **Duplicate trades/quotes** — retransmissions from the feed; dedupe on (exchange sequence number, timestamp, price, size) composite key.
> - **Crossed/locked quotes** — bid ≥ ask momentarily during fast markets; filter or flag rather than silently including in spread calculations, since they distort spread-cost attribution.
> - **Roll/contract-continuity breaks** — naive continuous-contract construction creates artificial price jumps at roll dates; use back-adjusted or ratio-adjusted continuous series, and never compute simple returns across a raw roll boundary.
> - **Corporate/session-boundary artifacts** — settlement/opening-auction prints that aren't representative of continuous trading; exclude or flag from intraday VWAP calculations unless the benchmark specifically requires the settlement print (e.g., TAS).
> - **Fill-report reconciliation gaps** — the OMS fill record and the exchange trade record may reconcile imperfectly (different timestamp conventions, partial-fill aggregation); build automated daily reconciliation with a tolerance/exception report rather than trusting either source blindly."

**D) Feynman summary:** Tick data is never actually clean — it's a recording of a chaotic, distributed system (multiple feed handlers, multiple systems, network jitter) trying to describe one 'true' sequence of events; the job of the data layer is to reconstruct the most likely true sequence and flag everywhere it can't be sure, rather than pretending certainty it doesn't have.

**E) Follow-ups:**
- *"How would you flag these programmatically at scale in kdb+?"* → Set 4 Q9 — asynchronous/out-of-order handling in kdb+ ingestion.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. How would you present a TCA finding that shows a PM's execution cost is worse than peers, without being adversarial?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Do you want the communication framing, or also the statistical rigor needed before you'd even bring this to a PM?" (Signals you know not to present unvalidated findings — ties to Set 6/8.)

**C) Detailed answer:**
> "First, I wouldn't bring it forward until I'd validated it's statistically meaningful (adequate sample size, controlled for size/urgency/instrument mix — Q6) — nothing damages trust faster than an unvalidated 'you're bad at this' claim that later gets debunked.
> Once validated, I'd frame it as **diagnostic, not evaluative**: lead with the decomposition (is it delay cost, impact cost, or opportunity cost driving the gap?), because that reframes the conversation from 'your trading is worse' to 'here's specifically where the cost is coming from, and here are 2-3 concrete, actionable levers' (e.g., broker/algo switch, order-timing adjustment, or size-slicing change). I'd also show the peer comparison with confidence intervals, not a single point estimate, so the PM sees the honest uncertainty rather than a false precision. The goal is to leave the PM with an action, not a grade."

**D) Feynman summary:** Nobody wants to hear "you're underperforming" — everybody wants to hear "here's specifically what's costing you money and here's the lever to pull," even if it's the same underlying fact; good TCA communication is a diagnosis with a prescription, not a report card.

**E) Follow-ups:**
- *"What if the PM disputes the peer comparison methodology?"* → Show the full attribution audit trail (Q1) down to the trade level — this is exactly why the framework must be fully traceable.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. Explain Almgren-Chriss market impact modeling and its assumptions relative to futures liquidity.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Full derivation of the optimal execution trajectory, or just the model structure and its assumption caveats for futures?"

**C) Detailed answer — write on mathcha:**
> "The Almgren-Chriss (2000) framework minimizes expected execution cost plus a risk penalty for price uncertainty during execution. For a position of size $X$ liquidated over $[0,T]$ with holdings $x_k$ remaining at time $k$:
>
> **Total cost objective:**
> $$\min_{\{x_k\}} \mathbb{E}[C] + \lambda \text{Var}[C]$$
> where cost $C$ has a **temporary impact** component (cost of trading *this* slice, reverts after) and a **permanent impact** component (moves the price for *all remaining* slices):
> $$\text{Temporary impact}: h(v_k) = \epsilon \cdot \text{sign}(v_k) + \eta v_k \quad (\text{linear in trade rate } v_k)$$
> $$\text{Permanent impact}: g(v_k) = \gamma v_k$$
>
> The optimal trading trajectory (risk-neutral case, $\lambda=0$) is simply linear/TWAP-like; with risk-aversion $\lambda > 0$, the solution is a **hyperbolic-sine trajectory** — trade faster early to reduce exposure to price variance, front-loading execution as risk aversion increases.
>
> **Assumptions and their futures caveats:**
> - Assumes constant volatility and linear impact — futures liquidity/volatility is often regime-dependent (session effects, event-driven vol spikes around economic releases) so a static calibration under-reacts to regime shifts (Set 6 Q6).
> - Assumes a single, static liquidity pool — doesn't natively capture the calendar-spread/implied-liquidity dynamics unique to futures (Set 2 Q8), so a futures adaptation needs to model impact jointly across the roll-relevant contracts, not one instrument in isolation.
> - Assumes impact parameters are stable — in practice $\eta, \gamma$ need periodic recalibration, especially around contract roll dates when liquidity characteristics shift discretely."

**D) Feynman summary:** Almgren-Chriss is the answer to "if I need to sell a big pile of something over the next few hours, how fast should I do it?" — go too fast and you crash the price into your own remaining shares (impact cost); go too slow and you're exposed to the market drifting against you the whole time (risk cost) — the model finds the speed that balances those two, but its assumption of one calm, constant liquidity pool needs adjusting for futures' choppier, roll-driven reality.

**E) Follow-ups:**
- *"What's the closed-form optimal trajectory?"* → $x_k = X \dfrac{\sinh(\kappa(T-t_k))}{\sinh(\kappa T)}$ where $\kappa$ is derived from $\lambda, \sigma, \eta$ — front-loaded for $\lambda>0$, linear as $\lambda \to 0$.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. How do give-up fees, exchange fees, and clearing costs factor into total cost of trading?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed — direct factual question.

**C) Detailed answer:**
> "These are the **explicit** cost components, distinct from the **implicit** costs (impact, spread, delay) covered above — a complete TCA framework must report both, since PMs (and regulators, for best-execution purposes) need total cost, not just market-impact cost.
> - **Exchange fees**: per-contract transaction fees charged by CME/ICE, often with maker/taker rebate structures similar to equities — directly deductible from cost, fully deterministic given the fee schedule.
> - **Give-up fees**: negotiated per-contract fee paid to the EB (Set 2 Q5) — fixed and known in advance, but easy to forget in a naive TCA model that only looks at price data.
> - **Clearing costs**: CB clearing fees plus the *opportunity cost of margin* — capital tied up as IM/VM has a funding cost that should, in a rigorous total-cost-of-ownership framework, be reflected (e.g., annualized funding rate × average margin posted).
> A complete cost waterfall: $\text{Total Cost} = \text{Market Impact} + \text{Spread Cost} + \text{Delay/Opportunity Cost} + \text{Exchange Fees} + \text{Give-up Fees} + \text{Clearing/Funding Cost}$ — reporting only the first few and ignoring the last three understates true cost, especially for high-turnover strategies where explicit fees can rival implicit costs."

**D) Feynman summary:** Implicit costs (impact, spread) are the 'invisible' costs baked into the price you got; explicit costs (fees) are the visible line-items on the invoice — a PM who only looks at slippage and ignores fees is reading half the bill.

**E) Follow-ups:**
- *"Which typically dominates for a high-turnover futures strategy?"* → Depends on liquidity — for very liquid front-month contracts (e.g., ES), explicit fees can be a meaningful fraction of total cost; for less liquid/back-month trading, implicit impact cost usually dominates.

[🔝 Back to Top](#-table-of-contents)

---
