# Set 2 — Futures Market Structure & Mechanics
**Total time budget: ~10–12 minutes** (this set is conceptual/verbal, low math load — keep each answer tight so it doesn't crowd out Sets 3–7).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Explain the difference between an outright futures contract, a calendar spread, TAS, and BTIC.**](#q1-explain-the-difference-between-an-outright-futures-contract-a-calendar-spread-tas-and-btic)
- [§2 · **Q2. Walk through the lifecycle of a futures trade from execution through clearing and give-up.**](#q2-walk-through-the-lifecycle-of-a-futures-trade-from-execution-through-clearing-and-give-up)
- [§3 · **Q3. What are margin requirements and position limits, and how do they constrain execution strategy?**](#q3-what-are-margin-requirements-and-position-limits-and-how-do-they-constrain-execution-strategy)
- [§4 · **Q4. How does execution differ between electronic futures markets (CME, ICE) and OTC-cleared products?**](#q4-how-does-execution-differ-between-electronic-futures-markets-cme-ice-and-otc-cleared-products)
- [§5 · **Q5. Explain how give-up agreements work between executing brokers and clearing brokers.**](#q5-explain-how-give-up-agreements-work-between-executing-brokers-and-clearing-brokers)
- [§6 · **Q6. What operational considerations arise when rolling a futures position near contract expiry?**](#q6-what-operational-considerations-arise-when-rolling-a-futures-position-near-contract-expiry)
- [§7 · **Q7. How would you evaluate a futures broker or execution algorithm's quality across venues?**](#q7-how-would-you-evaluate-a-futures-broker-or-execution-algorithms-quality-across-venues)
- [§8 · **Q8. Describe how the futures order book differs structurally from an equities limit order book.**](#q8-describe-how-the-futures-order-book-differs-structurally-from-an-equities-limit-order-book)
- [§9 · **Q9. What is basis risk in a futures roll, and how do you monitor it operationally?**](#q9-what-is-basis-risk-in-a-futures-roll-and-how-do-you-monitor-it-operationally)
- [§10 · **Q10. How do give-ups and booking conventions differ across asset classes (futures vs FX vs equities)?**](#q10-how-do-give-ups-and-booking-conventions-differ-across-asset-classes-futures-vs-fx-vs-equities)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Explain the difference between an outright futures contract, a calendar spread, TAS, and BTIC.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Should I frame this around CME financial futures (e.g., ES, rates) or commodities, given my macro-futures background is broadest there?"

**C) Detailed answer:**
> "Four related but distinct instruments:
> - **Outright** — a single futures contract for one expiry, e.g., long the December ES contract. Price risk is pure directional exposure to that expiry's forward price.
> - **Calendar spread** — simultaneously long one expiry and short another (e.g., long Dec, short Mar) in the same underlying. You're trading the *relative* price between expiries — driven by cost-of-carry, dividends/financing for equity index futures, or supply/demand curve shape for commodities — not the outright level.
> - **TAS (Trade at Settlement)** — an order type letting you trade *during* the day at a price fixed to (settlement price ± ticks), executed at prevailing spread, but priced off the eventual close. Useful for benchmarked strategies (e.g., an index-tracking mandate that must match the settle) without taking on end-of-day auction risk.
> - **BTIC (Basis Trade at Index Close)** — similar concept but references an *index* close rather than the futures settlement — used to trade the futures basis (futures price vs. cash index) at the close, common in equity index futures rolls.
>
> The unifying theme: outright and calendar spread are about *what* you're exposed to (level vs. relative), while TAS/BTIC are about *when/how* the price is fixed (intraday execution, settlement-referenced pricing)."

**D) Feynman summary:** Outright = "I want exposure to a date." Calendar spread = "I want exposure to the *shape* of the curve between two dates." TAS/BTIC = "I want to trade now but be priced as if I traded at the close" — they decouple execution time from pricing time.

**ASCII diagram — instrument taxonomy:**
```
                     Futures-Related Order Types
                     ----------------------------
        +-------------------+       +----------------------+
        |     OUTRIGHT       |       |   CALENDAR SPREAD     |
        |  (single expiry)   |       | (long exp A/short B)  |
        +-------------------+       +----------------------+
                 |                             |
                 v                             v
          Directional level              Curve-shape / carry
          exposure                       exposure

        +-------------------+       +----------------------+
        |        TAS         |       |        BTIC           |
        | price = settlement |       | price = index close   |
        |   +/- ticks,       |       |   +/- basis ticks,    |
        |  traded intraday   |       |  traded intraday      |
        +-------------------+       +----------------------+
```

**E) Follow-ups:**
- *"Why would a PM use TAS instead of just trading at the close?"* → Avoids concentrating size in the volatile closing auction; locks settlement-relative price with more time flexibility.
- *"What's the basis in BTIC exactly?"* → Futures price minus fair-value-adjusted index (cost of carry: financing minus dividend yield times time to expiry).

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Walk through the lifecycle of a futures trade from execution through clearing and give-up.

**A) Time budget:** 2–2.5 minutes.

**B) Follow-ups:** "Do you want the give-up mechanics emphasized, since that's often the operational pain point specific to multi-manager platforms?"

**C) Detailed answer:**
> "Five stages:
> 1. **Order origination** — PM/pod generates the order; routed to an executing broker (EB) or the platform's own execution desk with the algo/venue chosen.
> 2. **Execution** — order matched on the exchange (CME/ICE match engine); trade confirmed with EB.
> 3. **Give-up** — because the EB executed the trade but the position must ultimately sit at the platform's designated **clearing broker (CB)**, the EB 'gives up' the trade to the CB per a pre-negotiated **give-up agreement**, which specifies fee splits, T+1 affirmation windows, and dispute handling.
> 4. **Clearing/margin** — the CB submits the trade to the exchange's clearinghouse (e.g., CME Clearing); novation occurs — clearinghouse becomes buyer to every seller and seller to every buyer, eliminating bilateral counterparty risk; initial and variation margin calculated (SPAN/CME's newer risk methodology).
> 5. **Settlement/booking** — daily mark-to-market, variation margin flows, and eventually either offsetting close-out or physical/cash settlement at expiry, with position rolled into the platform's books under the appropriate pod/PM sub-account."

**D) Feynman summary:** Think of it as three separate relationships stitched together — who executes (EB), who nets your risk (clearinghouse via novation), and who holds your account (CB) — the give-up agreement is just the legal glue connecting execution to custody.

**ASCII diagram — trade lifecycle:**
```
   PM/Pod Order
        |
        v
+----------------+     execute      +------------------+
| Executing Broker|----------------->|  Exchange Match   |
|      (EB)       |<-----------------|  Engine (CME/ICE) |
+----------------+     fill confirm  +------------------+
        |
        | GIVE-UP (per give-up agreement)
        v
+----------------+   submit trade   +------------------+
| Clearing Broker |----------------->|  Clearinghouse    |
|      (CB)       |<-----------------|  (novation, margin)|
+----------------+   margin calls   +------------------+
        |
        v
  Platform Books
  (pod/PM sub-account)
```

**E) Follow-ups:**
- *"What happens if a give-up is rejected/disputed?"* → Trade sits in a break/exception queue; EB and CB reconcile against the give-up agreement terms (usually T+1); until resolved, position risk is with the EB.
- *"Who bears basis/timing risk during the give-up window?"* → Contractually per the give-up agreement, but operationally the EB typically carries intraday risk until affirmation.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. What are margin requirements and position limits, and how do they constrain execution strategy?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Initial/variation margin mechanics, or more the strategic execution-sizing implications?"

**C) Detailed answer:**
> "**Margin**: Initial margin (IM) is collateral posted to open a position, sized by the clearinghouse's risk model (e.g., SPAN or CME's new methodology) to cover a ~99% VaR-style worst-case move over the margin period of risk. Variation margin (VM) is the daily (or intraday) mark-to-market settlement of gains/losses.
>
> **Position limits**: Exchange- or regulator-imposed caps on the net position a single account (or related accounts) can hold in a given contract/month, meant to prevent corners/squeezes, especially binding near expiry (spot-month limits are often tighter than back-month).
>
> **Execution constraint**: Both create a *sizing feedback loop* into execution strategy — (1) margin cost scales with gross notional, so a cost-aware execution desk must weigh algo aggressiveness (fast fills, higher impact) against holding period and margin efficiency; (2) position limits force **rolling ahead of expiry** and can force **spreading size across correlated contracts/venues** rather than concentrating in one instrument, which itself becomes a TCA input — you're now measuring cost across a *basket* of related exposures, not one clean fill."

