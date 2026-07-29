# Set 4 — KDB+/q Technical Screen
**Total time budget: ~15 minutes** (live-coding-adjacent; expect screen share for q code — keep answers code-first, commentary second).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Write a q query to compute VWAP for a futures contract over a configurable rolling time window.**](#q1-write-a-q-query-to-compute-vwap-for-a-futures-contract-over-a-configurable-rolling-time-window)
- [§2 · **Q2. Explain the difference between `aj` (as-of join) and `lj` (left join) in kdb+, and when TCA uses each.**](#q2-explain-the-difference-between-aj-as-of-join-and-lj-left-join-in-kdb-and-when-tca-uses-each)
- [§3 · **Q3. How would you structure a splayed/partitioned kdb+ database for multi-year tick-level futures data?**](#q3-how-would-you-structure-a-splayedpartitioned-kdb-database-for-multi-year-tick-level-futures-data)
- [§4 · **Q4. Write q code to compute rolling realized volatility from a trade table.**](#q4-write-q-code-to-compute-rolling-realized-volatility-from-a-trade-table)
- [§5 · **Q5. How do you optimize a slow q query that scans across many partitions?**](#q5-how-do-you-optimize-a-slow-q-query-that-scans-across-many-partitions)
- [§6 · **Q6. Explain q's vector operations vs iterative loops — why does it matter for performance at scale?**](#q6-explain-qs-vector-operations-vs-iterative-loops--why-does-it-matter-for-performance-at-scale)
- [§7 · **Q7. How would you join execution fills against a reference market-data table to compute arrival-price slippage?**](#q7-how-would-you-join-execution-fills-against-a-reference-market-data-table-to-compute-arrival-price-slippage)
- [§8 · **Q8. Describe a memory/performance issue you've hit in kdb+ and how you resolved it.**](#q8-describe-a-memoryperformance-issue-youve-hit-in-kdb-and-how-you-resolved-it)
- [§9 · **Q9. How do you handle asynchronous or out-of-order tick arrivals in a kdb+ ingestion pipeline?**](#q9-how-do-you-handle-asynchronous-or-out-of-order-tick-arrivals-in-a-kdb-ingestion-pipeline)
- [§10 · **Q10. Write q/Python (pykx) code to summarize daily slippage by portfolio manager group.**](#q10-write-qpython-pykx-code-to-summarize-daily-slippage-by-portfolio-manager-group)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Write a q query to compute VWAP for a futures contract over a configurable rolling time window.

**A) Time budget:** 3 minutes (write + explain).

**B) Follow-ups:** "Is the trade table `trade` with columns `time`,`sym`,`price`,`size`, and should the window be wall-clock (e.g., 5 minutes) or a fixed number of trades?"

**C) Detailed answer — VS Code / q console:**
```q
/ vwap.q
/ Compute rolling VWAP over a configurable time window (e.g., 00:05:00) per symbol.

rollingVwap:{[t;win]
  / t: trade table with columns time,sym,price,size
  / win: rolling window as a time span, e.g., 00:05:00
  t:update pv:price*size from t;
  select time, sym,
    vwap: (sums pv) % (sums size)   / cumulative fallback if no window fn available
    by sym
  from t
 };

/ Preferred: true rolling window using .Q or manual xbar bucketing
rollingVwapWindow:{[t;win]
  select time, sym,
    vwap: (mavg[`long$win % `long$1D%86400000; ] each price*size) % (mavg[`long$win % `long$1D%86400000; ] each size)
  from `sym`time xasc t
 };

/ Cleanest idiomatic version: bucket into win-sized bars and compute VWAP per bar
barVwap:{[t;win]
  select vwap: size wavg price
  by sym, time.date, bucket: win xbar time.minute
  from t
 };
```

**D) Feynman summary:** VWAP is just "total dollars traded divided by total shares traded" — in q, `wavg` (weighted average) does exactly that in one primitive, and `xbar` is q's idiomatic way to say "round every timestamp down into its window bucket" so the `by` clause naturally groups trades into rolling bars.

**E) Follow-ups:**
- *"How would you make it a true sliding window instead of fixed bars?"* → Use a windowed join (`wj`) against a time-indexed table, or maintain a running sum with a decay/eviction of trades older than `win` in a streaming/kdb-tick context.
- *"What's `wavg` doing under the hood?"* → `w wavg x` computes `(sum w*x)%sum w` — exactly VWAP's definition, weight = size, value = price.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Explain the difference between `aj` (as-of join) and `lj` (left join) in kdb+, and when TCA uses each.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```q
/ lj: exact-key left join — every row in the left table must find a matching key in the right table
result: t1 lj t2

/ aj: as-of join — for each row in t1, find the LAST row in t2 whose timestamp is <= t1's timestamp
/ this is THE core TCA primitive: "what was the market price at or just before this fill?"
result: aj[`sym`time; fills; quotes]
```
> "`lj` requires an exact key match (e.g., joining on `sym` alone, or `sym,time` with identical timestamps) — rarely useful for TCA since fills and quotes almost never share identical timestamps. `aj` is the workhorse: given a fills table and a quotes table both sorted by `sym,time`, `aj[\`sym\`time; fills; quotes]` attaches, to each fill, the most recent quote at or before the fill time — exactly what's needed to compute arrival-price slippage, spread cost, or mark against the prevailing NBBO-equivalent at execution."

Every IS/VWAP-slippage calculation in **[Set 3 → Q3. How do VWAP, TWAP, and arrival-price benchmarks differ, and when would you use each for futures](./ANSWER3.md#q3-how-do-vwap-twap-and-arrival-price-benchmarks-differ-and-when-would-you-use-each-for-futures)** is, under the hood, an `aj` between an order/fill table and a market-data table.

**D) Feynman summary:** `lj` asks "what row exactly matches this key?" `aj` asks the much more useful trading question "what was true in the market at this exact moment, using only information available up to that moment?" — TCA lives and dies on `aj`.

**E) Follow-ups:**
- *"What if you need the closest quote in *either* direction, not just before?"* → q doesn't have a native symmetric nearest-join; typically compute both a backward `aj` and a forward-shifted variant and pick the minimum time-distance, or use `bin`/`binr` for lower-level control.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. How would you structure a splayed/partitioned kdb+ database for multi-year tick-level futures data?

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Single exchange/venue, or multi-venue with different schemas per feed?"

**C) Detailed answer:**
```q
/ Directory layout for a partitioned (by date) DB:
/ /db/2024.01.15/trade/    -- splayed columns for that date's trade table
/ /db/2024.01.15/quote/
/ /db/2025.06.02/trade/
/ ...

/ Loading:
\l /db

/ Table schema example, sym as first column of `p# (parted) attribute within a date partition
trade: ([] time:`timestamp$(); sym:`g#`symbol$(); price:`float$(); size:`long$())

/ Apply attributes for query speed:
`p#sym   / parted attribute on sym within each date-partitioned splayed table -> O(1)-ish sym lookups
`s#time  / sorted attribute on time -> enables binary search / fast aj and asof operations
```
> "Partition by **date** at the top level (standard kdb+ convention) — this lets the query engine prune partitions instantly for date-ranged queries (`select from trade where date within (d1;d2)`), which is essential once you have multi-year tick data (billions of rows). Within each date partition, splay each table into per-column files, and apply the **parted attribute** (`` `p# ``) on `sym` since queries almost always filter/group by symbol — this turns sym-based lookups into near-O(1) rather than scanning. Apply the **sorted attribute** (`` `s# ``) on `time` to enable binary-search-based as-of joins instead of linear scans. For a futures desk specifically, I'd also maintain a **continuous-contract mapping table** (raw contract → generic front-month series) as a small in-memory reference table, since roll-adjusted series are needed constantly for TCA continuity ( Refer to similar question **[Set 2 → Q6. What operational considerations arise when rolling a futures position near contract expiry?](./ANSWER2.md#q6-what-operational-considerations-arise-when-rolling-a-futures-position-near-contract-expiry)** ) but shouldn't be baked into the raw partitioned data."

**D) Feynman summary:** Partitioning by date is like organizing a filing cabinet by year so you never have to open drawers you don't need; the parted attribute on sym is like adding an alphabetical tab inside each drawer so you don't have to flip through every page to find one symbol.

**E) Follow-ups:**
- *"Why not partition by symbol instead of date?"* → Date partitioning matches how queries are actually asked (date ranges) and how data arrives (daily batches); symbol-partitioning would fragment writes and make date-range scans (the common case) far slower.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Write q code to compute rolling realized volatility from a trade table.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Realized vol from trade prices directly, or from a resampled return series (e.g., 1-minute bars) first?"

**C) Detailed answer:**
```q
/ realized_vol.q
/ Rolling realized volatility from 1-minute log-return bars, per symbol.

bars: select last price by sym, time.date, minute: 1 xbar time.minute from trade;
bars: update logret: log price % prev price by sym from `sym`date`minute xasc bars;

rollingRealizedVol:{[bars;win]
  / win: number of bars in the rolling window (e.g., 30 for a 30-min rolling window)
  update rvol: sqrt win * mdev[win] each logret  / sqrt(N) * std-dev annualized-style scaling
  by sym, date
  from bars
 };

result: rollingRealizedVol[bars; 30]
```

**D) Feynman summary:** Realized volatility is just "how spread-out were the recent price changes" — build minute bars, take log returns between consecutive bars, and take a rolling standard deviation; `mdev` (moving deviation) is q's built-in for exactly that rolling window statistic.

**E) Follow-ups:**
- *"How would you annualize it?"* → Multiply by $\sqrt{\text{periods per year}}$, e.g., $\sqrt{252 \times 390}$ for minute bars over a regular equity session, or the appropriate futures session-length equivalent.
- *"What about overnight/session-boundary returns contaminating the calculation?"* → Exclude the first bar's return of each new session/date (reset `prev price` at session boundaries) so you're not computing a return across a market-closed gap.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. How do you optimize a slow q query that scans across many partitions?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Do you have a specific example query in mind, or general principles?"

**C) Detailed answer:**
> "Ordered checklist I'd apply:
> 1. **Partition pruning first** — ensure the `where` clause filters on `date` (or the partition column) as the *first* constraint, so the query engine skips irrelevant partitions entirely rather than scanning and filtering after the fact.
> 2. **Push filters down / filter before joins** — filter each table to the minimal relevant rows *before* joining (`aj`, `lj`), not after — a common anti-pattern is joining large tables first then filtering, which does far more work than necessary.
> 3. **Use attributes** — confirm `` `p# `` on grouping columns (e.g., sym) and `` `s# `` on time; a missing attribute silently turns an O(log n) or O(1) operation into an O(n) scan.
> 4. **Avoid per-row functions / vectorize** (Q6) — a `each`-applied scalar function across millions of rows is far slower than a vectorized primitive; rewrite in terms of q's built-in vector operators.
> 5. **Column selection discipline** — select only the columns actually needed before heavy computation; kdb+ is column-oriented, so pulling unused columns off disk wastes I/O.
> 6. **Parallelize across partitions** — for genuinely large multi-year scans, use `peach` (parallel each) across date partitions if running on a multi-core/multi-process kdb+ setup, or split the query into per-year sub-queries and union results.
> 7. **Profile before guessing** — use `\ts` to time and space-profile a query, and isolate which stage (I/O, join, aggregation) dominates before optimizing blindly."

**D) Feynman summary:** Slow kdb+ queries almost always come from one of three sins: scanning partitions you didn't need to, doing row-by-row work that should have been vectorized, or missing an attribute that would have turned a scan into a lookup — profile first, fix the actual bottleneck, don't guess.

**E) Follow-ups:**
- *"How does `peach` differ from `each` in terms of risk?"* → `peach` distributes work across secondary processes/cores — real speedup for CPU-bound independent work, but adds overhead for small tasks and requires the workload to be genuinely parallelizable (no shared mutable state).

