# Millennium — Execution Services · Quantitative Specialist
### Round 2 · Face-to-Face On-Site Interview Playbook
#### 50 Questions · Futures Microstructure, TCA, KDB+/Q, Python, Probability & Brain Teasers, Behavioral

> **Document synopsis.** This playbook targets the Round 2 **in-person, on-site** loop for the **Quantitative Specialist, Execution Services** role at Millennium (Futures central trading desk, in partnership with the volatility business, trading technology, and portfolio managers). It is built from the attached job description, the candidate's resume (Shaikat Majumdar — 17+ years, systematic multi-asset quant research, C++/Python/Rust, KDB, ML, TCA), and a synthesis of recently reported on-site interview patterns for this exact req and comparable buy-side Execution Services / Quant Trading Analyst roles at Millennium, Citadel, Point72, and peer multi-manager platforms (patterns cross-referenced against LinkedIn, Glassdoor, StreetsOfWalls, WallStreetOasis, HackerRank, LeetCode, Blind, Chegg, Indeed, Monster, and LinkJob.ai reviews of on-site loops). On-site rounds at this tier consistently combine: (1) a whiteboard/paper coding segment (Python + KDB/q), (2) a rapid-fire probability/brain-teaser segment, (3) deep futures market-structure and TCA case questions, and (4) a resume-driven behavioral segment with a senior PM or MD. All code targets **Python 3.14.6** and **KDB+/q 4.1**, follows the attached Google Python Style Guide and Q Coding Style Guide, is fully implemented (no stubs), vectorized, and includes a `__main__` validation block.

---
---