**D) Feynman summary:** Margin is the toll you pay to keep the position; position limits are the guardrail on how much position you're even allowed to hold in one place — together they force execution to think in terms of *aggregate risk across instruments*, not a single ticket.

**E) Follow-ups:**
- *"How does margin period of risk differ across products?"* → Wider/less liquid contracts get longer assumed liquidation horizons, hence higher IM per contract.
- *"Do position limits differ between speculative and hedge accounts?"* → Yes — bona fide hedge exemptions typically get higher limits; relevant for a macro pod claiming hedging rationale.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. How does execution differ between electronic futures markets (CME, ICE) and OTC-cleared products?

**A) Time budget:** 1.5–2 minutes.

**B) Follow-ups:** "Are you thinking OTC-cleared as in cleared swaps (e.g., IRS via SEF) or bilateral OTC that's later cleared?"

**C) Detailed answer:**
> "Electronic futures (CME/ICE) trade on a **central limit order book (CLOB)** — continuous, anonymous, price-time priority matching, full pre-trade transparency of the book. Execution strategy there looks like classic algo trading: slicing orders, working passive vs. aggressive fills, benchmarking against VWAP/Arrival Price.
>
> OTC-cleared products (e.g., interest rate swaps on a SEF, or cleared FX forwards) typically trade via **request-for-quote (RFQ)** to a panel of dealers rather than a lit order book — less pre-trade transparency, execution quality is judged more by **dealer competitiveness / cover price analysis** than by slippage-vs-book benchmarks. Post-trade, both eventually clear (novate to a CCP), but the *price discovery* mechanism is fundamentally different: continuous double-auction vs. episodic multi-dealer quoting. This changes the TCA framework — futures TCA leans on tick-level book reconstruction; OTC TCA leans on RFQ cover-price and dealer-panel analysis."