[🔝 Back to Top](#-table-of-contents)

---

## Q6. Explain q's vector operations vs iterative loops — why does it matter for performance at scale?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```q
/ Vectorized (fast): operates on the whole column at once, C-level loop under the hood
slippage: (execPrice - arrivalPrice) * size

/ Iterative (slow): q-level loop, function-call overhead per row
slippage: {[e;a;s] (e-a)*s}'[execPrice;arrivalPrice;size]   / ' (each-both) still row-by-row overhead
```
> "kdb+'s core primitives (`+`,`-`,`*`,`%`, `sum`, `avg`, `wavg`, comparisons) are implemented in compiled C and operate on entire columns/vectors in one call — no per-element q-interpreter overhead. An explicit q-level loop or lambda applied `each`/`'` still pays q's interpreter dispatch cost per element. At millions-to-billions of rows (multi-year tick data), that per-element overhead compounds into orders-of-magnitude slowdowns. The discipline is: express every calculation as composition of built-in vector primitives first, and only drop to `each`/explicit iteration when the logic is genuinely not vectorizable (e.g., a path-dependent recursive calculation)."

**D) Feynman summary:** Vector operations are like using a paper cutter that slices an entire stack at once; a loop is picking up scissors and cutting one sheet at a time — same result, but at a million sheets the difference between one machine pass and a million manual snips is the difference between seconds and hours.