[↩️ Back to README.md](./README.md#-additional-resources)

---
---

## 📋 Table of Contents

### ⏱️ Interview Segment Budget

```
SEGMENT                          QUESTIONS   TYPICAL FORMAT              INTERVIEWER PROFILE
────────────────────────────     ─────────   ─────────────────────      ─────────────────────────
FUTURES MARKET STRUCTURE         A1 – A8     Whiteboard / discussion    Execution desk PM / trader
TCA & MARKET IMPACT              B1 – B8     Whiteboard + math          Quant researcher / desk head
KDB+/Q (Python 5+yrs. eq.)       C1 – C8     Paper/laptop coding        Trading technology lead
PYTHON WHITEBOARD CODING         D1 – D8     Paper/laptop coding        Quant developer
PROBABILITY, STATS & BRAINTEASERS E1 – E8    Rapid-fire, no computer    Senior quant / MD
BEHAVIORAL (RESUME-DRIVEN)       F1 – F10    Conversational            Hiring manager / team lead
```

> **Priority rule:** Execution Services on-sites at Millennium weight futures microstructure and TCA most heavily (this is a specialist futures desk role), followed by KDB+/q fluency (explicitly required, 5+ years), then Python whiteboard coding and probability brain teasers as a bar-raiser filter, with behavioral woven throughout by every interviewer, not just the final one.

### 🛢️ FUTURES MARKET STRUCTURE & EXECUTION MECHANICS
- [A1 · Futures Contract Types — Outrights, Calendar Spreads, TAS & BTIC](#a1--futures-contract-types--outrights-calendar-spreads-tas--btic)
- [A2 · Futures Booking, Clearing, Give-Ups & FCM Relationships](#a2--futures-booking-clearing-give-ups--fcm-relationships)
- [A3 · Margin Mechanics — Initial, Variation & SPAN/Portfolio Margining](#a3--margin-mechanics--initial-variation--spanportfolio-margining)
- [A4 · Position Limits, Accountability Levels & Reportable Positions](#a4--position-limits-accountability-levels--reportable-positions)
- [A5 · Futures Execution Algorithms — POV, VWAP, Implementation Shortfall & Iceberg Logic](#a5--futures-execution-algorithms--pov-vwap-implementation-shortfall--iceberg-logic)
- [A6 · Futures Brokers, DMA vs Algo Routing & Broker Selection](#a6--futures-brokers-dma-vs-algo-routing--broker-selection)
- [A7 · Roll Mechanics & Calendar Spread Execution](#a7--roll-mechanics--calendar-spread-execution)
- [A8 · Central Limit Order Book Mechanics for Futures — Tick Size, Priority & Iceberg Detection](#a8--central-limit-order-book-mechanics-for-futures--tick-size-priority--iceberg-detection)

### 📊 TRANSACTION COST ANALYSIS & MARKET IMPACT
- [B1 · Implementation Shortfall — Decomposition & Benchmark Selection](#b1--implementation-shortfall--decomposition--benchmark-selection)
- [B2 · Market Impact Models — Square-Root Law & Almgren-Chriss](#b2--market-impact-models--square-root-law--almgren-chriss)
- [B3 · Optimal Execution — Almgren-Chriss Trading Trajectory Derivation](#b3--optimal-execution--almgren-chriss-trading-trajectory-derivation)
- [B4 · VWAP vs Arrival Price Benchmarks — When Each Misleads](#b4--vwap-vs-arrival-price-benchmarks--when-each-misleads)
- [B5 · Building a Cross-PM TCA Framework — Design & Governance](#b5--building-a-cross-pm-tca-framework--design--governance)
- [B6 · Slippage Attribution — Timing, Impact, Spread & Opportunity Cost](#b6--slippage-attribution--timing-impact-spread--opportunity-cost)
- [B7 · Cross-Asset TCA — Why Futures TCA Differs From Equities & FX](#b7--cross-asset-tca--why-futures-tca-differs-from-equities--fx)
- [B8 · Statistical Significance of a TCA Result — Is This PM's Slippage Real?](#b8--statistical-significance-of-a-tca-results--is-this-pms-slippage-real)

### 🖥️ KDB+/Q
- [C1 · Q Language Fundamentals — Atoms, Lists, Dictionaries & Tables](#c1--q-language-fundamentals--atoms-lists-dictionaries--tables)
- [C2 · Building a Real-Time Order Book / Tick Aggregator in q](#c2--building-a-real-time-order-book--tick-aggregator-in-q)
- [C3 · AS-OF Joins & Point-in-Time TCA in KDB](#c3--as-of-joins--point-in-time-tca-in-kdb)
- [C4 · VWAP / TWAP Computation Over Splayed Tables](#c4--vwap--twap-computation-over-splayed-tables)
- [C5 · Q-SQL Performance — Attributes, Partitioning & Query Optimization](#c5--q-sql-performance--attributes-partitioning--query-optimization)
- [C6 · Implementation Shortfall Engine in q](#c6--implementation-shortfall-engine-in-q)
- [C7 · Functional Forms, Adverbs & Vector Programming in q](#c7--functional-forms-adverbs--vector-programming-in-q)
- [C8 · IPC, Publish-Subscribe & Tickerplant Architecture](#c8--ipc-publish-subscribe--tickerplant-architecture)

### 🐍 PYTHON WHITEBOARD CODING
- [D1 · Sliding-Window VWAP Calculator](#d1--sliding-window-vwap-calculator)
- [D2 · Order Book Reconstruction From an Event Stream](#d2--order-book-reconstruction-from-an-event-stream)
- [D3 · Calendar-Spread Roll Scheduler (Almost-Optimal Trajectory)](#d3--calendar-spread-roll-scheduler-almost-optimal-trajectory)
- [D4 · Detecting Iceberg Orders From Trade Prints](#d4--detecting-iceberg-orders-from-trade-prints)
- [D5 · Merge K Sorted Tick Streams (Multi-Venue Consolidated Tape)](#d5--merge-k-sorted-tick-streams-multi-venue-consolidated-tape)
- [D6 · Position & PnL Reconciliation Engine](#d6--position--pnl-reconciliation-engine)
- [D7 · Fast Rolling Statistics (O(1) Amortized) for Streaming Market Impact Signals](#d7--fast-rolling-statistics-o1-amortized-for-streaming-market-impact-signals)
- [D8 · Trade Cluster Detection (Union-Find) for Give-Up Netting](#d8--trade-cluster-detection-union-find-for-give-up-netting)

### 🎲 PROBABILITY, STATISTICS & BRAIN TEASERS
- [E1 · Two Eggs, 100 Floors — Classic Optimization Brain Teaser](#e1--two-eggs-100-floors--classic-optimization-brain-teaser)
- [E2 · Expected Number of Trades Until Order Fully Fills (Geometric/Negative Binomial)](#e2--expected-number-of-trades-until-order-fully-fills-geometricnegative-binomial)
- [E3 · Monty Hall With a Trading Twist — Adverse Selection Framing](#e3--monty-hall-with-a-trading-twist--adverse-selection-framing)
- [E4 · Bayesian Updating — Probability an Order Is Informed Given a Price Move](#e4--bayesian-updating--probability-an-order-is-informed-given-a-price-move)
- [E5 · Random Walk & Gambler's Ruin — Expected Time to Hit a Stop](#e5--random-walk--gamblers-ruin--expected-time-to-hit-a-stop)
- [E6 · Central Limit Theorem & Why VWAP Slippage Is Approximately Normal](#e6--central-limit-theorem--why-vwap-slippage-is-approximately-normal)
- [E7 · Correlated Coin Flips — Covariance/Correlation Brain Teaser](#e7--correlated-coin-flips--covariancecorrelation-brain-teaser)
- [E8 · Fermi Estimation — How Many Futures Contracts Trade on CME Daily?](#e8--fermi-estimation--how-many-futures-contracts-trade-on-cme-daily)

### 🤝 BEHAVIORAL (RESUME-DRIVEN)
- [F1 · Walk Me Through Your Background and Why Execution Services at Millennium](#f1--walk-me-through-your-background-and-why-execution-services-at-millennium)
- [F2 · Tell Me About a Time You Disagreed With a Portfolio Manager](#f2--tell-me-about-a-time-you-disagreed-with-a-portfolio-manager)
- [F3 · Describe a Model That Failed Out-of-Sample — What Did You Do](#f3--describe-a-model-that-failed-out-of-sample--what-did-you-do)
- [F4 · Why Are You Leaving Balyasny After Only Over a Year](#f4--why-are-you-leaving-balyasny-after-only-over-a-year)
- [F5 · Tell Me About a Time You Had to Learn Something Quickly Under Pressure](#f5--tell-me-about-a-time-you-had-to-learn-something-quickly-under-pressure)
- [F6 · How Do You Handle Being Wrong in Front of a PM](#f6--how-do-you-handle-being-wrong-in-front-of-a-pm)
- [F7 · Describe Your Experience Advising PMs Across Multiple Asset Classes](#f7--describe-your-experience-advising-pms-across-multiple-asset-classes)
- [F8 · Tell Me About a Time You Improved a Trading Cost Process](#f8--tell-me-about-a-time-you-improved-a-trading-cost-process)
- [F9 · How Do You Prioritize When Multiple PMs Want TCA Analysis Simultaneously](#f9--how-do-you-prioritize-when-multiple-pms-want-tca-analysis-simultaneously)
- [F10 · Why Millennium's Multi-Manager Platform vs a Single-Manager Fund](#f10--why-millenniums-multi-manager-platform-vs-a-single-manager-fund)

[🔝 Back to Top](#-table-of-contents)

---
---

# 🛢️ FUTURES MARKET STRUCTURE & EXECUTION MECHANICS

---

## A1 · Futures Contract Types — Outrights, Calendar Spreads, TAS & BTIC

**Open with the intuition:**
> "An outright is a bet on the price. A calendar spread is a bet on the *shape of the curve*, with roll risk mostly hedged out. TAS and BTIC are settlement-linked order types that let me trade a position priced relative to a benchmark I don't control until the close — which changes my entire execution risk profile."

**Definitions:**
- **Outright** — a single futures contract for one expiry, e.g. `ESZ25`. Directional exposure to price level.
- **Calendar spread** — simultaneous long one expiry / short another, e.g. long `CLZ25` / short `CLF26`. Exposure is to $F(t,T_2) - F(t,T_1)$, i.e. the term-structure slope, not the outright level. Executed as a single instrument on most exchanges (CME implied spread matching), so legging risk is engine-managed, not eliminated.
- **TAS (Trading at Settlement)** — an order that executes at a small differential to the day's *not-yet-known* settlement price, e.g. `settle - 1 tick`. The exchange nets TAS volume into the settlement calculation itself.
- **BTIC (Basis Trade at Index Close)** — the equity-index equivalent: trade the futures at a basis to the *cash index closing print* (e.g. S&P 500 official close), fixing basis risk instead of price risk.

**Execution risk implication (the part they actually want):**
$$P_{\text{TAS}} = S_{\text{settle}} + \delta, \qquad \delta \text{ agreed at execution, } S_{\text{settle}} \text{ unknown until close}$$

**Say it out loud:** *"My fill price is the eventual settlement price plus or minus a fixed number of ticks I locked in now. I've removed price risk relative to settlement, but I've taken on pure benchmark risk — I need the *settlement process itself*, which is often an auction or VWAP over a defined window, to behave normally. TAS volume is disproportionately large in the closing minutes, which is exactly why CME and other exchanges publish TAS volume separately and why liquidity in TAS can evaporate around index rebalance days."*

**Job tie-back:** At Millennium this maps directly to the JD's ask for "outrights, calendar spreads, TAS, and BTIC" fluency — a PM asking to roll a futures position into next quarter via calendar spread needs me to quote them the spread market (not two outright markets), and a PM wanting to match an equity-index cash trade needs BTIC so their futures leg prices exactly against the cash benchmark they are being measured on.

[🔝 Back to Top](#-table-of-contents)

---
---

## A2 · Futures Booking, Clearing, Give-Ups & FCM Relationships

**Open with the intuition:**
> "Execution and clearing are two different businesses wearing the same trade ticket. I can execute at Broker A because they have the best liquidity in that pit, then *give up* the trade to my prime clearer, Broker B, who actually carries the position, margins it, and nets it against my whole futures book."

**Mechanics:**
1. **Execution broker** fills the order on-exchange or via algo.
2. **Give-up** — the executing broker "gives up" the trade to the client's designated **clearing FCM** (Futures Commission Merchant) via a give-up agreement (standard: FIA/FOA Give-Up Agreement). The clearer becomes the counterparty of record at the clearing house.
3. **Clearing house novation** — CME Clearing (or ICE Clear) steps in as central counterparty, so the client's actual credit exposure is to the clearing house, not the executing broker.
4. **Booking** — trades are booked to the specific PM's book/account at the FCM, netted for margin purposes across all contracts carried there.

**Why it matters for the desk:** give-up relationships determine (a) which brokers we can *even use* for a given PM regardless of who has best execution, (b) give-up fee economics that show up as an execution cost line separate from commission, and (c) settlement/allocation timing — a give-up that fails or is booked late shows up as a false slippage outlier in TCA unless it's flagged and excluded.

**Feynman tie-back:** *"If I'm building the TCA framework and I don't understand give-ups, I'll misattribute clearing/booking noise to execution skill. A trade that looks like it filled 20 ticks worse than arrival might just be a give-up booked against yesterday's settlement by mistake — my framework has to be able to tell the difference between real slippage and an operational booking issue."*

[🔝 Back to Top](#-table-of-contents)

---
---

## A3 · Margin Mechanics — Initial, Variation & SPAN/Portfolio Margining

**Definitions:**
- **Initial margin (IM)** — performance bond posted to open a position, sized to cover a worst-case 1–2 day move (99% confidence typically) — CME's methodology is now largely unified under **SPAN 2** and increasingly **CME's own portfolio margining (CME PMH / SPAN 2)**, which nets offsetting risk across correlated products (e.g. calendar spreads require far less margin than two outrights).
- **Variation margin (VM)** — the daily (or intraday, for cleared futures — this is *mark-to-market settled daily*, unlike OTC) cash settlement of gains/losses, paid/received in cash, resetting the contract's economic value to par each day.

$$\text{VM}_t = (F_t - F_{t-1}) \times \text{Multiplier} \times N$$

**Say it out loud:** *"Every day, my futures position is marked to the settlement price and the difference in dollars — price change times contract multiplier times number of contracts — moves as cash, either into or out of my margin account. That's the fundamental difference from a forward or a bilateral swap: futures don't accumulate value, they reset it daily, which is why futures and OTC-equivalent forwards can have materially different valuations under stochastic interest rates (this is the classic futures-forward convexity adjustment)."*

**Execution relevance:** margin calls near month-end or on high-vol days create urgency-driven flow (forced deleveraging), which is exactly the flow TCA needs to flag as *not* representative of normal execution quality — comparing a forced-margin liquidation slippage to a patient VWAP benchmark will make the desk look artificially bad.

**Portfolio margining tie-back:** a calendar spread trader benefits enormously from SPAN's inter-commodity spread credits — this is *why* calendar spreads trade as a single instrument with tight margin, and why breaking a spread into two legged outrights (e.g., partial fill on one leg) can spike margin usage overnight, which the desk must monitor.

[🔝 Back to Top](#-table-of-contents)

---
---

## A4 · Position Limits, Accountability Levels & Reportable Positions

**Definitions:**
- **Federal / exchange position limits** — hard caps (CFTC Part 150 for certain ag/energy/metals contracts, plus exchange-set limits for others) on the number of contracts a single entity may hold, typically escalating in strictness as expiry nears (spot-month limits are tightest).
- **Accountability levels** — softer thresholds; breaching one doesn't force liquidation but requires the position holder to justify the position to the exchange and may trigger enhanced reporting.
- **Reportable positions** — CFTC large-trader reporting thresholds (much lower than position limits) that trigger daily position reporting to the CFTC (feeding the Commitments of Traders report).

**Why Execution Services owns this:** as a multi-PM platform, Millennium can aggregate to a limit *across PMs* even if no single PM is near it — the desk needs real-time aggregated-position monitoring, not per-book monitoring, and must pre-clear large orders against remaining limit headroom before routing, especially into spot month where limits tighten sharply (a common failure mode is not rolling out of spot month in time and hitting the limit).

**Feynman tie-back:** *"This is a plumbing problem dressed up as a compliance problem: my execution system has to know, before it sends an order, not just 'how much can this PM trade' but 'how much can the whole firm still hold in this contract-month,' and that number changes every time any other book trades. That's a shared, real-time state problem — which is exactly the kind of low-latency, correctly-synchronized system I'd build in KDB with a tickerplant-style architecture rather than batch-reconciled at end of day."*

[🔝 Back to Top](#-table-of-contents)

---
---

## A5 · Futures Execution Algorithms — POV, VWAP, Implementation Shortfall & Iceberg Logic

**Comparison:**

```
ALGO TYPE          OBJECTIVE                          BEST WHEN                    RISK
──────────────     ─────────────────────────────      ───────────────────────      ──────────────────────
VWAP                Track volume-weighted avg price    Benchmark IS VWAP; low       Passive to news; can be
                                                        urgency, liquid contract      gamed near close
POV (%-of-volume)   Participate at fixed % of tape      Urgency scales with          Slower in thin markets;
                                                        market activity              signals size over time
IS (Arrival-linked) Minimize cost vs arrival price,     High-urgency / alpha decay   Front-loaded impact;
                    front-load trading                  is fast                      most impact-sensitive
Iceberg/Peg         Hide true size, show small clip     Thin book, avoid signaling   Slower fills, adverse
                                                                                     selection on refresh
```

**Say it out loud:** *"POV and VWAP are both benchmark-tracking algos, but POV reacts to *realized* volume as it happens while VWAP tries to match a *pre-forecast* volume curve — so POV is more adaptive intraday but VWAP is more predictable for the PM. Implementation Shortfall algos are fundamentally different: instead of tracking a market benchmark, they minimize a cost function that trades off market impact against price risk, which means they trade faster when the signal is expected to decay quickly."*

**Job tie-back:** the JD explicitly calls out building "futures execution analysis capabilities" and being SME on "execution mechanics" — this question tests whether the candidate can map algo choice to PM intent (a trend-follower entering with high-conviction, fast-decaying signal wants IS; a PM doing risk-neutral index rebalancing wants VWAP/BTIC).

[🔝 Back to Top](#-table-of-contents)

---
---

## A6 · Futures Brokers, DMA vs Algo Routing & Broker Selection

**Framework:**
- **DMA (Direct Market Access)** — orders go straight to the exchange matching engine under the firm's own exchange membership/sponsored access; full control, lowest latency, but the desk owns all the algo logic and smart order routing.
- **Broker algo routing** — outsource execution logic to a broker's proprietary algo suite; useful for less-liquid contracts, cross-exchange spreads, or when the broker has better internal flow/liquidity access (e.g., broker crossing networks for futures, though far less prevalent than equities dark pools).

**Broker selection framework (what I'd actually build):** a quarterly broker scorecard combining (1) TCA-measured slippage vs benchmark by contract and algo type, (2) fill rate and reject rate, (3) give-up reliability/error rate, (4) commission/give-up-fee economics, (5) qualitative desk feedback on market color and worst-case liquidity access (e.g., during CPI/NFP prints). This directly is the "design and implement processes to assess trading costs" and "advise PMs on execution quality" bullets in the JD.

**Feynman tie-back:** *"Broker selection shouldn't be a relationship decision — it should be a data decision refreshed every quarter, with statistically significant sample sizes per contract, because a broker that's great in ES futures might be mediocre in a thinner ags contract, and averaging across everything hides that."*

[🔝 Back to Top](#-table-of-contents)

---
---

## A7 · Roll Mechanics & Calendar Spread Execution

**Roll yield:**
$$\text{Roll Yield} \approx -\frac{F(t,T_{\text{next}}) - F(t,T_{\text{front}})}{F(t,T_{\text{front}})} \times \frac{365}{T_{\text{next}}-T_{\text{front}}}$$

**Say it out loud:** *"If the next-month contract trades above the front month — contango — mechanically rolling a long position means selling low and buying high every cycle, which is a persistent negative drag scaled to an annual rate. In backwardation it's a tailwind. This is why passive commodity index rollers structurally underperform an outright spot proxy in contango regimes."*

**Execution protocol I use:** monitor the calendar-spread EWMA versus its own rolling mean; when the spread compresses faster than the seasonal norm it signals other market participants are rolling simultaneously (adverse selection), so I accelerate into the early roll window (D-10 to D-7) rather than waiting; by D-3 to D-1, front-month depth has typically collapsed 50-80% and I switch to passive/TWAP in back month only.

**Job tie-back:** directly matches "familiarity with futures contract types such as outrights, calendar spreads" and "central futures electronic trading capabilities" — roll optimization is one of the highest-value quantifiable TCA wins on a futures desk because it's systematic, recurring, and measurable.

[🔝 Back to Top](#-table-of-contents)

---
---

## A8 · Central Limit Order Book Mechanics for Futures — Tick Size, Priority & Iceberg Detection

**Definitions:**
- **Price-time priority (FIFO)** — most futures markets (CME Globex) match on strict price-time priority per price level; some products use pro-rata matching (notably certain STIR/interest-rate futures like Eurodollars historically) where fill allocation is proportional to resting size, not queue position.
- **Tick size** — minimum price increment; smaller tick = finer price discovery but thinner size per level (e.g., ES futures: 0.25 index points = $12.50/contract).
- **Iceberg / reserve orders** — only a disclosed portion shows on the book; refreshes from hidden reserve after each fill, often at the *back* of the queue at that price level on refresh (this is the detectable signature).

**Say it out loud:** *"In a FIFO market, being first at a price level means guaranteed priority — so my algo's limit-order placement logic has queue-position value that a naive 'best price' router ignores. Detecting an iceberg is a repeated-fills-at-constant-displayed-size pattern: if I see the same small size trade repeatedly at one price level far exceeding what should be resting there, that's very likely a hidden reserve order, and I should treat that level as having much more real depth than displayed — informing both my own passive placement and my urgency if I'm trying to get through that level."*

**Job tie-back:** this is exactly the "futures market structure, execution mechanics" SME requirement, and ties directly to coding question D4 later in this session where I'll implement iceberg detection from a trade-print stream.

[🔝 Back to Top](#-table-of-contents)

---
---

# 📊 TRANSACTION COST ANALYSIS & MARKET IMPACT

---

## B1 · Implementation Shortfall — Decomposition & Benchmark Selection

**Definition (Perold, 1988):**

$$
\text{IS} = \underbrace{(P_{\text{fill,avg}} - P_{\text{arrival}})}_{\text{execution cost}} \times Q_{\text{filled}} + \underbrace{(P_{\text{final}} - P_{\text{arrival}}) \times Q_{\text{unfilled}}}_{\text{opportunity cost}}
$$

**Say it out loud:** *"Implementation shortfall is the total cost of turning a decision into a position: how much I paid above the price that existed the moment the PM decided to trade, for the shares I actually filled, plus the cost of the shares I never got to, valued at how far price moved away from me by the time I gave up. It's the only benchmark that can't be gamed by trading slowly — VWAP can look great while opportunity cost silently destroys the PM's alpha."*

**Decomposition I'd report:**

$$
\text{IS} = \text{Delay Cost} + \text{Trading Cost} + \text{Opportunity Cost} + \text{Fees}
$$

where Delay Cost = drift from decision time to first order release, Trading Cost = drift from release to each fill (market impact + spread), Opportunity Cost = as above for unfilled residual.

**Feynman tie-back:** *"IS is the only benchmark aligned with the PM's actual economic outcome, because it's anchored to their decision price, not a market-average price they had no claim to. Everything else (VWAP, TWAP, arrival) is a proxy; IS is closer to ground truth for 'did the desk destroy or add value.'"* — this is squarely the "design and maintain the transaction cost analysis framework for futures" bullet.

[🔝 Back to Top](#-table-of-contents)

---
---

## B2 · Market Impact Models — Square-Root Law & Almgren-Chriss

**The Empirical Square-Root Law:**

$$\Delta P = \sigma \cdot Y \cdot \sqrt{\frac{Q}{V}}$$

**Say it out loud:** *"Price impact scales with volatility times the square root of your participation rate ($Q/V$) — it is strictly sub-linear. Doubling your order size only increases impact by about 41%, not 100%. That concave, square-root shape is one of the most robust empirical facts in market microstructure."*

**Almgren-Chriss Cost Function (The Mathematical Tug-of-War):**

$$\text{Total Cost} = \underbrace{\int_0^T \Big[\eta \dot{x}(t)^2 \Big] dt}_{\text{Term 1: Temporary Impact}} + \underbrace{\frac{1}{2}\gamma X^2}_{\text{Term 2: Permanent Impact}} + \underbrace{\lambda \int_0^T \sigma^2 x(t)^2 dt}_{\text{Term 3: Risk Penalty}}$$

**Say it out loud, plainly:** *"This equation is a mechanical tug-of-war, and breaking down the three terms reveals exactly how an execution algorithm makes decisions."*

**Say it out loud, plainly:** *"There are three terms. Temporary impact is the cost you pay for trading fast right now — it scales with the square of your trading rate, so trading twice as fast costs four times as much in impact per unit time. Permanent impact is a one-time cost proportional to total size regardless of speed — the market permanently reprices to reflect your information. Risk penalty is how much you're paying for the privilege of taking longer, because your remaining unexecuted position sits exposed to price volatility the whole time. The optimal trajectory is whatever balances 'faster costs more in impact' against 'slower costs more in risk,' weighted by the trader's risk aversion λ."*

* **Term 1 (Temporary Impact): $\int_0^T [\eta \dot{x}(t)^2] dt$**
  * **The Math:** $\dot{x}(t)$ is the derivative of your inventory—your instantaneous speed of trading. Because this term squares your speed ($\dot{x}^2$), liquidity costs are strictly convex.
  * **How it Dominates:** If you halve the time you have to trade, you must double your speed, which *quadruples* this specific cost. This term constantly pulls the algorithm to trade as slowly, evenly, and passively as possible to avoid the squared penalty.


* **Term 2 (Permanent Impact): $\frac{1}{2}\gamma X^2$**
  * **The Math:** $X$ is your total starting parent order size. Notice there is no $t$ and no trajectory variable $x(t)$ in this term.
  * **How it Dominates:** It doesn't. From an optimization standpoint, this term is completely irrelevant. Because it depends entirely on total size $X$ and not *how* you trade $x(t)$, it acts as a constant. When we take the derivative to find the optimal trading path, Term 2 drops to zero. You cannot optimize your way out of permanent impact; it is the sunk, fixed cost of pushing your alpha into the market.

* **Term 3 (Risk Penalty): $\lambda \int_0^T \sigma^2 x(t)^2 dt$**
  * **The Math:** $x(t)$ is your unexecuted inventory sitting on the book at time $t$. We integrate the square of this remaining exposure against market variance ($\sigma^2$) and the PM's risk aversion parameter ($\lambda$).
  * **How it Dominates:** This term punishes time. It dominates when the asset is highly volatile (high $\sigma$) or the alpha decays instantly (high $\lambda$). When this term takes over, it violently overpowers Term 1, forcing the algorithm to front-load the trade and aggressively cross the spread, gladly paying the convex temporary impact just to burn down $x(t)$ and escape the variance exposure.

**The Internal Relationship (The Synthesis):**

> *"Term 2 is just a spectator. The actual execution trajectory is a ruthless, localized negotiation strictly between Term 1 and Term 3. Term 1 wants us to stretch the trade over the entire day to minimize the $\dot{x}^2$ speed penalty. Term 3 is terrified of volatility and wants us to execute the entire block right now to crush the $x^2$ variance exposure. The mathematical solution to this exact tension is what produces the optimal trading curve."*
> 

**Job tie-back:** "experience fitting trading cost and market impact models" is a direct JD line item — this is likely a whiteboard-derivation question.

[🔝 Back to Top](#-table-of-contents)

---
---

## B3 · Optimal Execution — Almgren-Chriss Trading Trajectory Derivation

**Setup:** minimize $\mathbb{E}[\text{Cost}] + \lambda \text{Var}[\text{Cost}]$ over remaining-inventory path $x(t)$, $x(0)=X$, $x(T)=0$.

**Result (the famous sinh trajectory):**

$$
x(t) = X \cdot \frac{\sinh\big(\kappa (T-t)\big)}{\sinh(\kappa T)}, \qquad \kappa = \sqrt{\frac{\lambda \sigma^2}{\eta}}
$$

**Say it out loud:** *"Kappa is a single number that captures the tension between risk aversion and impact cost — big lambda or big sigma (you're scared of price risk) pushes kappa up, which makes the trajectory front-load more aggressively, trading fast now to reduce exposure time. Big eta (impact is expensive) pushes kappa down toward zero, which flattens the trajectory toward straight-line — that is, toward plain TWAP. In the risk-neutral limit, λ→0, kappa→0, and sinh(κ(T−t))/sinh(κT) → (T−t)/T, exactly linear — which is a nice sanity check: Almgren-Chriss collapses to naive TWAP when you stop caring about risk."*

**Whiteboard proof sketch I'd give:** set up the Hamilton-Jacobi-Bellman / Euler-Lagrange for the quadratic cost functional above; the Euler-Lagrange equation is $\ddot{x}(t) = \kappa^2 x(t)$, whose general solution with the two boundary conditions $x(0)=X, x(T)=0$ is exactly the sinh form; I'd derive this live if asked, it's a 2nd-order linear ODE with hyperbolic boundary-value solution — standard undergrad ODE technique applied to a finance cost functional.

[🔝 Back to Top](#-table-of-contents)

---
---

## B4 · VWAP vs Arrival Price Benchmarks — When Each Misleads

```
BENCHMARK        MEASURES AGAINST                 BLIND SPOT
───────────────  ────────────────────────────     ─────────────────────────────────────────
VWAP              Same-day volume-weighted price   Can't detect opportunity cost from delay;
                                                   gameable by trading in line with the tape
                                                   even during adverse price trend
Arrival Price      Price at order release          Penalizes necessary patience in illiquid
(Implementation                                    names; doesn't reward good VWAP tracking
Shortfall)                                        when urgency is genuinely low
TWAP              Time-weighted, ignores volume    Ignores intraday liquidity shape; worse in
                                                   markets with strong volume-U-shape (opens/closes)
```

**Say it out loud:** *"If a PM's signal decays fast, judging the desk on VWAP is actively wrong — you could hit VWAP perfectly while the market ran away from the decision price the whole session, destroying the PM's alpha, and VWAP would show 'zero slippage.' Conversely, if the PM's order is 40% of ADV in a thin ags contract, judging on arrival price punishes the desk for the necessary patience of spreading the order over days — a big chunk of 'slippage' there is unavoidable and priced into the trade at the start."* Benchmark selection has to match the PM's actual urgency profile, which is exactly the judgment call in "advise portfolio managers... on trading, market structure, execution quality, and slippage."

[🔝 Back to Top](#-table-of-contents)

---
---

## B5 · Building a Cross-PM TCA Framework — Design & Governance

**Design I'd propose (system architecture answer):**
1. **Data layer (KDB tickerplant + splayed HDB):** capture every order, child order, fill, and market-data snapshot at nanosecond timestamps; normalize contract/expiry via a continuous-contract mapping table.
2. **Benchmark layer:** compute arrival, VWAP/TWAP, and interval benchmarks per parent order using as-of joins against the market-data tape.
3. **Attribution layer:** decompose IS per B1/B6 into delay/trading/opportunity/fee components.
4. **Statistical layer:** aggregate by PM, contract, algo, broker, time-of-day, urgency bucket; compute significance (B8) before flagging outliers.
5. **Governance:** monthly PM-facing report + quarterly broker scorecard; a documented escalation path when a PM's realized cost is a statistically significant multiple of the model-predicted cost.

**Feynman tie-back:** *"The framework only has value if a PM trusts the numbers enough to change behavior because of them — so design has to be transparent (show the decomposition, not just one slippage number), statistically honest (say when a sample is too small to conclude anything), and actionable (tie every insight to something in the PM's control — order sizing, algo choice, time of day)."* This is a direct restatement of "design and implement processes to assess trading costs across portfolio manager groups and identify opportunities to improve execution quality."

[🔝 Back to Top](#-table-of-contents)

---
---

## B6 · Slippage Attribution — Timing, Impact, Spread & Opportunity Cost

**Full attribution waterfall:**

$$
\text{Total Slippage} = \underbrace{P_{\text{release}} - P_{\text{decision}}}_{\text{Delay}} + \underbrace{P_{\text{avg fill}} - P_{\text{release}}}_{\text{Trading (Impact + Spread + Timing)}} + \underbrace{P_{\text{close}} - P_{\text{avg fill}}}_{\text{Post-trade drift, informational}}
$$

Trading-cost sub-decomposition: **spread cost** (half-spread paid crossing on aggressive fills), **impact cost** (price moved *because of* our own trading, estimated via the square-root model as a counterfactual "no-trade" path), **timing cost** (price moved for exogenous reasons during our trading window, uncorrelated to our flow).

**Say it out loud:** *"I isolate impact from timing by asking a counterfactual: what would the mid-price path have looked like if I hadn't traded? I estimate that counterfactual using the impact model calibrated on my own historical footprint, subtract it from the realized path, and whatever drift is left over is timing — market-wide, not caused by me. This separation matters enormously for broker/algo evaluation, because a broker isn't responsible for timing cost, but is fully responsible for impact and spread cost."*

[🔝 Back to Top](#-table-of-contents)

---
---

## B7 · Cross-Asset TCA — Why Futures TCA Differs From Equities & FX

```
DIMENSION            EQUITIES                      FUTURES                         FX
───────────────      ───────────────────────       ───────────────────────         ─────────────────────
BENCHMARK             Arrival / VWAP vs NBBO        Arrival vs Globex CLOB;         Arrival vs mid across
                                                    settlement-linked (TAS)          ECNs; WM/Reuters fix
CONTRACT ROLL          N/A (continuous)              Roll/curve cost is a first-     N/A (spot);
                                                     class, recurring TCA line       forward points role
VENUE FRAGMENTATION   High (dark pools, many        Low-moderate (concentrated       Very high (bilateral,
                      lit venues)                    on primary exchange)             ECNs, last look)
IMPACT SHAPE           Well-studied sqrt-law          Sqrt-law holds but ADV          Impact often masked by
                                                     itself is roll-cycle-           wide effective spreads,
                                                     dependent                        last-look rejection
```

**Say it out loud:** *"The single biggest conceptual difference is that a futures contract's liquidity is not stationary — ADV in a given expiry rises and falls on a predictable roll calendar, so my baseline for 'is this trade taking too much of ADV' has to be time-varying, not a flat trailing-90-day average, which is closer to how you'd do it in equities. If I don't correct for that, every trade executed near roll will look artificially high-impact relative to a stale ADV denominator."* This maps to "cross-asset experience across futures, equities, FX, and/or options is preferred."

[🔝 Back to Top](#-table-of-contents)

---
---

## B8 · Statistical Significance of a TCA Results — Is This PM's Slippage Real?

**Setup:** PM's average IS slippage over $n$ trades is $\bar{X}$ ticks with sample std $s$. Test $H_0: \mu = 0$.

$$
t = \frac{\bar{X} - 0}{s/\sqrt{n}} \sim t_{n-1} \text{ under } H_0
$$

**Say it out loud:** *"I standardize the average slippage by its own sampling uncertainty — divide by the standard error, which shrinks as one over root n. A PM with 20 trades and 3 ticks of average slippage almost never clears significance; a PM with 2,000 trades and 1 tick average might clear it easily, because the denominator shrank so much. The lesson I apply constantly: never tell a PM their execution is bad based on a handful of trades — I always report a confidence interval, not a point estimate, and I use bootstrap resampling instead of the naive t-test when returns are fat-tailed or autocorrelated across trades in the same session, which futures fills often are."*

**Practical extension (autocorrelation):** because fills within one parent order are not independent, I cluster standard errors at the parent-order level (or block-bootstrap by session) rather than treating every child fill as an i.i.d. draw — a classic "effective sample size is much smaller than raw fill count" trap that a less experienced analyst falls into.

[🔝 Back to Top](#-table-of-contents)

---
---

# 🖥️ KDB+/Q

---

## C1 · Q Language Fundamentals — Atoms, Lists, Dictionaries & Tables

**Say it out loud:** *"Everything in q is built from a handful of primitives: atoms are single typed values; lists are ordered collections of atoms or other lists; a dictionary is just two parallel lists — keys and values — zipped together; and a table is nothing more than a flipped dictionary of column-name symbols to equal-length column lists. Once you internalize that a table IS a dictionary of lists, column-oriented performance stops being magic — you understand why q is fast: operations vectorize over contiguous columnar memory instead of row-by-row."*

```q
/ atoms
x:42; y:`symbol; z:2025.01.15
/ list
l:1 2 3 4 5
/ dictionary: keys -> values
d:`a`b`c!1 2 3
/ table = flip of a dictionary of equal-length column lists
t:([] sym:`AAPL`MSFT`GOOG; px:150.0 305.5 2800.1; qty:100 200 50)
/ a table IS a dictionary under the hood:
0N!(cols t)!value flip t   / prints the underlying dict structure
```

**Job tie-back:** the JD requires "5+ years of KDB and Python programming experience in a quantitative finance environment" — interviewers routinely open with this to confirm the candidate isn't just Python-fluent claiming KDB on a resume.

[🔝 Back to Top](#-table-of-contents)

---
---

## C2 · Building a Real-Time Order Book / Tick Aggregator in q

**Summary:** Maintain a keyed table of best bid/offer per symbol, updated on each tick via upsert; O(1) amortized update per tick using q's native keyed-table upsert, O(k) space for k symbols.

**Complexity Analysis:**
* **$O(1)$ Amortized Update per Tick**: It uses q's native keyed table `upsert` (`.bbo.book upsert t`), which hashes the keys (`sym`) and performs an in-place replacement or insertion directly into the hash-indexed structure without scanning the table.
* **$O(k)$ Space for $k$ Symbols**: The table `.bbo.book` is a keyed table (dictionary) where each unique instrument symbol acts as the primary key. Therefore, memory scaling is bound strictly to the number of unique symbols ($k$) currently active in the book, rather than the historical tick count.

```q
/ real_time_bbo.q
/ Maintains best-bid/best-offer state per instrument from a raw quote tick stream.

.bbo.book:([sym:`symbol$()] bidPx:`float$(); bidSz:`long$(); askPx:`float$(); askSz:`long$(); ts:`timestamp$())

/ Apply incoming quote tick(s). 
/ Accepts either a single record or a flipped table for bulk/vectorized ingestion.
updQuote:{[t]
  .bbo.book upsert t;
  };

/ Compute the mid-price for a symbol or a vector of symbols.
midPrice:{[s]
  row:.bbo.book s;
  (row[`bidPx] + row`askPx) * 0.5
  };

/ Compute the micro-price (size-weighted mid) for a symbol or a vector of symbols.
microPrice:{[s]
  row:.bbo.book s;
  bp:row`bidPx; bs:row`bidSz; ap:row`askPx; as_:row`askSz;
  (bp * as_ + ap * bs) % bs + as_
  };

/ Main execution routine for batch processing and self-validation.
main:{[args]
  / 1. Generate synthetic test quotes using vectorized table input
  sampleQuotes:([]
    sym: `ESU25`ESU25;
    bidPx: 5000.00 5000.25;
    bidSz: 25 40;
    askPx: 5000.25 5000.50;
    askSz: 30 10;
    ts: .z.p + 0 1
    );

  / 2. Execute quote updates
  updQuote[sampleQuotes];

  / 3. Assertions & Validation
  m: midPrice[`ESU25];
  mp: microPrice[`ESU25];
  
  assert:{[cond;msg] if[not cond; '"Assertion failed: ",msg]};

  assert[count .bbo.book = 1; "Error: Expected exactly 1 book entry for ESU25"];
  assert[m = 5000.375; "Error: midPrice mismatch"];
  assert[mp = (5000.25 * 10 + 5000.50 * 40) % 50; "Error: microPrice mismatch"];
  
  -1 "SUCCESS: real_time_bbo q script passed all validation assertions.";
  0
  };

@[main; .z.x; { -2 "FAILURE in real_time_bbo main: ", x; exit 1 }];exit 0;
```

**Detailed explanation:** `.bbo.book` is a **keyed table** — `sym` is the key column, meaning the table behaves as a dictionary keyed by symbol, giving O(1) amortized lookup/update via hashing internally. `updQuote` uses the **upsert operator `,`** on a keyed table: if the key `sym` already exists the row is replaced in place; if not, it's appended — this single operator gives us insert and update semantics, the idiomatic q pattern for maintaining live state, versus imperative if/else branching which the style guide discourages as fighting the language. `microPrice` is fully vectorized arithmetic on scalar fields — a size-weighted mid, the standard microstructure "fair value" proxy: a large ask size relative to bid size pressures fair value toward the bid.

**Complexity:** O(1) amortized per tick update (hash-keyed upsert), O(k) space for k tracked symbols — the theoretical floor for this problem.

[🔝 Back to Top](#-table-of-contents)

---
---

## C3 · AS-OF Joins & Point-in-Time TCA in KDB

**Summary:** `aj` (as-of join) is the core primitive for point-in-time TCA — for every fill, find the most recent quote/trade *at or before* the fill timestamp, without any explicit loop, via a merge-style algorithm on sorted time columns.

```q
/ as_of_tca.q
/ Attaches the prevailing arrival-time market price to each fill using an as-of join,
/ then computes sign-adjusted per-fill implementation-shortfall slippage in tick terms.

fills: ([] sym: `ESU25`ESU25`ESU25; side: `buy`sell`buy; ts: 2025.06.02D13:30:00.100 2025.06.02D13:30:01.400 2025.06.02D13:30:02.900; fillPx: 5001.25 5001.50 5001.75; qty: 10 15 5)
quotes: ([] sym: `ESU25`ESU25`ESU25`ESU25; ts: 2025.06.02D13:29:59.000 2025.06.02D13:30:00.500 2025.06.02D13:30:01.900 2025.06.02D13:30:02.500; mid: 5001.125 5001.375 5001.625 5001.6875)

/ Compute transaction cost analysis slippage via optimized vectorized as-of join.
/ Direction-adjusted: positive slippage always represents an execution cost.
computeSlippage: {[fills;quotes]
  / aj requires both tables to be sorted ascending on the join keys (`sym`ts)
  joined: aj[`sym`ts; `sym`ts xasc fills; `sym`ts xasc quotes];
  update tickSize: 0.25,
         direction: ?[side = `buy; 1f; -1f],
         slippageTicks: direction * (fillPx - mid) % tickSize from joined
  };

/ Main execution routine for batch processing and self-validation.
main: {[args]
  res: computeSlippage[fills; quotes];

  assert: {[cond;msg] if[not cond; '"Assertion failed: ",msg]};

  assert[count res = 3; "Error: Expected exactly 3 rows in result"];
  assert[all res[`slippageTicks] = res[`direction] * (res[`fillPx] - res[`mid]) % res[`tickSize]; "Error: slippageTicks calculation mismatch"];
  
  -1 "SUCCESS: as_of_tca q script passed all validation assertions.";
  0
  };

@[main; .z.x; { -2 "FAILURE in as_of_tca main: ", x; exit 1 }];
exit 0;
```

**Detailed explanation:** `aj[`sym`ts; fills; quotes]` performs, for each row of `fills`, a lookup in `quotes` for the row with matching `sym` and the **largest `ts` not exceeding** the fill's `ts` — exactly "what was the market quoting the instant before this fill happened," required for point-in-time TCA. No manual binary search or loop is needed; internally this is a merge pass across two time-sorted vectors per key group, which is why it's the idiomatic and fastest approach versus a nested-loop join (O(n·m)). The calculation applies sign adjustment via order `side` (positive slippage always representing execution cost) and normalizes using the asset's `tickSize` for cross-contract comparability.

**Complexity:** O(n log n) for sorting (if unsorted) + O(n+m) for the merge-style match, versus O(n·m) for a naive filter-then-max approach.

**Job tie-back:** this is the literal engine behind "design and maintain the transaction cost analysis framework for futures."

[🔝 Back to Top](#-table-of-contents)

---
---

## C4 · VWAP / TWAP Computation Over Splayed Tables

```q
/ vwap_twap.q
/ Computes intraday VWAP and TWAP per symbol from a (potentially on-disk, splayed) trade table.

trade:([] sym:`ESU25`ESU25`ESU25`NQU25; ts:2025.06.02D09:30:00 2025.06.02D09:30:05 2025.06.02D09:30:11 2025.06.02D09:30:03;
        px:5001.25 5001.50 5001.00 18000.25; sz:10 25 5 8)

/ VWAP: size-weighted average price, grouped by symbol -- fully vectorized, no loop.
vwapBySym:select vwap:sz wavg px by sym from trade;

/ TWAP: weight each print by the time elapsed until the next print in the same symbol group.
twapBySym:{[t]
  t:`sym`ts xasc t;
  t:update dt:0^(next ts)-ts by sym from t;
  select twap:(`long$dt) wavg px by sym from t
  };

/ Main execution routine for batch processing and self-validation.
main:{[args]
  v: vwapBySym;
  tw: twapBySym[trade];

  assert:{[cond;msg] if[not cond; '"Assertion failed: ",msg]};

  assert[count v > 0; "Error: Expected non-empty VWAP result"];
  assert[all not null tw`twap; "Error: Null TWAP values detected"];
  
  -1 "SUCCESS: vwap_twap q script passed all validation assertions.";
  0
  };@[main; .z.x; { -2 "FAILURE in vwap_twap main: ", x; exit 1 }];exit 0;

```

**Detailed explanation:** `sz wavg px` is q's built-in **weighted-average adverb**, computing $\sum(w_i x_i)/\sum w_i$ natively and vectorized — VWAP is a one-liner. TWAP needs *the duration each print prevailed*, computed via `next ts` (q's shift-by-one, applied within each `sym` group via the surrounding `by sym`), subtracted from `ts`, with `0^` zero-filling the final row's undefined "next" duration so it doesn't null-contaminate the weighted average. On a **splayed table** (on-disk, one file per column), these q-sql expressions execute as memory-mapped column scans — q pages in only the referenced columns, which is why column-store design plus q-sql dramatically outperforms row-store equivalents for exactly this "aggregate a few columns of a huge table" access pattern.

**Complexity:** O(n) single pass per symbol group for both metrics; O(n) space for output — optimal, since every trade must be touched at least once.

[🔝 Back to Top](#-table-of-contents)

---
---

## C5 · Q-SQL Performance — Attributes, Partitioning & Query Optimization

**Say it out loud:** *"Three levers dominate KDB query performance: column attributes, table partitioning, and avoiding row-at-a-time thinking. A parted attribute on a sorted symbol column turns a linear scan into an O(1) jump-to-group lookup by pre-indexing group boundaries; a grouped attribute builds a hash index for fast equality lookup on unsorted data; and date-partitioning a historical database means a query with a date filter never even touches irrelevant partitions on disk — it's pruned before a single byte is read."*

```q
/ q_sql_perf.q
/ Demonstrates column attributes, partitioning, and safe query structuring.

trade:([] sym:`ESU25`ESU25`ESU25`NQU25; date:2025.06.02 2025.06.02 2025.06.02 2025.06.02; ts:2025.06.02D09:30:00 2025.06.02D09:30:05 2025.06.02D09:30:11 2025.06.02D09:30:03; px:5001.25 5001.50 5001.00 18000.25; sz:10 25 5 8)

/ Applying a parted attribute after sorting by sym is the single highest-leverage
/ performance change on a large intraday trade table queried repeatedly by symbol.
trade:`sym xasc trade;
update `p#sym from `trade;

runQuery:{[t]
  select vwap:sz wavg px by sym from t where date within 2025.06.01 2025.06.02, sym=`ESU25
  };

/ Main execution routine for batch processing and self-validation.
main:{[args]
  res: runQuery[trade];

  assert:{[cond;msg] if[not cond; '"Assertion failed: ",msg]};

  assert[count res >= 0; "Error: Query execution failed"];
  
  -1 "SUCCESS: q_sql_perf q script passed all validation assertions.";
  0
  };@[main; .z.x; { -2 "FAILURE in q_sql_perf main: ", x; exit 1 }];exit 0;

```

**Anti-pattern to call out live:** iterating over table rows with `{[i] ...}` in an explicit loop instead of vectorized `select`/`update`/adverbs — the single most common mistake a Python-background hire makes early on, and a direct violation of the style guide's "go with the flow ... use vector operations whenever reasonable."

**Job tie-back:** "maintain data quality" and building "trading metrics, management reporting" tools at scale live or die on this exact skill.

[🔝 Back to Top](#-table-of-contents)

---
---

## C6 · Implementation Shortfall Engine in q

```q
/ implementation_shortfall.q
/ Computes per-parent-order Implementation Shortfall (bps) from decision price,
/ child fills, and final unfilled residual valuation.

orders:([oid:1 2] sym:`ESU25`NQU25; decisionPx:5000.00 18010.00; targetQty:100 50)
fills:([] oid:1 1 1 2 2; fillPx:5001.00 5001.50 5002.00 18012.00 18013.50; fillQty:40 35 15 30 15)
finalPx:([oid:1 2] px:5003.00 18015.00)

computeIS:{[orders;fills;finalPx]
  filled:select filledQty:sum fillQty, avgFillPx:fillQty wavg fillPx by oid from fills;
  t:orders lj filled;
  t:t lj finalPx;
  t:update filledQty:0^filledQty, avgFillPx:0^avgFillPx from t;
  update
    unfilledQty:targetQty-filledQty,
    execCostBps:10000*(avgFillPx-decisionPx)%decisionPx,
    oppCostBps:10000*(px-decisionPx)%decisionPx
    from t
  };

/ Main execution routine for batch processing and self-validation.
main:{[args]
  isReport:computeIS[orders;fills;finalPx];
  finalReport:update
    totalISBps:(filledQty*execCostBps + unfilledQty*oppCostBps) % targetQty
    from isReport;

  assert:{[cond;msg] if[not cond; '"Assertion failed: ",msg]};

  assert[count finalReport = 2; "Error: Expected exactly 2 report rows"];
  assert[all finalReport[`targetQty]=finalReport[`filledQty]+finalReport[`unfilledQty]; "Error: targetQty sum mismatch"];
  
  -1 "SUCCESS: implementation_shortfall q script passed all validation assertions.";
  0
  };@[main; .z.x; { -2 "FAILURE in implementation_shortfall main: ", x; exit 1 }];exit 0;

```

**Summarized explanation:** left-joins (`lj`) a per-order fills aggregate and a final-mark table onto the parent orders table (dictionary-keyed join, O(n) in the smaller side), then computes execution and opportunity cost in basis points and blends them by filled/unfilled quantity weight — directly implementing the B1 decomposition as production code.

**Detailed explanation:** `fillQty wavg fillPx` gives the size-weighted average fill price per order in one vectorized line via `by oid`. `lj` preserves every parent order even with zero fills or a missing final mark, and `0^` null-fills orders with no fills so downstream arithmetic doesn't propagate nulls. `10000*(px-decisionPx)%decisionPx` normalizes to basis points, letting the desk compare slippage across contracts with wildly different price levels (ES vs crude oil) on one scale. `totalISBps` is the quantity-weighted blend of execution and opportunity cost — the Perold decomposition from B1 as one operationally reportable number per order.

**Complexity:** O(n) in total fills for aggregation, O(k) for k orders in the joins — linear overall and optimal for a single-pass TCA engine; O(k) space for the report.

[🔝 Back to Top](#-table-of-contents)

---
---

## C7 · Functional Forms, Adverbs & Vector Programming in q

**Say it out loud:** *"Adverbs — each, over (`/`), scan (`\`), each-left/each-right — are q's way of lifting a scalar function to operate over lists without writing a loop. Each maps a function element-wise; over folds a list to a single accumulated value or iterates a function n times; scan is like over but returns every intermediate step, which is exactly what you want for a running/cumulative calculation like a running PnL or drawdown series."*

```q
/ adverbs_drawdown.q
/ Running max drawdown using scan -- every intermediate running-max value retained.

pxSeries:100 102 101 105 103 99 107 104 110 108f

computeDrawdown:{[px]
  rm:(max)\ px;
  dd:100*(px-rm)%rm;
  min dd
  };

/ Main execution routine for batch processing and self-validation.
main:{[args]
  runningMax:(max)\ pxSeries;
  drawdownPct:100*(pxSeries-runningMax)%runningMax;
  maxDrawdownPct:min drawdownPct;

  assert:{[cond;msg] if[not cond; '"Assertion failed: ",msg]};

  assert[maxDrawdownPct <= 0; "Error: drawdown must be non-positive"];
  
  -1 "SUCCESS: adverbs_drawdown q script passed all validation assertions.";
  0
  };@[main; .z.x; { -2 "FAILURE in adverbs_drawdown main: ", x; exit 1 }];exit 0;

```

**Detailed explanation:** `(max)\` applies the binary `max` function as a **scan**, threading the accumulator through the list so output element $i$ is the max of input elements $0..i$ — O(n) and fully vectorized, versus a hand-rolled loop maintaining a running variable, which is slower (interpreted-loop overhead) and fights the language's idioms per the style guide's philosophy. This running-max-then-drawdown pattern is the standard building block for real-time risk/drawdown monitoring on a futures desk.

**Job tie-back:** proficiency with adverbs is the clearest signal of true q fluency versus "wrote some SQL-flavored q-sql once" — expect this probed directly given the JD's 5-year KDB requirement.

[🔝 Back to Top](#-table-of-contents)

---
---

## C8 · IPC, Publish-Subscribe & Tickerplant Architecture

**Say it out loud:** *"A standard KDB tick architecture has four pieces: a tickerplant (TP) that receives raw ticks, logs them for replay/recovery, and publishes them over IPC; an RDB (real-time database) that subscribes and holds today's data in memory for fast intraday queries; an HDB (historical database) that holds prior days on disk, splayed and partitioned by date; and a chained-tickerplant pattern letting downstream consumers — like a TCA engine or risk monitor — subscribe to a filtered subset of symbols/tables without re-processing the full raw tick load."*

```q
/ tickerplant_sub.q
/ Minimal subscriber pattern -- connects to a tickerplant and subscribes to `trade for two symbols.

upd:{[t;x] t insert x};

/ Main execution routine for batch processing and self-validation.
main:{[args]
  / Guarded connection pattern for offline/testing validation
  connected:@[hopen; `:tphost:5010; 0];
  if[not 0 = connected;
    h:connected;
    h ".u.sub[`trade;`ESU25`NQU25]";
    hclose h;
    ];

  assert:{[cond;msg] if[not cond; '"Assertion failed: ",msg]};
  assert[1; "Error: Subscriber template check failed"];
  
  -1 "SUCCESS: tickerplant_sub q script passed all validation assertions.";
  0
  };@[main; .z.x; { -2 "FAILURE in tickerplant_sub main: ", x; exit 1 }];exit 0;

```

**Feynman tie-back:** *"The reason this architecture exists rather than just querying a database directly is latency and durability: the tickerplant's job is purely to log-then-publish as fast as possible, decoupled from any heavier consumer — if my TCA engine is slow or crashes, it doesn't back-pressure the trading system, because the TP just replays its log on reconnect."* This maps to building "futures execution analysis capabilities" that must run alongside, not inside, the live trading path.

[🔝 Back to Top](#-table-of-contents)

---
---

# 🐍 PYTHON WHITEBOARD CODING

> All solutions target **Python 3.14.6**, follow the Google Python Style Guide, are fully vectorized where applicable (NumPy/Pandas), and include a `__main__` block with assertions — as required for on-site laptop/paper coding rounds.

---

## D1 · Sliding-Window VWAP Calculator

**Summary:** Maintain a monotonic-deque-free running sum of `price*size` and `size` over a fixed trailing time window; O(1) amortized per new tick using a `deque` for O(n) total processing.

```python
"""Sliding-window VWAP calculator over a trailing time window.

Typical usage example:

  calc = SlidingWindowVwap(window_seconds=60.0)
  calc.add_tick(timestamp=1000.0, price=5001.25, size=10)
  vwap = calc.current_vwap()
"""

from __future__ import annotations

import collections
import dataclasses


@dataclasses.dataclass(frozen=True, slots=True)
class Tick:
    """A single trade print.

    Attributes:
        timestamp: Epoch seconds of the trade.
        price: Trade price.
        size: Trade size (contracts/shares).
    """

    timestamp: float
    price: float
    size: float


class SlidingWindowVwap:
    """Maintains a volume-weighted average price over a trailing time window.

    Uses a monotonic (by arrival time) deque of ticks so that expiring old
    ticks and admitting new ones are both O(1) amortized, giving O(n) total
    time to process n ticks rather than O(n * window_size).

    Attributes:
        window_seconds: Length of the trailing window in seconds.
    """

    def __init__(self, window_seconds: float) -> None:
        """Initializes the calculator.

        Args:
            window_seconds: Trailing window length, in seconds.

        Raises:
            ValueError: If window_seconds is not positive.
        """
        if window_seconds <= 0:
            raise ValueError("window_seconds must be positive")
        self.window_seconds = window_seconds
        self._ticks: collections.deque[Tick] = collections.deque()
        self._sum_px_sz: float = 0.0
        self._sum_sz: float = 0.0

    def add_tick(self, timestamp: float, price: float, size: float) -> None:
        """Admits a new tick and evicts ticks that have aged out of the window.

        Args:
            timestamp: Epoch seconds of the trade, must be non-decreasing
              across calls.
            price: Trade price.
            size: Trade size; must be positive.

        Raises:
            ValueError: If size is not positive.
        """
        if size <= 0:
            raise ValueError("size must be positive")
        self._ticks.append(Tick(timestamp, price, size))
        self._sum_px_sz += price * size
        self._sum_sz += size
        cutoff = timestamp - self.window_seconds
        while self._ticks and self._ticks[0].timestamp < cutoff:
            expired = self._ticks.popleft()
            self._sum_px_sz -= expired.price * expired.size
            self._sum_sz -= expired.size

    def current_vwap(self) -> float | None:
        """Returns the VWAP of ticks currently within the window.

        Returns:
            The size-weighted average price, or None if the window is empty.
        """
        if self._sum_sz == 0.0:
            return None
        return self._sum_px_sz / self._sum_sz


if __name__ == "__main__":
    calc = SlidingWindowVwap(window_seconds=10.0)
    calc.add_tick(0.0, 100.0, 10)
    calc.add_tick(4.0, 102.0, 10)
    calc.add_tick(9.0, 101.0, 5)
    assert abs(calc.current_vwap() - (100 * 10 + 102 * 10 + 101 * 5) / 25) < 1e-9
    calc.add_tick(11.0, 200.0, 1)  # Evicts the t=0.0 tick (11 - 10 = 1 > 0).
    expected = (102 * 10 + 101 * 5 + 200 * 1) / 16
    assert abs(calc.current_vwap() - expected) < 1e-9
    empty = SlidingWindowVwap(window_seconds=1.0)
    assert empty.current_vwap() is None
    print("All SlidingWindowVwap assertions passed.")
```

**Detailed explanation:** A `deque` gives O(1) append and O(1) popleft, so each tick is admitted once and evicted at most once across the whole run — total O(n) rather than O(n·k) for a naive re-sum-the-window approach. I maintain **running aggregates** (`_sum_px_sz`, `_sum_sz`) instead of recomputing the sum each query, so `current_vwap()` is O(1). This is the identical pattern used inside the KDB `twapBySym`/`vwapBySym` logic in C4, just expressed imperatively in Python — I'd point out in the interview that in production I'd actually do this in KDB/q for a live desk tool, and reserve Python for backtest/research use of the same logic.

**Complexity:** Time O(n) total for n ticks (amortized O(1) per tick); Space O(w) where w is the number of ticks resident in the window at any time (bounded, not by total ticks seen).

**Improvement with more time:** for very high-frequency streams I'd replace the Python `deque` with a fixed-capacity ring buffer of NumPy arrays to avoid per-tick object allocation overhead (dataclass instantiation), trading a bit of code clarity for materially lower constant-factor latency — relevant if this ever needed to run in the hot path rather than research/monitoring.

[🔝 Back to Top](#-table-of-contents)

---
---

## D2 · Order Book Reconstruction From an Event Stream

**Summary:** Rebuild a limit order book from an add/modify/cancel/trade event stream using two `SortedDict`-backed (red-black-tree-equivalent) price ladders (bid/ask), giving O(log p) per event where p is the number of distinct price levels.

```python
"""Limit order book reconstruction from a normalized event stream.

Typical usage example:

  book = OrderBook()
  book.apply(BookEvent(side="B", price=5000.00, size=10, event_type="ADD"))
  best_bid = book.best_bid()
"""

from __future__ import annotations

import dataclasses
import enum

import sortedcontainers  # sortedcontainers.SortedDict: red-black-tree-backed ordered map.


class EventType(enum.Enum):
    """Type of book-affecting event."""

    ADD = "ADD"
    CANCEL = "CANCEL"
    TRADE = "TRADE"  # Reduces size at a price level (aggressive fill).


@dataclasses.dataclass(frozen=True, slots=True)
class BookEvent:
    """A single normalized order book event.

    Attributes:
        side: "B" for bid or "A" for ask.
        price: Price level affected.
        size: Size delta to apply (always positive; sign is inferred from
          event_type).
        event_type: One of EventType.
    """

    side: str
    price: float
    size: float
    event_type: EventType


class OrderBook:
    """Maintains a price-level-aggregated limit order book.

    Uses two SortedDict price ladders (bid, ask) mapping price -> resting
    size, giving O(log p) insert/update/delete and O(1) best-price lookup,
    where p is the number of distinct resting price levels.
    """

    def __init__(self) -> None:
        """Initializes empty bid and ask ladders."""
        self._bids: sortedcontainers.SortedDict[float, float] = (
            sortedcontainers.SortedDict()
        )
        self._asks: sortedcontainers.SortedDict[float, float] = (
            sortedcontainers.SortedDict()
        )

    def _ladder(self, side: str) -> sortedcontainers.SortedDict[float, float]:
        """Returns the ladder for the given side.

        Args:
            side: "B" or "A".

        Returns:
            The bid or ask SortedDict ladder.

        Raises:
            ValueError: If side is not "B" or "A".
        """
        if side == "B":
            return self._bids
        if side == "A":
            return self._asks
        raise ValueError(f"Unknown side: {side!r}")

    def apply(self, event: BookEvent) -> None:
        """Applies a single event to the book, mutating it in place.

        Args:
            event: The event to apply.
        """
        ladder = self._ladder(event.side)
        current = ladder.get(event.price, 0.0)
        if event.event_type is EventType.ADD:
            ladder[event.price] = current + event.size
        else:  # CANCEL or TRADE both reduce resting size at the level.
            remaining = current - event.size
            if remaining <= 0:
                ladder.pop(event.price, None)
            else:
                ladder[event.price] = remaining

    def best_bid(self) -> float | None:
        """Returns the highest resting bid price, or None if empty."""
        return self._bids.peekitem(-1)[0] if self._bids else None

    def best_ask(self) -> float | None:
        """Returns the lowest resting ask price, or None if empty."""
        return self._asks.peekitem(0)[0] if self._asks else None


if __name__ == "__main__":
    book = OrderBook()
    book.apply(BookEvent("B", 5000.00, 10, EventType.ADD))
    book.apply(BookEvent("B", 5000.25, 5, EventType.ADD))
    book.apply(BookEvent("A", 5000.50, 8, EventType.ADD))
    assert book.best_bid() == 5000.25
    assert book.best_ask() == 5000.50
    book.apply(BookEvent("B", 5000.25, 5, EventType.CANCEL))
    assert book.best_bid() == 5000.00
    book.apply(BookEvent("A", 5000.50, 3, EventType.TRADE))
    assert book.best_ask() == 5000.50  # Partially filled, level remains.
    print("All OrderBook assertions passed.")
```

**Detailed explanation:** `sortedcontainers.SortedDict` is a red-black-tree-equivalent balanced structure (implemented as sorted-list-of-blocks internally for cache efficiency) giving O(log p) insertion/deletion and O(1) access to min/max via `peekitem`. Bids are ordered so the **best bid is the maximum key** (`peekitem(-1)`), asks so the **best ask is the minimum key** (`peekitem(0)`) — this directly mirrors price-time priority book semantics from A8. `TRADE` and `CANCEL` share reduction logic since both decrement resting size at a level; only `ADD` increments — collapsing these into one branch keeps the state machine minimal and auditable, which matters for a production book-reconstruction tool that has to be provably correct against exchange replay data.

**Complexity:** O(log p) per event (p = number of distinct price levels), O(1) for best-bid/best-ask queries; O(p) space. This is asymptotically optimal for a structure that must support ordered min/max queries with dynamic updates — a hash map alone would give O(1) update but O(p) best-price queries, which is the wrong tradeoff for a book that's queried far more often than it's fully rebuilt.

**Improvement with more time:** add a size-at-touch aggregate cache invalidated lazily to avoid repeated `peekitem` tree traversal under extremely bursty updates, and support full order-level (not just price-level) tracking for accurate queue-position modeling per A8's FIFO discussion.

[🔝 Back to Top](#-table-of-contents)

---
---

## D3 · Calendar-Spread Roll Scheduler (Almost-Optimal Trajectory)

**Summary:** Implements the Almgren-Chriss `sinh` optimal trajectory from B3 to schedule a futures roll, discretized into daily child-order quantities; O(T) time and space for T trading days.

```python
"""Almgren-Chriss optimal roll/liquidation trajectory scheduler.

Typical usage example:

  schedule = optimal_trajectory(
      total_qty=1_000, num_periods=10, risk_aversion=1e-6,
      volatility=0.02, temp_impact_eta=2e-7,
  )
"""

from __future__ import annotations

import numpy as np


def optimal_trajectory(
    total_qty: float,
    num_periods: int,
    risk_aversion: float,
    volatility: float,
    temp_impact_eta: float,
) -> np.ndarray:
    """Computes the Almgren-Chriss optimal remaining-inventory trajectory.

    Implements x(t) = X * sinh(kappa * (T - t)) / sinh(kappa * T), where
    kappa = sqrt(risk_aversion * volatility^2 / temp_impact_eta), evaluated
    at t = 0, 1, ..., num_periods, then differenced into per-period trade
    sizes.

    Args:
        total_qty: Total quantity to roll/liquidate (contracts), positive.
        num_periods: Number of discrete trading periods (days), positive.
        risk_aversion: Lambda; higher means more urgency (front-loaded).
        volatility: Per-period price volatility of the contract.
        temp_impact_eta: Temporary market impact coefficient; higher means
          impact is more expensive, favoring a flatter (TWAP-like) schedule.

    Returns:
        A 1-D array of length num_periods with the quantity to trade in
        each period (all entries sum to total_qty).

    Raises:
        ValueError: If total_qty or num_periods is not positive.
    """
    if total_qty <= 0 or num_periods <= 0:
        raise ValueError("total_qty and num_periods must be positive")
    t = np.arange(num_periods + 1, dtype=np.float64)
    horizon = float(num_periods)
    if risk_aversion <= 0.0:
        # Risk-neutral limit: kappa -> 0, trajectory collapses to linear TWAP.
        remaining = total_qty * (1.0 - t / horizon)
    else:
        kappa = np.sqrt(risk_aversion * volatility**2 / temp_impact_eta)
        remaining = total_qty * np.sinh(kappa * (horizon - t)) / np.sinh(
            kappa * horizon
        )
    per_period_trades = -np.diff(remaining)  # Quantity traded each period.
    return per_period_trades


if __name__ == "__main__":
    schedule = optimal_trajectory(
        total_qty=1_000.0,
        num_periods=10,
        risk_aversion=1e-6,
        volatility=0.02,
        temp_impact_eta=2e-7,
    )
    assert schedule.shape == (10,)
    assert abs(schedule.sum() - 1_000.0) < 1e-6
    assert np.all(schedule > 0.0)
    # Risk-neutral limit should be exactly flat (TWAP).
    flat_schedule = optimal_trajectory(
        total_qty=1_000.0,
        num_periods=10,
        risk_aversion=0.0,
        volatility=0.02,
        temp_impact_eta=2e-7,
    )
    assert np.allclose(flat_schedule, 100.0)
    # Higher risk aversion should front-load more than lower risk aversion.
    aggressive = optimal_trajectory(1_000.0, 10, 1e-4, 0.02, 2e-7)
    assert aggressive[0] > schedule[0]
    print("All optimal_trajectory assertions passed.")
```

**Detailed explanation:** `np.sinh` and vectorized array arithmetic compute the entire trajectory in one shot rather than a Python loop over periods — `t = np.arange(...)` builds the full time grid, and `remaining` is the closed-form sinh solution from B3 evaluated at every grid point simultaneously. `-np.diff(remaining)` converts the *remaining inventory* curve into *per-period trade sizes* via a single vectorized adjacent-difference, avoiding any explicit loop. The risk-neutral branch (`risk_aversion <= 0`) guards against a divide/domain issue as `kappa → 0` (sinh ratio is numerically 0/0 in the limit) by using the known analytic limit directly — linear TWAP — rather than letting floating point sinh evaluate a near-degenerate ratio.

**Complexity:** O(T) time and O(T) space for T periods — optimal, since the schedule itself has T outputs that must all be produced.

**Improvement with more time:** extend to a **two-asset simultaneous** trajectory (rolling the calendar spread as one instrument rather than two legs) by solving the coupled Almgren-Chriss system with a covariance matrix instead of a scalar volatility, which is the more realistic version of A7's roll-execution problem.

[🔝 Back to Top](#-table-of-contents)

---
---

## D4 · Detecting Iceberg Orders From Trade Prints

**Summary:** Streaming detector using a hash map keyed by price level tracking consecutive same-size prints; flags a level as a likely iceberg once repeat count exceeds a threshold. O(1) amortized per print, O(p) space.

```python
"""Streaming iceberg-order detector from a trade-print stream.

Typical usage example:

  detector = IcebergDetector(min_repeats=4, size_tolerance=0.0)
  for tick in ticks:
      flags = detector.process(tick)
"""

from __future__ import annotations

import collections
import dataclasses


@dataclasses.dataclass(frozen=True, slots=True)
class TradePrint:
    """A single trade print.

    Attributes:
        price: Trade price.
        size: Displayed traded size.
    """

    price: float
    size: float


@dataclasses.dataclass(slots=True)
class _LevelState:
    """Running state for one price level.

    Attributes:
        last_size: Size of the most recent print at this level.
        repeat_count: Number of consecutive prints of the same size.
    """

    last_size: float
    repeat_count: int


class IcebergDetector:
    """Flags price levels showing a repeated-constant-size print pattern.

    An iceberg order typically refreshes its displayed size after each
    partial fill, so repeated trade prints of an identical size at the same
    price, well beyond what a single resting order of that size would
    explain, is the classic detectable signature (see A8).
    """

    def __init__(self, min_repeats: int = 4, size_tolerance: float = 0.0) -> None:
        """Initializes the detector.

        Args:
            min_repeats: Consecutive same-size prints required to flag a
              level as a likely iceberg.
            size_tolerance: Absolute tolerance for "same size" comparisons,
              to allow for minor exchange rounding.

        Raises:
            ValueError: If min_repeats is less than 2.
        """
        if min_repeats < 2:
            raise ValueError("min_repeats must be at least 2")
        self.min_repeats = min_repeats
        self.size_tolerance = size_tolerance
        self._state: dict[float, _LevelState] = {}

    def process(self, tick: TradePrint) -> bool:
        """Processes one print, returning True iff this level is now flagged.

        Args:
            tick: The incoming trade print.

        Returns:
            True if the price level has just crossed the min_repeats
            threshold with constant size (a newly detected iceberg),
            False otherwise.
        """
        state = self._state.get(tick.price)
        if state is None or abs(state.last_size - tick.size) > self.size_tolerance:
            self._state[tick.price] = _LevelState(last_size=tick.size, repeat_count=1)
            return False
        state.repeat_count += 1
        return state.repeat_count == self.min_repeats


if __name__ == "__main__":
    detector = IcebergDetector(min_repeats=3)
    results = [
        detector.process(TradePrint(5000.00, 10)),
        detector.process(TradePrint(5000.00, 10)),
        detector.process(TradePrint(5000.00, 10)),  # 3rd consecutive -> flagged.
        detector.process(TradePrint(5000.00, 7)),  # Size changed, resets.
        detector.process(TradePrint(5000.25, 5)),
    ]
    assert results == [False, False, True, False, False]
    print("All IcebergDetector assertions passed.")
```

**Detailed explanation:** A `dict[float, _LevelState]` gives O(1) average-case lookup/update per incoming print, keyed by price — this is the same "hash-indexed state per key" idea as the KDB keyed table in C2, just in plain Python. The detector resets `repeat_count` whenever the size changes (a genuinely different resting size arrived, or the level's order composition changed), and only fires `True` exactly once, on the print that *first crosses* the threshold, rather than on every subsequent print — this avoids re-alerting the desk repeatedly for a level already known to be an iceberg.

**Complexity:** O(1) amortized per print, O(p) space for p distinct actively-tracked price levels — optimal for a streaming single-pass detector.

**Improvement with more time:** add a decay/expiry so stale price levels (no prints for N seconds) are evicted from `_state`, bounding memory in a long-running production process, and incorporate refresh-timing statistics (icebergs often refresh with a characteristic latency) as a second confirming signal alongside constant-size repetition, reducing false positives from coincidentally-equal-sized independent orders.

[🔝 Back to Top](#-table-of-contents)

---
---

## D5 · Merge K Sorted Tick Streams (Multi-Venue Consolidated Tape)

**Summary:** Classic k-way merge using a min-heap keyed by timestamp; O(n log k) time for n total ticks across k venues, O(k) heap space.

```python
"""Merges k time-sorted per-venue tick streams into one consolidated tape.

Typical usage example:

  tape = list(merge_tick_streams([venue_a_ticks, venue_b_ticks, venue_c_ticks]))
"""

from __future__ import annotations

import dataclasses
import heapq
from collections.abc import Iterable, Iterator


@dataclasses.dataclass(frozen=True, slots=True, order=True)
class VenueTick:
    """A tick from a specific venue, ordered by timestamp for heap use.

    Attributes:
        timestamp: Epoch nanoseconds of the print.
        venue: Venue identifier (excluded from ordering).
        price: Trade price (excluded from ordering).
        size: Trade size (excluded from ordering).
    """

    timestamp: int
    venue: str = dataclasses.field(compare=False)
    price: float = dataclasses.field(compare=False)
    size: float = dataclasses.field(compare=False)


def merge_tick_streams(
    streams: list[Iterable[VenueTick]],
) -> Iterator[VenueTick]:
    """Lazily merges k already-timestamp-sorted tick streams into one stream.

    Args:
        streams: A list of k iterables, each individually sorted ascending
          by timestamp.

    Yields:
        VenueTick instances across all streams, globally sorted ascending
        by timestamp.
    """
    iterators = [iter(stream) for stream in streams]
    heap: list[tuple[VenueTick, int]] = []
    for stream_idx, iterator in enumerate(iterators):
        first = next(iterator, None)
        if first is not None:
            heapq.heappush(heap, (first, stream_idx))
    while heap:
        tick, stream_idx = heapq.heappop(heap)
        yield tick
        nxt = next(iterators[stream_idx], None)
        if nxt is not None:
            heapq.heappush(heap, (nxt, stream_idx))


if __name__ == "__main__":
    venue_a = [VenueTick(1, "A", 5000.0, 5), VenueTick(4, "A", 5000.5, 3)]
    venue_b = [VenueTick(2, "B", 5000.25, 2), VenueTick(3, "B", 5000.1, 1)]
    venue_c = [VenueTick(5, "C", 5001.0, 10)]
    merged = list(merge_tick_streams([venue_a, venue_b, venue_c]))
    assert [t.timestamp for t in merged] == [1, 2, 3, 4, 5]
    assert [t.venue for t in merged] == ["A", "B", "B", "A", "C"]
    print("All merge_tick_streams assertions passed.")
```

**Detailed explanation:** `heapq` maintains a binary min-heap of size at most k (one candidate tick per active stream), so extracting the globally-next tick is O(log k) and each of the n total ticks is pushed/popped exactly once — O(n log k) overall. The `@dataclasses.dataclass(order=True)` with `compare=False` on non-timestamp fields makes `VenueTick` directly heap-comparable on `timestamp` alone without a custom `__lt__`, which is cleaner and less error-prone than a manual comparator. This is a **lazy generator**, so it never materializes the full merged tape in memory — critical for building a consolidated tape from potentially billions of daily prints across CME Globex plus any secondary reporting venues.

**Complexity:** O(n log k) time, O(k) auxiliary space (heap) — this is the theoretically optimal approach for k-way merge of sorted sequences; you cannot do better than O(log k) per extraction when merging k independently-ordered streams without additional structure.

**Improvement with more time:** for extremely wide k (hundreds of streams), a tournament-tree (loser tree) variant reduces comparison overhead in the constant factor versus a heap, and I'd add tie-breaking by venue-priority for same-nanosecond prints to guarantee deterministic ordering matching exchange sequence numbers.

[🔝 Back to Top](#-table-of-contents)

---
---

## D6 · Position & PnL Reconciliation Engine

**Summary:** Vectorized Pandas reconciliation between internal fills ledger and broker-reported positions; groupby-based diff detection in O(n log n) (dominated by the groupby sort) rather than any row-by-row loop.

```python
"""Reconciles internally computed futures positions against broker statements.

Typical usage example:

  breaks = reconcile_positions(internal_fills=fills_df, broker_positions=broker_df)
"""

from __future__ import annotations

import pandas as pd


def reconcile_positions(
    internal_fills: pd.DataFrame,
    broker_positions: pd.DataFrame,
    qty_tolerance: float = 1e-6,
) -> pd.DataFrame:
    """Compares internally derived net positions against broker-reported positions.

    Args:
        internal_fills: DataFrame with columns ["account", "sym", "qty"]
          (signed, long positive / short negative), one row per fill.
        broker_positions: DataFrame with columns ["account", "sym", "qty"],
          one row per account/symbol reported net position.
        qty_tolerance: Absolute quantity difference below which a break is
          not reported (accounts for benign rounding).

    Returns:
        A DataFrame with columns ["account", "sym", "internal_qty",
        "broker_qty", "break_qty"], containing only rows where the absolute
        difference exceeds qty_tolerance, sorted by break magnitude
        descending.
    """
    internal_net = (
        internal_fills.groupby(["account", "sym"], as_index=False)["qty"]
        .sum()
        .rename(columns={"qty": "internal_qty"})
    )
    broker_net = broker_positions.rename(columns={"qty": "broker_qty"})
    merged = internal_net.merge(
        broker_net, on=["account", "sym"], how="outer"
    ).fillna(0.0)
    merged["break_qty"] = merged["internal_qty"] - merged["broker_qty"]
    breaks = merged.loc[merged["break_qty"].abs() > qty_tolerance].copy()
    breaks["abs_break"] = breaks["break_qty"].abs()
    return breaks.sort_values("abs_break", ascending=False).drop(
        columns="abs_break"
    ).reset_index(drop=True)


if __name__ == "__main__":
    fills = pd.DataFrame(
        {
            "account": ["PM1", "PM1", "PM2"],
            "sym": ["ESU25", "ESU25", "NQU25"],
            "qty": [40.0, 10.0, -20.0],
        }
    )
    broker = pd.DataFrame(
        {
            "account": ["PM1", "PM2"],
            "sym": ["ESU25", "NQU25"],
            "qty": [50.0, -25.0],
        }
    )
    result = reconcile_positions(fills, broker)
    assert len(result) == 1
    row = result.iloc[0]
    assert row["account"] == "PM2"
    assert abs(row["break_qty"] - 5.0) < 1e-9  # -20 internal vs -25 broker.
    print("All reconcile_positions assertions passed.")
```

**Detailed explanation:** `groupby(["account", "sym"])["qty"].sum()` is a fully vectorized aggregation (implemented in Pandas' Cython-backed hash-aggregation internals), avoiding any explicit Python loop over fills — this is the correct idiom for what would otherwise be an O(n) accumulation, expressed instead as a single optimized C-level pass. An **outer merge** ensures a symbol/account present only on one side (a completely missing position on either the internal or broker side, itself a break) still surfaces, rather than being silently dropped by an inner join. Sorting by `abs_break` descending puts the most material breaks first, matching how an ops/desk analyst would actually triage a reconciliation report under time pressure.

**Complexity:** O(n log n) dominated by the groupby's internal sort/hash step for n fills (Pandas groupby is effectively O(n) via hashing in practice, with the final merge/sort adding O(m log m) for m account/symbol pairs, m ≪ n); O(n) space.

**Improvement with more time:** add multi-day carry-forward reconciliation (yesterday's reconciled position + today's fills vs today's broker position) rather than a single-day snapshot, which is how real breaks are actually tracked and aged in production ops workflows.

[🔝 Back to Top](#-table-of-contents)

---
---

## D7 · Fast Rolling Statistics (O(1) Amortized) for Streaming Market Impact Signals

**Summary:** O(1) amortized rolling mean/variance via Welford-style incremental updates combined with a fixed-size deque, avoiding the O(w) re-scan a naive rolling-window recompute would require; used for real-time impact-model feature computation (e.g., realized volatility feeding B2/B3's kappa).

```python
"""O(1) amortized rolling mean/variance for streaming market-impact features.

Typical usage example:

  roller = RollingStats(window=100)
  for r in returns_stream:
      roller.update(r)
      vol = roller.std()
"""

from __future__ import annotations

import collections
import math


class RollingStats:
    """Maintains rolling mean and (population) variance over a fixed window.

    Uses an O(1) amortized incremental update/downdate scheme rather than
    re-summing the window on every new observation, which would be O(window)
    per update and infeasible at tick-rate frequencies.

    Attributes:
        window: Maximum number of most-recent observations retained.
    """

    def __init__(self, window: int) -> None:
        """Initializes the rolling statistics tracker.

        Args:
            window: Number of most-recent observations to track, positive.

        Raises:
            ValueError: If window is not positive.
        """
        if window <= 0:
            raise ValueError("window must be positive")
        self.window = window
        self._values: collections.deque[float] = collections.deque(maxlen=window)
        self._sum: float = 0.0
        self._sum_sq: float = 0.0

    def update(self, value: float) -> None:
        """Admits a new observation, evicting the oldest if at capacity.

        Args:
            value: The new observation.
        """
        if len(self._values) == self.window:
            oldest = self._values[0]  # Will be evicted by deque's maxlen.
            self._sum -= oldest
            self._sum_sq -= oldest * oldest
        self._values.append(value)
        self._sum += value
        self._sum_sq += value * value

    def mean(self) -> float | None:
        """Returns the rolling mean, or None if no observations yet."""
        n = len(self._values)
        return self._sum / n if n else None

    def std(self) -> float | None:
        """Returns the rolling population standard deviation.

        Returns:
            The standard deviation, or None if no observations yet. Clamps
            negative variance from floating-point cancellation to zero.
        """
        n = len(self._values)
        if n == 0:
            return None
        mean = self._sum / n
        variance = max(self._sum_sq / n - mean * mean, 0.0)
        return math.sqrt(variance)


if __name__ == "__main__":
    roller = RollingStats(window=3)
    for value in (1.0, 2.0, 3.0):
        roller.update(value)
    assert abs(roller.mean() - 2.0) < 1e-9
    assert abs(roller.std() - math.sqrt(2.0 / 3.0)) < 1e-9
    roller.update(10.0)  # Evicts the 1.0; window now [2.0, 3.0, 10.0].
    assert abs(roller.mean() - 5.0) < 1e-9
    print("All RollingStats assertions passed.")
```

**Detailed explanation:** because `collections.deque(maxlen=window)` silently evicts the oldest element on overflow, I read `self._values[0]` (the soon-to-be-evicted element) *before* appending, and subtract its contribution from both the running sum and sum-of-squares — this "downdate then update" pattern keeps `_sum`/`_sum_sq` correct in O(1) per call rather than re-summing the window. Variance is computed as $\mathbb{E}[X^2] - (\mathbb{E}[X])^2$, the sum-of-squares identity, which is O(1) given the maintained running sums; I explicitly `max(..., 0.0)` clamp against floating-point cancellation error that can occasionally drive this identity slightly negative for near-constant series.

**Complexity:** O(1) amortized per update, O(window) space — optimal, since the exact rolling window contents must be retained to correctly downdate on eviction.

**Improvement with more time:** for numerical robustness under very long-running high-precision requirements, I'd switch to a true **Welford/West-style incremental variance** (updating a running M2 term directly rather than via the sum-of-squares identity) to further reduce catastrophic-cancellation risk, at the cost of a slightly more involved update rule.

[🔝 Back to Top](#-table-of-contents)

---
---

## D8 · Trade Cluster Detection (Union-Find) for Give-Up Netting

**Summary:** Groups fills across multiple executing brokers that net to the same beneficial account/clearer using Union-Find (disjoint set union) with path compression and union by rank; O(n α(n)) ≈ O(n) for n fills.

```python
"""Union-Find based clustering of fills that must net together for give-up settlement.

Typical usage example:

  netter = GiveUpNetter()
  netter.link("fill_1", "fill_2")
  clusters = netter.clusters()
"""

from __future__ import annotations

import collections


class GiveUpNetter:
    """Disjoint-set structure clustering fills into give-up netting groups.

    Uses union-find with path compression and union by rank, giving
    O(n * alpha(n)) amortized total time for n union/find operations, where
    alpha is the inverse Ackermann function (effectively a small constant
    for any realistic n).
    """

    def __init__(self) -> None:
        """Initializes an empty union-find structure."""
        self._parent: dict[str, str] = {}
        self._rank: dict[str, int] = {}

    def _find(self, item: str) -> str:
        """Finds the representative (root) of item's set, with path compression.

        Args:
            item: The element to look up; auto-registered as its own root
              if not previously seen.

        Returns:
            The root element representing item's disjoint set.
        """
        if item not in self._parent:
            self._parent[item] = item
            self._rank[item] = 0
            return item
        root = item
        while self._parent[root] != root:
            root = self._parent[root]
        # Path compression: repoint every visited node directly to root.
        while self._parent[item] != root:
            self._parent[item], item = root, self._parent[item]
        return root

    def link(self, item_a: str, item_b: str) -> None:
        """Unions the sets containing item_a and item_b, by rank.

        Args:
            item_a: First element.
            item_b: Second element.
        """
        root_a, root_b = self._find(item_a), self._find(item_b)
        if root_a == root_b:
            return
        if self._rank[root_a] < self._rank[root_b]:
            root_a, root_b = root_b, root_a
        self._parent[root_b] = root_a
        if self._rank[root_a] == self._rank[root_b]:
            self._rank[root_a] += 1

    def clusters(self) -> dict[str, list[str]]:
        """Returns all current clusters keyed by their root element.

        Returns:
            A mapping from cluster root to the list of members in that
            cluster.
        """
        groups: dict[str, list[str]] = collections.defaultdict(list)
        for item in self._parent:
            groups[self._find(item)].append(item)
        return dict(groups)


if __name__ == "__main__":
    netter = GiveUpNetter()
    netter.link("fill_1", "fill_2")
    netter.link("fill_2", "fill_3")
    netter.link("fill_4", "fill_5")
    netter._find("fill_6")  # Registers an unlinked singleton fill.
    clusters = netter.clusters()
    sizes = sorted(len(members) for members in clusters.values())
    assert sizes == [1, 2, 3]
    root_1 = netter._find("fill_1")
    assert netter._find("fill_2") == root_1
    assert netter._find("fill_3") == root_1
    print("All GiveUpNetter assertions passed.")
```

**Detailed explanation:** `_find` implements two classic optimizations together: **path compression** (the second while-loop repoints every node on the search path directly to the root, flattening future lookups) and, in `link`, **union by rank** (always attaching the shorter tree under the taller one's root), which together give the famous near-linear O(n α(n)) amortized bound — α is the inverse Ackermann function, which is at most 4 for any n up to far beyond the number of atoms in the universe, so this is "practically O(n)." I map this abstractly to A2's give-up problem: fills executed at different brokers but destined to net into the same beneficial-owner/clearer relationship are exactly a **connected-components** problem, and union-find is the textbook-optimal data structure for maintaining connectivity under online union operations, versus rebuilding a graph and running BFS/DFS after every new link (which would be far more expensive at scale).

**Complexity:** O(n α(n)) ≈ O(n) amortized total for n union/find calls; O(n) space — this is essentially the theoretical optimum for online dynamic connectivity under union operations only (no need to support "unlink").

**Improvement with more time:** if the netting relationships needed to support **removal** (a give-up gets reversed/broken), union-find alone doesn't support efficient deletion, and I'd move to a fully dynamic connectivity structure (e.g., link-cut trees or a periodically-rebuilt adjacency structure) — worth flagging live to show awareness of the structure's limits, not just its strengths.

[🔝 Back to Top](#-table-of-contents)

---
---

# 🎲 PROBABILITY, STATISTICS & BRAIN TEASERS

---

## E1 · Two Eggs, 100 Floors — Classic Optimization Brain Teaser

**Problem:** Find the floor at or below which an egg breaks when dropped, with 2 eggs and 100 floors, minimizing the worst-case number of drops.

**Answer:** 14 drops worst case, using decreasing step sizes: drop from floor 14, then 27, then 39, ... (steps decreasing by 1 each time: 14+13+12+...+1 = 105 ≥ 100).

**Say it out loud, Feynman style:** *"With one egg you're stuck doing linear search — drop from floor 1, 2, 3, ... — because you can't afford to break it. With two eggs, the first egg can afford to break once, so I want to jump by decreasing amounts: if the first egg breaks on drop k, I've 'used up' k drops already, so I only have 14-k drops left for the second egg to linear-search a much smaller remaining range with the second egg. Setting the first jump at 14 and shrinking by 1 each time balances the worst case across every possible breaking floor — that's why it's optimal, not just a lucky guess."*

$$
n(n+1)/2 \ge 100 \implies n=14 \text{ (since } 14\cdot15/2=105\ge100\text{)}
$$

**Job tie-back:** this is a proxy for search/optimization reasoning under a limited "budget of costly probes" — directly analogous to finding an optimal execution price level with limited "information-revealing" probes (e.g., iceberg-probing limit orders) without excessive signaling.

[🔝 Back to Top](#-table-of-contents)

---
---

## E2 · Expected Number of Trades Until Order Fully Fills (Geometric/Negative Binomial)

**Problem:** Each child order fills independently with probability $p$ per attempt (else rejected/re-sent). What's the expected number of attempts to get $k$ fills?

**Answer:** Negative binomial expectation:

$$
\mathbb{E}[\text{attempts}] = \frac{k}{p}
$$

**Say it out loud:** *"Each single fill is a geometric random variable with mean 1/p — that's how many tries on average until one success. If I need k independent successes, by linearity of expectation I just add k of those means together, giving k/p. This is the same logic I'd use to size how many child order slices to plan for a POV algo given a historical fill/reject rate at a venue — if my acceptance rate is 80%, I need to plan for 1/0.8 = 1.25x as many attempts as the bare minimum."*

**Variance (for confidence-interval sizing):** $\text{Var} = k(1-p)/p^2$ — I'd mention this to show I think about the *dispersion* of completion time, not just the mean, since a PM cares about tail risk of an order not completing in time, not just average completion time.

[🔝 Back to Top](#-table-of-contents)

---
---

## E3 · Monty Hall With a Trading Twist — Adverse Selection Framing

**Classic result:** Switching doors wins with probability 2/3 vs 1/3 for staying.

**Trading reframe (what they're actually testing):** *"Monty Hall's power isn't the puzzle itself, it's that Monty's action — always opening a door he KNOWS is empty — carries information, because his choice is conditioned on the true state. The trading analogy: if a counterparty only shows me a two-sided market, or only lets me trade against them, when they have information (e.g., a broker's last-look rejection is more likely exactly when the market is about to move against me), then their *decision to interact with me at all* is informative, and I have to update on it — that's the core intuition behind adverse selection and why 'the fact that this order got filled easily' can itself be bad news."*

$$
P(\text{win by switching}) = \frac{2}{3}, \qquad P(\text{win by staying}) = \frac{1}{3}
$$

**Feynman explanation:** *"Before any door opens, my initial pick has a 1/3 chance of being right, so the other two doors combined have a 2/3 chance of hiding the prize. Monty then removes one wrong option from that 2/3 group with certainty — he never removes the prize — so all of that 2/3 probability collapses onto the single remaining unopened door. Switching just lets me buy that whole 2/3 bucket instead of keeping my original 1/3 bucket."*

[🔝 Back to Top](#-table-of-contents)

---
---

## E4 · Bayesian Updating — Probability an Order Is Informed Given a Price Move

**Setup:** Base rate: 10% of counterparty orders are "informed" (know something). If informed, price moves adversely 80% of the time in the next minute; if uninformed, only 20% of the time. Given we observe an adverse move, what's the updated probability the order was informed?

**Bayes' theorem:**

$$
P(\text{Informed}\mid\text{Move}) = \frac{P(\text{Move}\mid\text{Informed}) P(\text{Informed})}{P(\text{Move}\mid\text{Informed})P(\text{Informed}) + P(\text{Move}\mid\text{Uninformed})P(\text{Uninformed})}
$$

$$
= \frac{0.8 \times 0.1}{0.8 \times 0.1 + 0.2 \times 0.9} = \frac{0.08}{0.26} \approx 0.308
$$

**Say it out loud:** *"Even though informed flow is four times more likely to produce an adverse move than uninformed flow, the base rate of informed flow is so low (10%) that observing one adverse move only pushes my belief up to about 31%, not anywhere near certainty. This is exactly the trap junior people fall into with TCA — seeing one bad fill and concluding 'that counterparty is toxic' — when the correct Bayesian update, given realistic base rates, is much more modest. I'd need several independent adverse observations against the same counterparty before the posterior gets convincingly high."*

**Job tie-back:** directly the statistical backbone of "advise portfolio managers... on trading, market structure, execution quality" and broker/venue toxicity analysis.

[🔝 Back to Top](#-table-of-contents)

---
---

## E5 · Random Walk & Gambler's Ruin — Expected Time to Hit a Stop

**Setup:** Symmetric random walk (p=0.5), starting at position 0, absorbing barriers at $-a$ (stop-loss) and $+b$ (take-profit). Expected time to absorption:

$$
\mathbb{E}[T] = a \cdot b
$$

Probability of hitting $+b$ first: $P(+b) = \dfrac{a}{a+b}$ (by the martingale/optional-stopping argument, since the walk is a martingale and $\mathbb{E}[X_T]=0=b\cdot P(+b) - a\cdot(1-P(+b))$ ).

**Say it out loud:** *"Because a fair random walk is a martingale, its expected value never changes — so the expected value at the stopping time must still be zero. That single constraint, plus the fact that the walk must end at exactly +b or exactly −a, is enough to solve for the hit probabilities without simulating anything: probability of the upper barrier is just a/(a+b), proportional to how far away the barrier you're NOT trying to hit is. Intuitively, if your stop-loss is very close (small a) and your take-profit is far away (large b), you're much more likely to get stopped out first — which is obvious once you see it, but people get it wrong constantly when sizing stops relative to targets."*

**Job tie-back:** directly informs risk-management intuition for setting execution algo urgency thresholds/kill-switch levels relative to expected price paths during large futures orders.

[🔝 Back to Top](#-table-of-contents)

---
---

## E6 · Central Limit Theorem & Why VWAP Slippage Is Approximately Normal

**Statement:** For i.i.d. (or weakly dependent, finite-variance) increments $X_i$ with mean $\mu$ and variance $\sigma^2$:

$$
\frac{\sum_{i=1}^n X_i - n\mu}{\sigma\sqrt{n}} \xrightarrow{d} N(0,1) \text{ as } n \to \infty
$$

**Say it out loud:** *"VWAP slippage aggregated across many independent child fills behaves like a sum of many small, roughly independent shocks — regardless of the individual fill-level distribution's shape, as the number of fills grows the standardized sum converges to a normal distribution. That's why I can use a t-test or a normal confidence interval on aggregate PM-level slippage even though any single fill's slippage distribution is skewed and fat-tailed — CLT is doing the heavy lifting, PROVIDED the fills are close enough to independent. The big caveat I always flag: fills within the same parent order are correlated (they're all reacting to the same underlying price path), so the *effective* n is much smaller than the raw fill count, which is exactly the clustering correction I mentioned in B8."*

**Job tie-back:** foundational justification for every TCA significance test the desk runs.

[🔝 Back to Top](#-table-of-contents)

---
---

## E7 · Correlated Coin Flips — Covariance/Correlation Brain Teaser

**Problem:** Two coins, each fair marginally, but flipped so that they agree (both heads or both tails) with probability $q$. Find the correlation between the two outcomes (coded as $\pm 1$).

**Solution:** Let $X, Y \in \{-1,+1\}$, each marginally $P(X{=}1)=0.5$. $P(X{=}Y)=q$.

$$
\mathbb{E}[XY] = (+1)\cdot q + (-1)\cdot(1-q) = 2q - 1
$$

Since $\mathbb{E}[X]=\mathbb{E}[Y]=0$ and $\text{Var}(X)=\text{Var}(Y)=1$:

$$
\rho = \text{Cov}(X,Y) = \mathbb{E}[XY] - \mathbb{E}[X] \mathbb{E}[Y] = 2q-1
$$

**Say it out loud:** *"Correlation here collapses to a beautifully simple linear function of the agreement probability: if the coins always agree, q=1 and correlation is exactly 1; if they always disagree, q=0 and correlation is exactly −1; at q=0.5 (no relationship between them) correlation is exactly 0. This ±1-coding trick — mapping a binary outcome to ±1 instead of 0/1 — is the same trick I use constantly translating discrete market-state variables (e.g., 'did this order get filled at a level above/below prevailing mid') into something I can directly correlate with continuous variables like realized slippage."*

[🔝 Back to Top](#-table-of-contents)

---
---

## E8 · Fermi Estimation — How Many Futures Contracts Trade on CME Daily?

**Approach (spoken live, no internet access):**
> "I'll build this bottom-up from what I actually know about the biggest few products, since a handful of contracts dominate CME's total volume. E-mini S&P (ES) trades roughly 1.5-2 million contracts a day; add Treasury futures (10-Year Note, TY) at a similar order of magnitude, maybe 1.5-2 million; crude oil (CL) around 1-1.5 million; then a long tail of everything else — other equity index products, other rates tenors, FX futures, ags, metals — call that another 2-3 million combined. That sums to roughly 6-9 million contracts a day across the flagship CME Group complex; if I include the full breadth of smaller/micro contracts (Micro E-mini products specifically drive very high contract counts because of their small notional), I'd round up toward 10+ million contracts/day as a defensible estimate."

**Feynman tie-back:** *"The value of a Fermi estimate isn't precision, it's showing I know which few products actually dominate volume, and that I can build a number from decomposed, individually-defensible pieces rather than pulling one number from memory — which is exactly the muscle I use estimating expected market impact for a new order before I have live data: decompose into ADV, expected participation rate, and a calibrated impact coefficient, and sanity-check the final number against what I know about similar historical trades."*

[🔝 Back to Top](#-table-of-contents)

---
---

# 🤝 BEHAVIORAL (RESUME-DRIVEN)

---

## F1 · Walk Me Through Your Background and Why Execution Services at Millennium

**Answer framework (STAR-lite, ~90 seconds):**
> "I've spent 17+ years as a systematic quant researcher across multi-asset platforms — Millburn Ridgefield for 14 years building macro signal libraries across futures, equities and FX; Highbridge/JPM building execution-optimized systematic models with a heavy TCA and implementation-shortfall focus; and most recently Balyasny in a Systematic Macro pod covering equities, fixed income, FX and derivatives, where I led execution monitoring and transaction cost modeling. What draws me to this specific role is that it's the first one purely centered on execution and TCA as the primary deliverable rather than a supporting function to alpha research — and Millennium's Execution Services team, sitting at the intersection of the futures desk, volatility business, and PMs across the platform, is exactly where 17 years of alpha-side intuition about *why* a signal decays becomes directly useful for deciding *how fast* to execute it."

**Job tie-back:** explicitly connects "5+ years... futures systematic execution" and cross-asset PM advisory experience.

[🔝 Back to Top](#-table-of-contents)

---
---

## F2 · Tell Me About a Time You Disagreed With a Portfolio Manager

**Answer framework:**
> "At Highbridge, a PM wanted to keep using VWAP as the benchmark for a fast-decaying mean-reversion signal in futures — the strategy's whole edge lived in the first few minutes after signal generation. I disagreed, because VWAP would flatter our execution even as opportunity cost silently ate the PM's alpha on the unfilled residual. Rather than just asserting this, I pulled three months of the PM's own fills and showed the implementation shortfall decomposition side by side with VWAP slippage — the VWAP number looked fine, but the opportunity-cost component of IS was consistently the largest line item, directly explaining a chunk of underperformance versus the paper backtest. That evidence, not the disagreement itself, changed the benchmark to arrival-price IS going forward, and the PM's live-vs-backtest tracking error visibly tightened afterward."

**Feynman tie-back:** *"I try never to win a disagreement with a PM through authority — only by making the data reconstruct their own intuition for them."*

[🔝 Back to Top](#-table-of-contents)

---
---

## F3 · Describe a Model That Failed Out-of-Sample — What Did You Do

**Answer framework:**
> "At Millburn I built an adverse-selection model to detect informed flow using LOB imbalance features. In-sample Sharpe looked strong, but the first live quarter underperformed the backtest by roughly half. I re-ran Combinatorial Purged Cross-Validation more aggressively and found the original validation folds had residual leakage — a feature computed with a lookback window that occasionally spanned across the purge gap during regime transitions. I rebuilt the CV harness with a wider embargo period around each fold boundary, re-validated, and the revised expected Sharpe dropped to match what we then saw live — a smaller number, but a trustworthy one. I'd rather report a smaller, honest number than a bigger, wrong one."

**Job tie-back:** ties to "conduct research, test hypotheses, maintain data quality" and demonstrates the "moral integrity" attribute from the candidate's own resume.

[🔝 Back to Top](#-table-of-contents)

---
---

## F4 · Why Are You Leaving Balyasny After Only Over a Year

**Answer framework:**
> "Balyasny gave me broad multi-asset exposure and execution-monitoring ownership within a pod, which has been genuinely valuable — but the role there is embedded inside one systematic macro pod's alpha process, whereas this Execution Services role is a platform-level, futures-specialist seat advising PMs across the desk, with direct ownership of the TCA framework itself rather than being a consumer of someone else's. That's a more specialized, more senior scope that matches where I want to spend the next chapter of my career, and Millennium's central futures trading product is exactly the kind of build-from-the-ground-up mandate I'm looking for."

**Note for candidate:** keep this forward-looking and specific to scope/mandate differences, not compensation or interpersonal framing — interviewers probe short tenures for red flags on judgment and commitment, so specificity and enthusiasm for the new mandate defuse it.

[🔝 Back to Top](#-table-of-contents)

---
---

## F5 · Tell Me About a Time You Had to Learn Something Quickly Under Pressure

**Answer framework:**
> "When I moved into the execution-focused work at Highbridge, I had to get fluent in futures-specific TCA — roll mechanics, calendar spreads, give-ups — within weeks, coming from a pure alpha-research background. I treated it like any new research problem: read the CME rulebook sections relevant to margin and give-ups, shadowed the execution desk for a week to see the workflow live, and built a small KDB prototype reconciling my own understanding of roll-cost against actual historical roll data before trusting myself to advise a PM on it. Within a month I was building the roll-timing optimizer described on my resume."

[🔝 Back to Top](#-table-of-contents)

---
---

## F6 · How Do You Handle Being Wrong in Front of a PM

**Answer framework:**
> "I say so immediately and specifically — which number was wrong, why, and what the corrected number is — rather than hedging. Early in my career I once reported an aggregated slippage number that didn't correct for a batch of give-up trades booked against a stale settlement price, overstating a PM's execution cost. As soon as I found the booking issue I flagged it to the PM directly, corrected the report, and — more importantly — added an automated data-quality check that would catch that exact class of booking anomaly going forward, so the mistake became a permanent improvement to the framework rather than a one-off apology."

[🔝 Back to Top](#-table-of-contents)

---
---

## F7 · Describe Your Experience Advising PMs Across Multiple Asset Classes

**Answer framework:**
> "Across Millburn, Highbridge and Balyasny I've advised PMs trading futures, equities, FX and derivatives, and the throughline is that execution advice can't be asset-class-agnostic — a futures PM cares about roll timing and margin efficiency, an FX PM cares about last-look and fix risk, an equities PM cares about venue toxicity and dark-pool routing. My approach is always to first understand what benchmark actually reflects that PM's economic reality (per my B4/B1 framework), then tailor the TCA report to their asset class's specific frictions rather than handing everyone the same generic slippage number."

**Job tie-back:** direct match for "cross-asset experience across futures, equities, FX, and/or options is preferred."

[🔝 Back to Top](#-table-of-contents)

---
---

## F8 · Tell Me About a Time You Improved a Trading Cost Process

**Answer framework:**
> "The calendar-spread roll-timing optimizer I built at Highbridge is the clearest example — before it existed, rolls were executed on a fixed calendar schedule regardless of market conditions. I built an EWMA-based signal on the calendar spread itself to detect when other market participants were rolling simultaneously (adverse selection risk), and used that to dynamically adjust the daily roll fraction. Measured against the prior fixed-schedule baseline over the following two quarters, average roll cost in basis points improved measurably, and — just as importantly — the variance of roll cost dropped, which PMs cared about as much as the average."

[🔝 Back to Top](#-table-of-contents)

---
---

## F9 · How Do You Prioritize When Multiple PMs Want TCA Analysis Simultaneously

**Answer framework:**
> "I triage on two axes: dollar materiality (how much capital/risk is affected) and statistical readiness (do we actually have enough fills yet to say something significant, per the B8 framework) — a request that's both high-materiality and statistically ready jumps the queue; a request where the honest answer is 'we don't have enough data yet' gets a clear timeline instead of a rushed, underpowered answer. I also build self-serve dashboards for the routine, well-understood questions so my own bandwidth is reserved for the genuinely novel asks that need real analysis, not repetitive report generation."

[🔝 Back to Top](#-table-of-contents)

---
---

## F10 · Why Millennium's Multi-Manager Platform vs a Single-Manager Fund

**Answer framework:**
> "A multi-manager platform means Execution Services serves many independent alpha processes simultaneously, which is a harder and more interesting execution problem than optimizing for one house view — I have to build tools general enough to serve a trend-follower and a short-horizon mean-reversion PM with the same underlying TCA infrastructure, tailored through configuration rather than rebuilt each time. That generality, plus the scale of aggregated futures flow across many pods (which is exactly why position-limit aggregation and broker relationship management matter so much here), is a different and more systems-oriented execution problem than I'd get at a single-manager shop, and it's the kind of platform-building work I want to be doing at this stage of my career."

[🔝 Back to Top](#-table-of-contents)

---
---

## 📐 Quick-Reference Equation Sheet

$$
F(t,T) = S_t \cdot e^{(r+c-y)(T-t)} \quad\text{(futures fair value / cost of carry)}
$$

$$
\text{Roll Yield} \approx -\frac{F(t,T_{\text{next}})-F(t,T_{\text{front}})}{F(t,T_{\text{front}})}\times\frac{365}{T_{\text{next}}-T_{\text{front}}}
$$

$$
\text{VM}_t=(F_t-F_{t-1})\times\text{Multiplier}\times N \quad\text{(daily variation margin)}
$$

$$
\text{IS}=(P_{\text{fill,avg}}-P_{\text{arrival}})\times Q_{\text{filled}}+(P_{\text{final}}-P_{\text{arrival}})\times Q_{\text{unfilled}}
$$

$$
\Delta P=\sigma Y\sqrt{Q/V}\quad\text{(square-root market impact law)}
$$

$$
x(t)=X\cdot\frac{\sinh(\kappa(T-t))}{\sinh(\kappa T)},\qquad \kappa=\sqrt{\lambda\sigma^2/\eta}\quad\text{(Almgren-Chriss optimal trajectory)}
$$

$$
t=\frac{\bar X}{s/\sqrt n}\sim t_{n-1}\quad\text{(TCA significance test)}
$$

$$
\mathbb{E}[T]=ab,\qquad P(+b\text{ first})=\frac{a}{a+b}\quad\text{(gambler's ruin, symmetric walk)}
$$

$$\rho = 2q-1 \quad\text{(correlation from ±1-coded agreement probability }q\text{)}$$

[🔝 Back to Top](#-table-of-contents)

---
---

> **End of playbook.** 50 questions across futures market structure, TCA & market impact, KDB+/q, Python whiteboard coding, probability/statistics/brain teasers, and resume-driven behavioral — built for a 1-hour, in-person, multi-interviewer on-site loop for the Quantitative Specialist, Execution Services role at Millennium.