**D) Feynman summary:** Futures execution is like buying from a public farmers' market where every price is visible; OTC-cleared execution is like calling three specific vendors and asking each for their best price — same eventual clearing/settlement plumbing, completely different price-discovery front end.

**E) Follow-ups:**
- *"Which is harder to build TCA for?"* → OTC — no continuous consolidated tape, so you benchmark against reconstructed dealer quotes/cover prices rather than a clean market VWAP.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. Explain how give-up agreements work between executing brokers and clearing brokers.

**A) Time budget:** 1.5 minutes (partially overlaps Q2 — keep this tighter, focus on the legal/operational terms).

**B) Follow-ups:** none needed — this is a direct factual ask.

**C) Detailed answer:**
> "A give-up agreement is a tri-party contract (platform/client, EB, CB) that pre-authorizes the EB to execute trades on the client's behalf and immediately 'give up' (transfer) those trades to the designated CB for clearing, without the CB needing to pre-approve each trade. Key terms: (1) **give-up fee** — a small per-contract fee paid to the EB for execution services since they don't hold the ongoing relationship/margin; (2) **affirmation window** — typically same-day or T+1, during which the CB can accept or reject (e.g., on credit-limit breach or bad allocation); (3) **credit limits** — CB pre-sets exposure limits the EB must trade within; (4) **allocation instructions** — for multi-account platforms like Millennium, specifies which pod/sub-account the trade allocates to. This structure lets a platform use many specialized EBs (for algo access, venue coverage, broker relationships) while consolidating all clearing/margin/custody at one or few CBs."

**D) Feynman summary:** It's a permission slip signed in advance — "any trade this broker does for me today, my clearing bank agrees to take onto its books by tomorrow" — which is what lets a big platform shop around for the best execution broker per trade without fragmenting its collateral across dozens of custodians.

**E) Follow-ups:**
- *"What breaks a give-up?"* → Credit limit breach, bad/ambiguous allocation, or EB/CB system mismatch — lands in an exception queue.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. What operational considerations arise when rolling a futures position near contract expiry?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Financial futures (index/rates) roll, or physically-deliverable commodity roll — the operational risk profile differs a lot."