**E) Follow-ups:**
- *"Give an example that genuinely can't be vectorized."* → A stateful recursive calculation like an EWMA with a data-dependent reset condition, or a sequential order-matching simulation — sometimes unavoidable, but should be isolated and minimized in scope.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. How would you join execution fills against a reference market-data table to compute arrival-price slippage?

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Should arrival price be the mid-quote at order-arrival time, or the last-trade price?"

**C) Detailed answer:**
```q
/ arrival_slippage.q
/ fills: time, sym, orderId, execPrice, execSize
/ orders: orderId, sym, arrivalTime
/ quotes: time, sym, bid, ask

/ Step 1: get the prevailing mid-quote at each order's arrival time via as-of join
ordersWithArrival: aj[`sym`time; `sym`time xcol update time:arrivalTime from orders; quotes];
ordersWithArrival: update arrivalMid: 0.5*(bid+ask) from ordersWithArrival;

/ Step 2: attach arrival price to each fill by orderId
fillsWithArrival: fills lj (`orderId xkey select orderId, arrivalMid from ordersWithArrival);

/ Step 3: compute slippage in bps (buy convention: positive = cost)
result: update slippageBps: 10000 * (execPrice - arrivalMid) % arrivalMid from fillsWithArrival
```

**D) Feynman summary:** Two joins, two different jobs — the `aj` answers "what was the market's mid-price at the exact moment this order arrived," and the `lj` answers "now stamp that arrival price onto every individual fill belonging to that order" — separating the as-of (time-based) join from the exact-key (orderId-based) join keeps each step simple and auditable.

**E) Follow-ups:**
- *"What if an order has fills across multiple days?"* → Ensure the as-of join and reference tables are date-partitioned consistently, and confirm arrival time falls on the correct partition before joining — cross-partition as-of joins need `` `date`sym`time `` composite keys handled carefully.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. Describe a memory/performance issue you've hit in kdb+ and how you resolved it.

