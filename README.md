# Millennium Execution Services — Quantitative Specialist — 10 Mock Screening Interview Sets (10 Q each)

*Sourced/weighted from recent (2025–2026) Glassdoor, eFinancialCareers, LinkedIn, WallStreetOasis, Blind, and QuantBlueprint candidate reports for Millennium Execution Services / Execution Quant roles, tailored to a senior futures TCA/execution candidate.*

---
---

## 📑 Advanced Rounds

- **[Round2 - Technical Interview](./ROUND2/README.md)**

---
---

## 📑 Table of Contents

### 📑 Questions
- [§1 · Set 1 — Recruiter/HR Screen: Resume + Motivation](#set-1--recruiterhr-screen-resume--motivation)
- [§2 · Set 2 — Futures Market Structure & Mechanics](#set-2--futures-market-structure--mechanics)
- [§3 · Set 3 — Transaction Cost Analysis (TCA) Deep Dive](#set-3--transaction-cost-analysis-tca-deep-dive)
- [§4 · Set 4 — KDB+/q Technical Screen](#set-4--kdbq-technical-screen)
- [§5 · Set 5 — Python & Data Analysis Screen](#set-5--python--data-analysis-screen)
- [§6 · Set 6 — Statistics & Quantitative Reasoning](#set-6--statistics--quantitative-reasoning)
- [§7 · Set 7 — Coding (Live/Take-Home Style)](#set-7--coding-livetake-home-style)
- [§8 · Set 8 — Stakeholder Management & Communication (Behavioral)](#set-8--stakeholder-management--communication-behavioral)
- [§9 · Set 9 — Cross-Asset & Desk-Specific Depth](#set-9--cross-asset--desk-specific-depth)
- [§10 · Set 10 — Final Screen: Full Mixed Rapid-Fire](#set-10--final-screen-full-mixed-rapid-fire)

### 📑 Questions & Answers
- [§1 · Set 1 — Recruiter/HR Screen: Resume + Motivation](./ANSWER1.md)
- [§2 · Set 2 — Futures Market Structure & Mechanics](./ANSWER2.md)
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

## Set 1 — Recruiter/HR Screen: Resume + Motivation
1. Walk me through your resume, focusing on your execution and TCA experience at BAM, Highbridge, and Millburn Ridgefield.
2. Why are you interested in the Execution Services team at Millennium specifically, versus staying in alpha research?
3. What do you know about Millennium's central trading desk and how it partners with portfolio managers?
4. Why leave a Senior Quantitative Researcher title to move into an execution-focused specialist role?
5. Describe your compensation expectations given the posted $145k–$200k base range.
6. What is your visa/work-authorization status and are you open to being in the New York office full-time?
7. How do you handle being one of a small group of technical execution quants supporting many PMs at once?
8. Tell me about a time you had to say no to a portfolio manager's request. How did you frame it?
9. What is your timeline and are you interviewing elsewhere right now?
10. What questions do you have about the team structure or reporting line for this role?

[🔝 Back to Top](#-table-of-contents)

---

## Set 2 — Futures Market Structure & Mechanics
1. Explain the difference between an outright futures contract, a calendar spread, TAS, and BTIC.
2. Walk through the lifecycle of a futures trade from execution through clearing and give-up.
3. What are margin requirements and position limits, and how do they constrain execution strategy?
4. How does execution differ between electronic futures markets (CME, ICE) and OTC-cleared products?
5. Explain how give-up agreements work between executing brokers and clearing brokers.
6. What operational considerations arise when rolling a futures position near contract expiry?
7. How would you evaluate a futures broker or execution algorithm's quality across venues?
8. Describe how the futures order book differs structurally from an equities limit order book.
9. What is basis risk in a futures roll, and how do you monitor it operationally?
10. How do give-ups and booking conventions differ across asset classes (futures vs FX vs equities)?

[🔝 Back to Top](#-table-of-contents)

---

## Set 3 — Transaction Cost Analysis (TCA) Deep Dive
1. Walk through how you would design a TCA framework for futures execution from scratch.
2. Explain implementation shortfall and how it decomposes into delay cost, market impact, and opportunity cost.
3. How do VWAP, TWAP, and arrival-price benchmarks differ, and when would you use each for futures?
4. How would you adapt an equities TCA framework to account for futures-specific costs (roll cost, spread cost)?
5. Describe how you would fit a market impact model using historical futures execution data.
6. How would you attribute slippage across a portfolio manager group to identify a systemic cost driver?
7. What data quality issues typically arise in tick-level futures execution data, and how do you address them?
8. How would you present a TCA finding that shows a PM's execution cost is worse than peers, without being adversarial?
9. Explain Almgren-Chriss market impact modeling and its assumptions relative to futures liquidity.
10. How do give-up fees, exchange fees, and clearing costs factor into total cost of trading?

[🔝 Back to Top](#-table-of-contents)

---

## Set 4 — KDB+/q Technical Screen
1. Write a q query to compute VWAP for a futures contract over a configurable rolling time window.
2. Explain the difference between `aj` (as-of join) and `lj` (left join) in kdb+, and when TCA uses each.
3. How would you structure a splayed/partitioned kdb+ database for multi-year tick-level futures data?
4. Write q code to compute rolling realized volatility from a trade table.
5. How do you optimize a slow q query that scans across many partitions?
6. Explain q's vector operations vs iterative loops — why does it matter for performance at scale?
7. How would you join execution fills against a reference market-data table to compute arrival-price slippage?
8. Describe a memory/performance issue you've hit in kdb+ and how you resolved it.
9. How do you handle asynchronous or out-of-order tick arrivals in a kdb+ ingestion pipeline?
10. Write q/Python (pykx) code to summarize daily slippage by portfolio manager group.
[🔝 Back to Top](#-table-of-contents)

---

## Set 5 — Python & Data Analysis Screen
1. Write Python/pandas code to compute rolling VWAP and slippage from a fills DataFrame.
2. How would you clean duplicate or corrupted trade records in a multi-exchange execution dataset?
3. Explain how you'd vectorize a slippage-attribution calculation instead of using a Python loop.
4. Walk through your approach to building a reusable execution-analytics reporting library.
5. How do you profile and speed up a slow Python research/reporting pipeline before it goes into daily production use?
6. Describe your experience integrating Python with kdb+ (e.g., via pykx or qPython).
7. How would you structure unit tests for a transaction-cost calculation function?
8. What matplotlib/visualization approach would you use to show a PM their execution quality over time?
9. How do you handle timezone and exchange-calendar alignment across global futures markets in Python?
10. Describe a time you had to productionize a research notebook into a maintained tool.

[🔝 Back to Top](#-table-of-contents)

---

## Set 6 — Statistics & Quantitative Reasoning
1. How would you statistically test whether one execution algorithm produces lower slippage than another?
2. Explain heteroskedasticity in trading-cost data and how you'd correct standard errors (e.g., Newey-West).
3. How would you fit a market-impact model relating order size to price impact, and what functional form would you use?
4. Explain multicollinearity in a cost-attribution regression with correlated explanatory variables (size, volatility, spread).
5. How do you separate temporary vs permanent market impact empirically?
6. What statistical approach would you use to detect a regime shift in trading costs (e.g., pre/post a liquidity event)?
7. How would you determine the appropriate sample size/lookback window for a TCA benchmark to be statistically meaningful?
8. Explain the difference between correlation and causation in the context of "high volatility causes high slippage."
9. How would you communicate a cost-model result with wide confidence intervals to a non-technical PM?
10. Describe how you'd validate a trading-cost model out-of-sample before rolling it out firm-wide.

[🔝 Back to Top](#-table-of-contents)

---

## Set 7 — Coding (Live/Take-Home Style)
1. Given tick-level futures trade and quote data, compute VWAP over configurable intervals (live coding).
2. Implement a function to detect and flag duplicate or corrupted rows in a streaming execution dataset.
3. Given an array of fills, compute implementation shortfall against a given arrival price (coding).
4. Write a sliding-window algorithm to compute rolling volatility from a price series.
5. Design a class representing an order/fill blotter supporting add, amend, and cancel in efficient time.
6. Given noisy records across multiple exchange feeds, design a dedup/normalization pipeline.
7. Write pseudocode for an automated daily TCA report generation job.
8. Explain the time/space complexity of your solution and how you'd optimize it for production latency.
9. How would you design the schema for a database storing multi-asset execution and market data?
10. What testing/validation would you build into a production TCA pipeline before it goes live?

[🔝 Back to Top](#-table-of-contents)

---

## Set 8 — Stakeholder Management & Communication (Behavioral)
1. Describe a time you advised a portfolio manager on execution quality and they pushed back. How did you handle it?
2. Tell me about a time you had to translate a technical cost-analysis finding into a clear, actionable recommendation.
3. How do you balance being a subject-matter expert with being collaborative across trading, technology, and PM teams?
4. Describe a time you had to defend a research or analytical result under skeptical questioning.
5. How do you prioritize competing requests from multiple portfolio manager groups with limited time?
6. Tell me about a disagreement with a trading desk or technology partner on how to build an execution tool. How was it resolved?
7. Describe how you've driven a roadmap or product decision that required aligning business priorities with technical implementation.
8. How do you handle ambiguity when a PM gives you a vague request to "look into" their execution costs?
9. Give an example of when you identified an opportunity to reduce trading costs and how you implemented the change.
10. How do you approach knowledge transfer/documentation so your analysis is reproducible by teammates?

[🔝 Back to Top](#-table-of-contents)

---

## Set 9 — Cross-Asset & Desk-Specific Depth
1. How would you adapt your futures TCA framework for cross-asset use across equities and FX?
2. Compare execution quality considerations for futures vs FX vs equities — what changes in your model?
3. How does queue position and passive order placement differ across futures exchanges vs equities venues?
4. Explain how you'd evaluate a broker's execution algorithm performance across different futures products.
5. What role does market commentary/content play in supporting PMs, and how would you contribute to it?
6. How would you assess trading cost differences between electronic execution and voice/manual execution in futures?
7. Describe your understanding of how central trading desks interact with volatility/vol-risk businesses.
8. How would you design a process to systematically assess trading costs across multiple PM groups?
9. What experience do you have advising on execution algorithms, market structure, and slippage across asset classes?
10. How would you keep a cross-asset TCA framework maintainable as new products/venues are added?

[🔝 Back to Top](#-table-of-contents)

---

## Set 10 — Final Screen: Full Mixed Rapid-Fire
1. Give a 2-minute summary of your most impactful execution/TCA project.
2. Why should Millennium's Execution Services team hire you over another senior execution quant candidate?
3. Explain implementation shortfall in one sentence, as you would to a portfolio manager.
4. What's the biggest mistake you've seen in a TCA framework and how would you avoid it?
5. How do you decide when a signal or cost-model result is robust enough to present to senior stakeholders?
6. Walk through how you'd onboard onto Millennium's futures execution stack in your first 90 days.
7. What's your view on using KDB+ vs a Python/pandas-based stack for large-scale tick data analysis?
8. How do you stay current on futures market structure changes (new order types, venue rules)?
9. Describe how you would measure success in this role after six months.
10. What questions do you have for us about the team, technology stack, or research process?

[🔝 Back to Top](#-table-of-contents)

---