**C) Detailed answer:**
> "Several considerations layer together:
> 1. **Liquidity migration** — volume/open interest migrates from front to back contract over a roll window; executing too early means trading an illiquid back month, too late means competing with everyone else in a concentrated window (worse market impact).
> 2. **Basis/spread risk** — the calendar spread price itself moves during the roll window; a poorly-timed roll can lose more on spread slippage than it saves on liquidity.
> 3. **Position limits** — spot-month limits tighten near expiry (see Q3), forcing the roll rather than allowing it to be discretionary.
> 4. **Physical delivery avoidance** — for deliverable commodities, failing to roll/close before first notice day risks unwanted physical delivery obligations — an operational and compliance risk, not just a P&L one.
> 5. **TAS/BTIC usage** — rolls are often executed via TAS or as a calendar-spread order type specifically to avoid taking outright directional risk during the transition and to benchmark against settlement.
> 6. **TCA attribution** — roll cost needs to be tracked *separately* from alpha-driven trading cost, or it contaminates a PM's slippage numbers with a cost that's structural, not discretionary."

 - Refer to similar question **[Set 4 → Q4. Write q code to compute rolling realized volatility from a trade table.](./ANSWER4.md#q4-write-q-code-to-compute-rolling-realized-volatility-from-a-trade-table)**.

**D) Feynman summary:** Rolling is moving house while the old lease and new lease briefly overlap — you're paying for both, exposed to price changes in both, and must be out before the "eviction date" (first notice day) — good roll execution just means minimizing the overlap-period cost.

**E) Follow-ups:**
- *"How would you measure roll cost in TCA?"* → Compare the executed calendar-spread price against a benchmark spread (e.g., VWAP of the spread over the roll window), separate from outright-price slippage.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. How would you evaluate a futures broker or execution algorithm's quality across venues?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Are we talking single-venue algo performance, or cross-venue smart order routing quality specifically?"

**C) Detailed answer:**
> "A multi-dimensional scorecard, not a single number:
> - **Slippage vs. benchmark** — Implementation Shortfall / Arrival Price and VWAP-relative performance, normalized by order size (bps per unit of participation rate) so you're comparing like-for-like.
> - **Fill rate / passive rate** — for algos with passive components, what fraction rested and got filled vs. had to cross the spread — reveals whether the algo is genuinely capturing spread or masking impact as delay cost.
> - **Reversion post-fill** — measure short-horizon price reversion after fills; heavy reversion suggests the algo/broker is causing temporary impact rather than executing on genuine liquidity.
> - **Consistency/variance** — not just mean slippage but its dispersion; a broker with slightly worse average cost but much lower tail risk may be preferable for large orders.
> - **Venue-specific factors** — for cross-venue evaluation, normalize for venue liquidity/fee structure (maker-taker vs. flat fee) before comparing, since raw slippage differences may just reflect venue selection, not algo skill.
> I'd build this as a standardized scorecard (dashboard) that runs automatically per broker/algo/venue combination on a rolling window, flagged for statistical significance (see Set 6) before drawing conclusions."

**D) Feynman summary:** Judging a broker only on average slippage is like judging a driver only on average speed — you also need to know how often they crash (tail risk) and whether they're speeding through school zones (causing impact that shows up as reversion).

**E) Follow-ups:**
- *"How many trades/what time window before you'd trust a comparison?"* → Ties into Set 6 Q7 — sample size sufficient for statistical power given typical slippage variance, often needs weeks-to-months of data for less liquid contracts.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. Describe how the futures order book differs structurally from an equities limit order book.

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> "Mechanically both are price-time-priority CLOBs, but differences matter for execution: (1) **Tick size / contract multiplier** — futures often have coarser relative tick sizes and larger implied notional per contract, so the same participation-rate strategy produces different market-impact dynamics; (2) **Concentration of liquidity** — futures liquidity concentrates heavily in the front-month contract, with back months and calendar spreads much thinner, unlike equities where a single listing usually holds the liquidity; (3) **Cross-venue fragmentation** — equities in the U.S. are fragmented across many lit/dark venues (Reg NMS), while a given futures contract typically trades natively on one primary exchange (CME's ES vs ICE's products aren't fungible), so smart-order-routing considerations differ — futures SOR is more about spreading size over time/venues-for-the-same-product (e.g., globex vs block/EFP) rather than routing across competing exchanges; (4) **Implied/synthetic liquidity** — futures order books often show 'implied' orders synthesized from correlated spread markets (e.g., an implied outright price derived from a calendar spread order plus the other leg's book), which doesn't have a clean equities analogue."