**A) Time budget:** 2 minutes (STAR-lite, but keep it technical and concrete).

**B) Follow-ups:** "A specific ingestion/pipeline example, or a query-performance example?" (Only ask if genuinely ambiguous which the interviewer wants.)

**C) Detailed answer:**
> "In building a multi-asset execution-monitoring pipeline, I hit an issue where a daily reconciliation query joined the full day's tick-level quote table against fills **before** filtering by symbol — for a portfolio trading dozens of instruments, this meant materializing a join across the entire day's cross-product-relevant quote data in memory, causing intermittent OOM on high-volume days (e.g., FOMC days with elevated tick volume).
> **Fix**: restructured the query to filter the quote table down to only the relevant symbols and a tight time window (arrival time ± a few seconds) *before* the as-of join, using the parted attribute on sym to make that pre-filter essentially free, and switched from an ad hoc unattributed table to properly splayed + `` `p#sym `` /`` `s#time `` partitioned storage as in Q3. This dropped peak memory by an order of magnitude and made the reconciliation job stable even on the highest-volume days."

**D) Feynman summary:** The bug wasn't a kdb+ limitation, it was a query doing far more work than the question required — joining everything then filtering, instead of filtering then joining; fixing the *order of operations* fixed the memory problem, not throwing more hardware at it.

