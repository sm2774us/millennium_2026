# Millennium Execution Services — Quantitative Specialist — Round 2: 10 Mock Technical Interview Sets (10 Q each)

*Sourced/weighted from recent (2025–2026) Glassdoor, eFinancialCareers, WallStreetOasis, Blind, QuantBlueprint, and kdb+/q community reports for Millennium Execution Services technical/onsite rounds, tailored to a senior futures TCA/execution candidate.*


---
---

[↩️ Back to ../README.md](../README.md#-advanced-rounds)

---
---

## 📑 Table of Contents

### 📑 Questions
- [§1 · Set 1 of 10 · KDB+/q Live Coding](#set-1--kdbq-live-coding)
- [§2 · Set 2 of 10 · Python / Pandas Live Coding](#set-2--pythonpandas-live-coding)
- [§3 · Set 3 — Transaction Cost Analysis (TCA) Deep Dive](#set-3--transaction-cost-analysis-tca-deep-dive)
- [§4 · Set 4 — KDB+/q Technical Screen](#set-4--kdbq-technical-screen)
- [§5 · Set 5 — Python & Data Analysis Screen](#set-5--python--data-analysis-screen)
- [§6 · Set 6 — Statistics & Quantitative Reasoning](#set-6--statistics--quantitative-reasoning)
- [§7 · Set 7 — Coding (Live/Take-Home Style)](#set-7--coding-livetake-home-style)
- [§8 · Set 8 — Stakeholder Management & Communication (Behavioral)](#set-8--stakeholder-management--communication-behavioral)
- [§9 · Set 9 — Cross-Asset & Desk-Specific Depth](#set-9--cross-asset--desk-specific-depth)
- [§10 · Set 10 — Final Screen: Full Mixed Rapid-Fire](#set-10--final-screen-full-mixed-rapid-fire)

### 📑 Questions & Answers
- [§1 · Set 1 of 10 · KDB+/q Live Coding](./ANSWER1.md)
- [§2 · Set 2 of 10 · Python / Pandas Live Coding](./ANSWER2.md)
- [§3 · Set 3 — Transaction Cost Analysis (TCA) Deep Dive](./ANSWER3.md)
- [§4 · Set 4 — KDB+/q Technical Screen](./ANSWER4.md)
- [§5 · Set 5 — Python & Data Analysis Screen](./ANSWER5.md)
- [§6 · Set 6 — Statistics & Quantitative Reasoning](./ANSWER6.md)
- [§7 · Set 7 — Coding (Live/Take-Home Style)](./ANSWER7.md)
- [§8 · Set 8 — Stakeholder Management & Communication (Behavioral)](./ANSWER8.md)
- [§9 · Set 9 — Cross-Asset & Desk-Specific Depth](./ANSWER9.md)
- [§10 · Set 10 — Final Screen: Full Mixed Rapid-Fire](./ANSWER10.md)

[🔝 Back to Top](#-table-of-contents)

---

## Set 1 — KDB+/q Live Coding
1. Write a q function to compute VWAP for a given contract over a configurable rolling time bucket using `xbar`.
2. Given a trades table and a quotes table, write an `aj` (as-of join) to attach the prevailing bid/ask to each trade.
3. Write q code to compute rolling realized volatility from a tick-level price series.
4. How would you partition/splay a multi-year, multi-contract futures tick database for efficient date-range queries?
5. Write q code to detect and remove duplicate trade prints within a microsecond window.
6. Explain the performance difference between `each` and vectorized q operations, with an example from TCA code.
7. Write a function to compute implementation shortfall per order given fills and a decision/arrival price.
8. How do you handle late-arriving or out-of-order ticks in a kdb+ ingestion pipeline without corrupting rolling aggregates?
9. Write q code to bucket trades into TWAP intervals and compute the TWAP benchmark price per bucket.
10. Describe how you'd optimize a q query that currently scans across 5 years of partitioned data and times out.

## Set 2 — Python/Pandas Live Coding
1. Given a fills DataFrame, write vectorized pandas code to compute slippage vs arrival price per order.
2. Write code to compute rolling VWAP over a configurable window without using a Python-level loop.
3. Given noisy/duplicate records across multiple exchange feeds, write a dedup and normalization function.
4. Write a function to compute participation rate (order volume / market volume) over the life of an order.
5. How would you vectorize a market-impact regression feature set (size, spread, volatility) across millions of orders?
6. Write code to compute rolling Sharpe or cost-adjusted performance across a panel of PM groups.
7. Profile a slow pandas pipeline (conceptually) — what are the first three things you'd check?
8. Write a function that merges execution data with reference market data on nearest prior timestamp (as-of merge).
9. How would you structure a pykx/qPython bridge to pull kdb+ data into a pandas workflow efficiently?
10. Write pseudocode/tests you'd add to validate a TCA calculation function before productionizing it.

## Set 3 — Market Impact & Execution Cost Modeling
1. Derive/explain the Almgren-Chriss framework — temporary vs permanent impact, and the optimal execution trade-off.
2. Compare Almgren-Chriss to Kyle's Lambda — what assumptions differ and when is each appropriate for futures?
3. How would you fit a power-law market impact model (impact ∝ size^α) to empirical futures execution data?
4. How do you separate temporary impact (reverts) from permanent impact (information-driven) empirically?
5. What functional form would you use to model impact for TAS or BTIC orders vs outright futures orders?
6. How would you incorporate volatility regime into a market impact model's parameters?
7. Explain how bid-ask spread widening intraday (open/close effects) should change optimal order scheduling.
8. How would you validate a market impact model out-of-sample and detect when it's degraded?
9. What's the effect of participation rate constraints on your optimal execution schedule derivation?
10. How would cross-asset hedges (e.g., futures hedge for an equity basket) change your impact-cost estimation?

## Set 4 — Statistics & Econometrics for TCA
1. Derive the OLS estimator for a slippage-attribution regression and state the Gauss-Markov assumptions.
2. Explain heteroskedasticity in trading-cost residuals and how Newey-West standard errors correct for it.
3. How would you test whether Algo A produces statistically lower slippage than Algo B, controlling for order size?
4. Explain multicollinearity between order size, volatility, and spread in a cost-attribution regression — how do you detect (VIF) and address it?
5. How would you design a hypothesis test to detect a cost regime shift after a market structure change?
6. What's the difference between statistical significance and economic significance in a trading-cost finding?
7. How would you correct for autocorrelation in a time series of daily execution costs?
8. Explain how you'd use a Hidden Markov Model to classify liquidity regimes for execution scheduling.
9. How would you set thresholds for flagging "outlier" executions given multiple-testing concerns across thousands of orders daily?
10. Walk through how you'd build a confidence interval around an estimated market impact coefficient.

## Set 5 — Coding: Data Structures & Algorithms
1. Implement an order book class supporting add/cancel/match with efficient (O(log n)) operations.
2. Given an array of trade prices, find the longest increasing subarray (discuss complexity).
3. Solve a sliding-window problem to compute the maximum rolling volume over a fixed time window.
4. Implement a function to merge k sorted market-data feeds into one time-ordered stream.
5. Given a stream of order events (new/amend/cancel/fill), design a data structure to reconstruct order state efficiently.
6. Write a function to detect gaps or missing ticks in a time series and flag them for review.
7. Design an efficient way to compute a running median of trade prices (e.g., using two heaps).
8. Given matrix-style tick data, construct a cumulative-sum matrix (prefix sums) for windowed aggregate queries.
9. Implement a dynamic programming solution to allocate a fixed order quantity across time buckets to minimize expected cost.
10. Explain the time/space complexity trade-offs of your order book implementation vs a simpler array-based approach.

## Set 6 — Futures Market Structure Technical Deep Dive
1. Walk through the technical mechanics of a calendar spread execution and how it's priced relative to two outright legs.
2. Explain how TAS (Trading at Settlement) orders are matched and priced, and the risk considerations for a desk trading them.
3. How would you technically model the price relationship in a BTIC (Basis Trade at Index Close) order?
4. Explain the operational/technical steps involved in a futures give-up from executing broker to clearing broker.
5. How would you model position limits and margin requirements as constraints in an execution-scheduling algorithm?
6. What data feeds/fields are essential for building a futures TCA pipeline (trade, quote, order, reference data)?
7. How does exchange matching-engine latency and order-type priority (e.g., iceberg, stop) affect your slippage attribution?
8. Explain how you'd technically handle a futures contract roll in a continuous price series used for backtesting cost models.
9. How would you design a system to compare execution algo performance across CME vs ICE for the same underlying?
10. What are the technical challenges in aligning tick data across time zones and exchange calendars for a global futures TCA framework?

## Set 7 — System Design: Execution Analytics Platform
1. Design an end-to-end TCA system architecture: ingestion, storage, computation, and reporting layers.
2. How would you design the schema for a kdb+ database storing multi-asset order/fill/market data at scale?
3. How would you design a daily automated TCA report pipeline that's robust to late/missing data?
4. How would you architect a real-time slippage monitoring dashboard for the trading desk?
5. What's your approach to versioning and backtesting changes to a production cost model without disrupting live reporting?
6. How would you design the system to support both intraday real-time queries and multi-year historical analysis?
7. How would you build in data quality checks/alerts across multiple exchange feeds feeding the platform?
8. Describe how you'd design an API/interface so PMs can self-serve execution-quality queries.
9. How would you scale this system as more asset classes (equities, FX) are added to the framework?
10. What testing/validation/monitoring would you put in production before the tool is trusted for PM decision-making?

## Set 8 — Applied Statistics / ML for Execution Research
1. How would you use gradient boosting or random forests to predict expected slippage for a new order?
2. What features would you engineer for a slippage-prediction model (size, spread, volatility, time-of-day, venue)?
3. How would you validate a slippage-prediction model given serial correlation in trading-cost data (CPCV/walk-forward)?
4. Explain the bias-variance tradeoff in the context of an overfit market-impact model.
5. How would you use PCA to reduce dimensionality in a multi-venue, multi-contract cost dataset?
6. How would you detect and handle regime changes in a machine-learning cost model (e.g., pre/post volatility shock)?
7. What's your view on using LLMs/GenAI to assist in generating TCA commentary or flagging anomalous executions?
8. How would you explain a black-box ML slippage model's predictions to a skeptical PM (interpretability)?
9. How would you avoid overfitting when testing many candidate cost-model features on the same historical dataset?
10. Describe how you'd set up an experiment (e.g., A/B test across algos) to causally estimate the effect of a new execution algorithm on costs.

## Set 9 — SQL / Data Engineering Technical Screen
1. Given a Trades table and an Orders table, write SQL to produce fill-vs-order quantity with zero-filled missing orders.
2. Write a SQL query to compute daily VWAP per contract from a trades table.
3. How would you design indexes/partitioning for a SQL-based execution database with billions of rows?
4. Write a query to identify orders with abnormally high slippage relative to their peer group (size/venue/time bucket).
5. How would you handle schema evolution (new fields, new venues) in a production execution database without downtime?
6. Write a query to join trade, quote, and order-state tables to reconstruct implementation shortfall per order.
7. How would you deduplicate records where the same fill is reported by two different broker feeds?
8. Explain your approach to migrating a legacy execution-cost reporting pipeline to a new data platform with zero data loss.
9. How would you write an efficient query to compute rolling 30-day average slippage by portfolio manager group?
10. What monitoring/alerting would you build around a nightly ETL job feeding the TCA database?

## Set 10 — Final Technical Round: Mixed Rapid-Fire + Take-Home Discussion
1. Walk through your open-source project's C++/Python signal engine architecture and how it would map to a TCA pipeline.
2. Explain how you'd fit and validate a market impact model end-to-end, from raw data to a presentable coefficient.
3. Write pseudocode to backtest an execution-scheduling algorithm against historical order/market data, including costs.
4. How do you decide the right lookback window for a cost-attribution model, and how do you avoid look-ahead bias?
5. Explain Kelly criterion-style position sizing in the context of execution risk (not just alpha sizing).
6. What's the difference between R² and adjusted R² and why does it matter with many cost-model features?
7. How would you profile and speed up a Python/kdb+ hybrid pipeline before it goes into daily production?
8. Describe a technically challenging execution/TCA problem you solved and the trade-offs you made.
9. What would you build first in your first 90 days if given a blank slate for the futures TCA framework?
10. What technical questions do you have for us about the current TCA stack, kdb+ usage, or data infrastructure?