**D) Feynman summary:** Same basic auction mechanism, but futures liquidity is a single deep pool concentrated in one 'main' contract with synthetic depth borrowed from spread markets, while equities liquidity is many shallower pools spread across competing venues for the same stock.

**E) Follow-ups:**
- *"What's an implied order concretely?"* → If you have a calendar spread market and an outright market for one leg, the exchange engine can synthesize/imply a matching order in the other outright from the spread + known leg, adding depth that isn't 'real' resting liquidity in that specific contract.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. What is basis risk in a futures roll, and how do you monitor it operationally?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** "Basis versus the underlying cash/index, or basis between the two futures legs of the roll itself?" (Both are valid readings — clarify before committing.)

**C) Detailed answer:**
> "Two related but distinct basis-risk concepts:
> 1. **Futures-vs-cash basis** — the gap between the futures price and the fair-value-adjusted spot/index price (cost of carry: $F = S \\cdot e^{(r-q)T}$ for an index future with financing rate $r$ and dividend yield $q$). If your hedge relies on the futures tracking the cash index and that basis moves unexpectedly, your hedge P&L diverges from the intended exposure.
> 2. **Calendar-spread basis** — during a roll, the price difference between the expiring and new contract; if it widens/narrows unexpectedly between when you decide to roll and when you execute, you realize a cost/gain unrelated to your directional view.
> Operationally, I'd monitor basis with a **live dashboard tracking the spread/basis in real time relative to its recent historical distribution (e.g., z-score vs. a rolling mean/std)**, with alerts if it moves outside a threshold during a planned roll window — flagging whether to accelerate, delay, or split the roll execution."

**D) Feynman summary:** Basis risk is the risk that the ruler you're using to measure your hedge (the futures price) stops matching the thing you're actually exposed to (cash index or the other contract leg) — you monitor it the same way you'd monitor any spread: track it, know its normal range, alert on deviations.

**E) Follow-ups:**
- *"Give the cost-of-carry formula."* → $F_0 = S_0 e^{(r-q)T}$; basis $= F_0 - S_0$, converging to zero at expiry.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. How do give-ups and booking conventions differ across asset classes (futures vs FX vs equities)?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** "Should I focus on operational booking mechanics or on how it affects TCA benchmark construction?"

**C) Detailed answer:**
> "**Futures**: give-up model as in Q5 — EB executes, CB clears via exchange novation; booking is clean, standardized by the exchange.
> **FX (spot/forward, non-cleared)**: no central clearinghouse for most bilateral FX — 'give-up' concept is looser, more like **prime brokerage give-up**, where trades executed with multiple executing dealers are given up to a single FX prime broker who nets/settles under CLS or bilateral credit lines; booking conventions involve settlement date (T+2 spot) and netting conventions that don't exist in futures.
> **Equities**: give-up is typically via **DVP (delivery-versus-payment) settlement** through a custodian, with trades executed across many venues (lit/dark/ATS) and given up/allocated to the custodian at T+1 (post-2024 shortened cycle); no exchange-level margin/clearing analogous to futures — settlement risk is bilateral/custodian-based rather than CCP-novated.
> The unifying TCA implication: each asset class's benchmark construction must respect its own settlement/booking convention — e.g., an FX TCA benchmark needs to account for settlement-date netting effects that a futures TCA framework simply doesn't have."

**D) Feynman summary:** All three have some version of 'who executed it vs. who holds it,' but only futures has a true clearinghouse in the middle; FX relies on prime-broker netting and CLS settlement, equities relies on custodian DVP — so a cross-asset TCA framework (Set 9 territory) has to abstract over genuinely different plumbing, not just different price benchmarks.

**E) Follow-ups:**
- *"Which is riskiest operationally?"* → FX, historically, due to settlement/Herstatt risk pre-CLS; still requires care in cross-currency netting even with CLS today.

[🔝 Back to Top](#-table-of-contents)

---