**E) Follow-ups:**
- *"How would you have caught this before it hit production?"* → Load/stress-test with a high-tick-volume day (e.g., a past FOMC day) in a pre-prod environment before shipping, plus a memory-usage alert threshold in the actual job.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. How do you handle asynchronous or out-of-order tick arrivals in a kdb+ ingestion pipeline?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Real-time (tickerplant/RDB) ingestion, or batch end-of-day reconciliation of historical data?"

**C) Detailed answer:**
> "In a standard kdb+ tick architecture (feedhandler → tickerplant → RDB/HDB), out-of-order arrival typically stems from network jitter across multiple feed handlers or venues. My approach:
> 1. **Buffer with a small watermark delay** — hold incoming ticks in the tickerplant/RDB for a short grace window (e.g., a few hundred ms to low seconds, tunable per venue's typical jitter) before considering a time-window 'closed,' rather than assuming strict real-time ordering.
> 2. **Sort/re-sequence on exchange timestamp** — always key/sort tables on the exchange-provided timestamp (or sequence number), not receipt timestamp, and re-sort a rolling buffer before writing down to the RDB/HDB.
> 3. **Idempotent, sequence-numbered writes** — tag records with venue sequence numbers so duplicate or replayed messages (common after a feed handler reconnect) can be deduped deterministically rather than by best-effort timestamp matching.
> 4. **End-of-day reconciliation pass** — regardless of real-time handling, run a batch job at end-of-day that fully re-sorts and validates the day's partition against sequence-number continuity, flagging any gaps for investigation before the data is considered 'final' for TCA purposes."

**D) Feynman summary:** Real-time ticks arrive like mail from multiple couriers who don't coordinate — you can't stop them from showing up out of order, so instead you keep a small in-tray, wait a beat before filing anything, and always file by the postmark date (exchange timestamp) rather than by when it landed on your desk.

**E) Follow-ups:**
- *"What's the tradeoff of the watermark delay?"* → Longer delay = more accurate ordering but higher latency for real-time consumers (e.g., real-time TCA/risk dashboards); tune per use case — end-of-day TCA can afford much longer buffering than a live risk monitor.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. Write q/Python (pykx) code to summarize daily slippage by portfolio manager group.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Would you like this called from Python via pykx, or should I keep it pure q and note the pykx call pattern separately?"

**C) Detailed answer:**
```q
/ q side: daily_slippage_by_pm.q
dailySlippageByPM:{[fills]
  select avgSlippageBps: avg slippageBps,
         medSlippageBps: med slippageBps,
         stdSlippageBps: dev slippageBps,
         n: count i
  by date, pmGroup
  from fills
 };
```
```python
# daily_slippage_report.py
"""Daily slippage summary by PM group via pykx.

Fetches the fills table from a running kdb+ process and summarizes
per-PM-group slippage statistics for the daily TCA report.
"""
import pykx as kx


def daily_slippage_by_pm(conn: kx.QConnection) -> "kx.Table":
    """Query daily slippage summary by portfolio-manager group.

    Args:
        conn: An active pykx connection to the kdb+ HDB/RDB process.

    Returns:
        A pykx Table with columns: date, pmGroup, avgSlippageBps,
        medSlippageBps, stdSlippageBps, n.
    """
    return conn(
        "select avgSlippageBps: avg slippageBps, "
        "medSlippageBps: med slippageBps, "
        "stdSlippageBps: dev slippageBps, "
        "n: count i by date, pmGroup from fills"
    )


if __name__ == "__main__":
    with kx.QConnection(host="localhost", port=5001) as q_conn:
        summary = daily_slippage_by_pm(q_conn)
        print(summary.pd())  # convert to pandas for downstream reporting
```

**D) Feynman summary:** The q side does the heavy columnar aggregation where the data already lives (fast, no data movement); pykx is just a thin, typed bridge that lets Python-side reporting/plotting tools (matplotlib, downstream dashboards) consume the already-summarized result as a pandas DataFrame — push computation to the data, not data to the computation.

**E) Follow-ups:**
- *"Why not just pull all fills into pandas and aggregate there?"* → Violates 'push computation to the data' — pulling billions of raw ticks/fills over IPC into Python is far slower and memory-heavier than letting kdb+ aggregate first and shipping only the small summary table.

[🔝 Back to Top](#-table-of-contents)

---
