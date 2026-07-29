# Set 7 — Coding (Live/Take-Home Style)
**Total time budget: ~20 minutes** (likely the dedicated live-coding segment — write real, runnable Python 3.13 in VS Code, narrate while typing, keep talking about complexity/edge-cases as you go).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Given tick-level futures trade and quote data, compute VWAP over configurable intervals (live coding).**](#q1-given-tick-level-futures-trade-and-quote-data-compute-vwap-over-configurable-intervals-live-coding)
- [§2 · **Q2. Implement a function to detect and flag duplicate or corrupted rows in a streaming execution dataset.**](#q2-implement-a-function-to-detect-and-flag-duplicate-or-corrupted-rows-in-a-streaming-execution-dataset)
- [§3 · **Q3. Given an array of fills, compute implementation shortfall against a given arrival price (coding).**](#q3-given-an-array-of-fills-compute-implementation-shortfall-against-a-given-arrival-price-coding)
- [§4 · **Q4. Write a sliding-window algorithm to compute rolling volatility from a price series.**](#q4-write-a-sliding-window-algorithm-to-compute-rolling-volatility-from-a-price-series)
- [§5 · **Q5. Design a class representing an order/fill blotter supporting add, amend, and cancel in efficient time.**](#q5-design-a-class-representing-an-orderfill-blotter-supporting-add-amend-and-cancel-in-efficient-time)
- [§6 · **Q6. Given noisy records across multiple exchange feeds, design a dedup/normalization pipeline.**](#q6-given-noisy-records-across-multiple-exchange-feeds-design-a-dedupnormalization-pipeline)
- [§7 · **Q7. Write pseudocode for an automated daily TCA report generation job.**](#q7-write-pseudocode-for-an-automated-daily-tca-report-generation-job)
- [§8 · **Q8. Explain the time/space complexity of your solution and how you'd optimize it for production latency.**](#q8-explain-the-timespace-complexity-of-your-solution-and-how-youd-optimize-it-for-production-latency)
- [§9 · **Q9. How would you design the schema for a database storing multi-asset execution and market data?**](#q9-how-would-you-design-the-schema-for-a-database-storing-multi-asset-execution-and-market-data)
- [§10 · **Q10. What testing/validation would you build into a production TCA pipeline before it goes live?**](#q10-what-testingvalidation-would-you-build-into-a-production-tca-pipeline-before-it-goes-live)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Given tick-level futures trade and quote data, compute VWAP over configurable intervals (live coding).

**A) Time budget:** 3 minutes.

**B) Follow-ups:** "Trade table only, or do you want VWAP computed from quotes' midpoint too as a cross-check?"

**C) Detailed answer:**
```python
"""vwap_intervals.py — VWAP over configurable time intervals."""

from __future__ import annotations

import pandas as pd


def vwap_by_interval(
    trades: pd.DataFrame, interval: str = "5min"
) -> pd.Series:
    """Compute VWAP bucketed into fixed time intervals.

    Args:
        trades: DataFrame indexed by timestamp with columns
            ["price", "size"].
        interval: Pandas offset alias for bucket size, e.g. "5min".

    Returns:
        Series of VWAP indexed by interval start.
    """
    pv = (trades["price"] * trades["size"]).resample(interval).sum()
    vol = trades["size"].resample(interval).sum()
    return (pv / vol).rename("vwap")
```

**D) Feynman summary:** `resample` buckets the timestamps into fixed intervals the same way `xbar` does in q — sum price×size and sum size per bucket, divide, done.

**E) Follow-ups:**
- *"What if a bucket has zero volume?"* → Division produces `NaN` naturally in pandas — forward-fill or explicitly flag as "no trades in interval" rather than silently treating as zero cost.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Implement a function to detect and flag duplicate or corrupted rows in a streaming execution dataset.

**A) Time budget:** 3 minutes.

**B) Follow-ups:** "Streaming as in a live feed you process incrementally, or a batch of streamed-in data processed at intervals?"

**C) Detailed answer:**
```python
"""stream_dedup.py — incremental dedup/corruption flagging for a stream."""

from dataclasses import dataclass, field


@dataclass
class StreamValidator:
    """Tracks seen record keys and flags duplicates/corrupted records
    incrementally as a stream is processed.

    Attributes:
        seen_keys: Set of (exchange, sym, seq_num) tuples already observed.
        price_bounds: Per-symbol (min, max) plausible price bounds.
    """

    seen_keys: set[tuple[str, str, int]] = field(default_factory=set)
    price_bounds: dict[str, tuple[float, float]] = field(default_factory=dict)

    def validate(
        self, exchange: str, sym: str, seq_num: int, price: float, size: int
    ) -> str | None:
        """Validate one incoming record.

        Args:
            exchange: Source exchange identifier.
            sym: Instrument symbol.
            seq_num: Exchange sequence number.
            price: Trade price.
            size: Trade size.

        Returns:
            None if the record is valid; otherwise a string reason
            ("duplicate", "bad_price", "bad_size").
        """
        key = (exchange, sym, seq_num)
        if key in self.seen_keys:
            return "duplicate"
        self.seen_keys.add(key)

        if price <= 0:
            return "bad_price"
        if size <= 0:
            return "bad_size"

        lo, hi = self.price_bounds.get(sym, (0.0, float("inf")))
        if lo and not (lo <= price <= hi):
            return "bad_price"
        return None
```

**D) Feynman summary:** A running set of seen keys gives O(1) duplicate detection per incoming record; the price/size sanity checks are the same corruption filters from **[Set 5 → Q2. How would you clean duplicate or corrupted trade records in a multi-exchange execution dataset?](./ANSWER5.md#q2-how-would-you-clean-duplicate-or-corrupted-trade-records-in-a-multi-exchange-execution-dataset)**, just applied one record at a time instead of vectorized over a whole DataFrame, because a stream processes records as they arrive.

**E) Follow-ups:**
- *"How do you keep `seen_keys` from growing unbounded over a long-running stream?"* → Evict keys older than a retention window (e.g., keep only the current trading day's keys, reset/persist-and-clear at session boundary) — an unbounded set is a memory leak in a long-running process.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. Given an array of fills, compute implementation shortfall against a given arrival price (coding).

**A) Time budget:** 3 minutes.

**B) Follow-ups:** none needed — this is a direct implementation of the **[Set 3 → Q2. Explain implementation shortfall and how it decomposes into delay cost, market impact, and opportunity cost.](./ANSWER3.md#q2-explain-implementation-shortfall-and-how-it-decomposes-into-delay-cost-market-impact-and-opportunity-cost)** formula.

**C) Detailed answer:**
```python
"""implementation_shortfall.py — IS calculation per Perold (1988) decomposition."""

from dataclasses import dataclass


@dataclass(frozen=True)
class ISResult:
    """Implementation shortfall decomposition result, all in price units.

    Attributes:
        delay_cost: Cost from decision-to-arrival price drift.
        impact_cost: Cost of fills relative to arrival price.
        opportunity_cost: Cost of unfilled remainder.
        total: Sum of all three components.
    """

    delay_cost: float
    impact_cost: float
    opportunity_cost: float

    @property
    def total(self) -> float:
        """Total implementation shortfall."""
        return self.delay_cost + self.impact_cost + self.opportunity_cost


def implementation_shortfall(
    decision_price: float,
    arrival_price: float,
    fill_prices: list[float],
    fill_sizes: list[float],
    end_price: float,
    intended_size: float,
) -> ISResult:
    """Compute Implementation Shortfall decomposition for a parent order.

    Args:
        decision_price: Price at the moment the order was decided.
        arrival_price: Price at the moment the order reached the market.
        fill_prices: Execution price of each individual fill.
        fill_sizes: Quantity of each individual fill.
        end_price: Price at end of the measurement horizon.
        intended_size: Total quantity originally intended to trade.

    Returns:
        An ISResult with delay, impact, and opportunity cost components.

    Raises:
        ValueError: If intended_size is not positive.
    """
    if intended_size <= 0:
        raise ValueError("intended_size must be positive")

    executed_size = sum(fill_sizes)
    delay_cost = executed_size * (arrival_price - decision_price)
    impact_cost = sum(
        q * (p - arrival_price) for p, q in zip(fill_prices, fill_sizes)
    )
    unfilled = intended_size - executed_size
    opportunity_cost = unfilled * (end_price - arrival_price)

    return ISResult(delay_cost, impact_cost, opportunity_cost)
```

**D) Feynman summary:** Direct code translation of the **[Set 3 → Q2. Explain implementation shortfall and how it decomposes into delay cost, market impact, and opportunity cost.](./ANSWER3.md#q2-explain-implementation-shortfall-and-how-it-decomposes-into-delay-cost-market-impact-and-opportunity-cost)** formula: three sums, each isolating one economic effect (drift before arrival, cost of the actual fills, cost of what never got done) — `dataclass(frozen=True)` makes the result immutable and self-documenting via the `total` property.

**E) Follow-ups:**
- *"What if fill_prices and fill_sizes have mismatched lengths?"* → Add an explicit length-check/`ValueError` at the top — good catch, I'd add `if len(fill_prices) != len(fill_sizes): raise ValueError(...)` for defensive robustness.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Write a sliding-window algorithm to compute rolling volatility from a price series.

**A) Time budget:** 3 minutes.

**B) Follow-ups:** "Should this be an O(n) sliding-window implementation from scratch, or is using pandas's built-in rolling acceptable?" (Tests whether they want algorithmic first-principles or production pragmatism — cover both.)

**C) Detailed answer:**
```python
"""rolling_volatility.py — O(n) sliding-window realized volatility."""

import math


def rolling_realized_vol(log_returns: list[float], window: int) -> list[float | None]:
    """Compute rolling standard deviation of log returns in O(n) time.

    Maintains running sum and sum-of-squares so each step is O(1)
    amortized, rather than recomputing the full window's stats each time.

    Args:
        log_returns: Sequence of log returns.
        window: Number of periods in the rolling window.

    Returns:
        List the same length as log_returns; entries before the window
        is full are None.
    """
    result: list[float | None] = [None] * len(log_returns)
    running_sum = 0.0
    running_sq_sum = 0.0

    for i, r in enumerate(log_returns):
        running_sum += r
        running_sq_sum += r * r

        if i >= window:
            old = log_returns[i - window]
            running_sum -= old
            running_sq_sum -= old * old

        if i >= window - 1:
            mean = running_sum / window
            variance = running_sq_sum / window - mean * mean
            result[i] = math.sqrt(max(variance, 0.0))

    return result
```
> "This is $O(n)$ overall — each element enters and leaves the running sums exactly once — versus a naive approach recomputing `std()` over each window from scratch, which is $O(n \cdot \text{window})$. In production I'd typically just use `pandas.Series.rolling(window).std()`, which is implemented efficiently in C under the hood, but I wanted to show I understand what it's doing algorithmically underneath, and the `max(variance, 0.0)` guard handles floating-point cancellation error that can occasionally push the running variance formula slightly negative."

**D) Feynman summary:** Instead of re-adding up every number in the window from scratch every single step, keep a running total and a running total-of-squares, and just add the new value and subtract the value that's falling out of the window — that turns an $O(n\cdot\text{window})$ brute-force algorithm into $O(n)$.

**E) Follow-ups:**
- *"Why can the running-sum-of-squares approach be numerically unstable?"* → Catastrophic cancellation when `running_sq_sum/window` and `mean*mean` are both large and close in value — for production-grade numerical stability at scale, Welford's online algorithm is preferable; happy to write that variant too if wanted.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. Design a class representing an order/fill blotter supporting add, amend, and cancel in efficient time.

**A) Time budget:** 3.5 minutes.

**B) Follow-ups:** "Should amend support partial-quantity amendment, or just price/full-quantity replacement?"

**C) Detailed answer:**
```python
"""order_blotter.py — O(1) add/amend/cancel order blotter."""

from dataclasses import dataclass
from enum import Enum, auto


class OrderStatus(Enum):
    """Lifecycle state of an order."""

    ACTIVE = auto()
    AMENDED = auto()
    CANCELLED = auto()
    FILLED = auto()


@dataclass
class Order:
    """A single order resting on the blotter.

    Attributes:
        order_id: Unique identifier.
        sym: Instrument symbol.
        price: Limit price.
        size: Remaining quantity.
        status: Current lifecycle status.
    """

    order_id: str
    sym: str
    price: float
    size: float
    status: OrderStatus = OrderStatus.ACTIVE


class OrderBlotter:
    """Maintains active orders with O(1) add, amend, and cancel.

    Backed by a dict keyed on order_id, giving hash-map O(1) average-case
    lookup/update rather than scanning a list.
    """

    def __init__(self) -> None:
        """Initialize an empty blotter."""
        self._orders: dict[str, Order] = {}

    def add(self, order: Order) -> None:
        """Add a new order to the blotter. O(1) average case.

        Args:
            order: The order to add.
        """
        self._orders[order.order_id] = order

    def amend(self, order_id: str, *, price: float | None = None,
              size: float | None = None) -> None:
        """Amend price and/or size of an existing order. O(1) average case.

        Args:
            order_id: Identifier of the order to amend.
            price: New price, if changing.
            size: New size, if changing.

        Raises:
            KeyError: If order_id is not found or already terminal.
        """
        order = self._orders[order_id]
        if order.status in (OrderStatus.CANCELLED, OrderStatus.FILLED):
            raise KeyError(f"Cannot amend terminal order {order_id}")
        if price is not None:
            order.price = price
        if size is not None:
            order.size = size
        order.status = OrderStatus.AMENDED

    def cancel(self, order_id: str) -> None:
        """Cancel an existing order. O(1) average case.

        Args:
            order_id: Identifier of the order to cancel.
        """
        self._orders[order_id].status = OrderStatus.CANCELLED

    def active_orders(self) -> list[Order]:
        """Return all currently active/amended orders."""
        return [
            o for o in self._orders.values()
            if o.status in (OrderStatus.ACTIVE, OrderStatus.AMENDED)
        ]
```

**D) Feynman summary:** A dict keyed by order ID gives constant-time add/amend/cancel because there's never a need to scan a list looking for the right order — mutate the order object in place and just flip its status enum, which also keeps a clean audit trail of lifecycle transitions.

**E) Follow-ups:**
- *"How would you extend this for efficient price-level queries (e.g., best bid/offer)?"* → Add a secondary sorted structure (e.g., a price-indexed `SortedDict` or heap) alongside the id-indexed dict, updated on every add/amend/cancel — trades off O(1) simplicity for O(log n) price-level operations when that's needed.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. Given noisy records across multiple exchange feeds, design a dedup/normalization pipeline.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** none needed — combines  **[Set 4 → Q9. How do you handle asynchronous or out-of-order tick arrivals in a kdb+ ingestion pipeline?](./ANSWER4.md#q9-how-do-you-handle-asynchronous-or-out-of-order-tick-arrivals-in-a-kdb-ingestion-pipeline)** and **[Set 5 → Q2. How would you clean duplicate or corrupted trade records in a multi-exchange execution dataset?](./ANSWER5.md#q2-how-would-you-clean-duplicate-or-corrupted-trade-records-in-a-multi-exchange-execution-dataset)** into pipeline design; answer directly.

**C) Detailed answer:**
```python
"""normalize_pipeline.py — multi-feed dedup/normalization pipeline."""

from dataclasses import dataclass


@dataclass(frozen=True)
class NormalizedRecord:
    """A record normalized to a common schema across feeds."""

    exchange: str
    sym: str          # normalized to internal symbology
    timestamp_ns: int  # normalized to UTC nanoseconds
    price: float
    size: float


def normalize_and_dedup(
    raw_records: list[dict], symbol_map: dict[tuple[str, str], str]
) -> list[NormalizedRecord]:
    """Normalize and deduplicate records from multiple exchange feeds.

    Args:
        raw_records: Raw dicts, each with feed-specific keys/timezone/symbology.
        symbol_map: Maps (exchange, raw_symbol) to internal canonical symbol.

    Returns:
        Deduplicated, normalized records sorted by timestamp.
    """
    seen: set[tuple[str, str, int]] = set()
    normalized: list[NormalizedRecord] = []

    for rec in raw_records:
        exch = rec["exchange"]
        internal_sym = symbol_map[(exch, rec["raw_symbol"])]
        key = (exch, internal_sym, rec["seq_num"])
        if key in seen:
            continue
        seen.add(key)

        if rec["price"] <= 0 or rec["size"] <= 0:
            continue  # corrupted, drop (or route to an exceptions sink)

        normalized.append(NormalizedRecord(
            exchange=exch,
            sym=internal_sym,
            timestamp_ns=rec["timestamp_ns"],  # assume upstream already UTC
            price=float(rec["price"]),
            size=float(rec["size"]),
        ))

    return sorted(normalized, key=lambda r: r.timestamp_ns)
```
> "The design principle: **normalize to a single internal schema first** (canonical symbol, UTC nanosecond timestamps, consistent units) before any cross-feed comparison or dedup logic runs — trying to dedup/compare records that are still in each feed's native format is fragile and error-prone. Symbol mapping (`symbol_map`) is externalized as configuration, not hardcoded, since exchange symbology conventions differ and change over time."

**D) Feynman summary:** You can't meaningfully compare or merge records from different feeds until you've translated them into the same language first — normalize (symbol, timezone, units) before you dedup or join, not after.

**E) Follow-ups:**
- *"What if two feeds disagree on the same trade's price (arbitration ambiguity, not just duplication)?"* → That's not a duplicate, it's a discrepancy — route to an exceptions/reconciliation queue for investigation rather than picking one arbitrarily; flag it exactly like the **[Set 3 → Q7. What data quality issues typically arise in tick-level futures execution data, and how do you address them?](./ANSWER3.md#q7-what-data-quality-issues-typically-arise-in-tick-level-futures-execution-data-and-how-do-you-address-them)** data-quality discipline.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. Write pseudocode for an automated daily TCA report generation job.

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```python
"""daily_tca_job.py — orchestration pseudocode for the daily TCA report."""

def run_daily_tca_report(report_date: str) -> None:
    """Orchestrate the end-to-end daily TCA report generation.

    Args:
        report_date: The trading date to report on, e.g. "2026.07.27".
    """
    # 1. Extract
    fills = fetch_fills(report_date)             # from kdb+ via pykx
    market_data = fetch_market_data(report_date)  # ticks/quotes

    # 2. Clean & normalize
    fills = clean_trade_records(fills)             # Set 5 Q2
    market_data = clean_trade_records(market_data)

    # 3. Benchmark computation
    fills = attach_arrival_prices(fills, market_data)  # as-of join, Set 4 Q7
    fills = compute_implementation_shortfall(fills)     # Set 3 Q2 / Set 7 Q3

    # 4. Attribution & statistics
    attribution = attribute_by_pm_broker_instrument(fills)  # Set 3 Q6
    validate_statistical_significance(attribution)          # Set 6 Q7

    # 5. Data-quality gate — fail loudly, don't ship a silently-broken report
    if not passes_quality_checks(fills, market_data):
        alert_data_quality_team(report_date)
        raise RuntimeError(f"Data quality gate failed for {report_date}")

    # 6. Report & alert
    report = render_report(attribution)             # Set 5 Q8
    distribute_report(report, recipients="pm_desk_heads")
    check_anomaly_thresholds_and_alert(attribution)  # Set 6 Q6
```

**D) Feynman summary:** Same extract-clean-compute-attribute-report pipeline as the layered TCA framework design (**[Set 3 → Q1. Walk through how you would design a TCA framework for futures execution from scratch.](./ANSWER3.md#q1-walk-through-how-you-would-design-a-tca-framework-for-futures-execution-from-scratch)**), expressed as an orchestration job — the crucial addition for an *automated, unattended* job is the explicit quality gate that fails loudly and alerts rather than silently shipping a report built on bad data.

**E) Follow-ups:**
- *"What would `passes_quality_checks` actually check?"* → Row-count sanity vs. historical norms, no unexpected nulls in key benchmark columns, reconciliation between fills-record count and exchange trade-count, and no stale/missing symbol coverage versus the prior day.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. Explain the time/space complexity of your solution and how you'd optimize it for production latency.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Would you like me to walk through complexity for a specific one of the above solutions (e.g., the blotter or rolling vol), or in general terms?"

**C) Detailed answer:**
> "Taking the rolling-volatility solution (Q4) as the concrete example: time complexity is $O(n)$ overall (amortized $O(1)$ per element via the running-sum trick), space is $O(n)$ for storing the output series (or $O(1)$ extra beyond the input/output if streaming one value out at a time rather than materializing the full list). The blotter (Q5) is $O(1)$ average-case per operation, $O(m)$ space for $m$ resting orders.
> For **production latency** optimization beyond asymptotic complexity: (1) avoid unnecessary object allocation in hot paths (e.g., prefer mutating in place over creating new objects per tick, as the blotter does); (2) for genuinely latency-critical paths (e.g., real-time TCA alerts), consider moving the hot loop to Numba (`@njit`) or a compiled extension rather than pure Python, since Python's per-operation interpreter overhead dominates at high tick rates even for an $O(n)$ algorithm; (3) batch I/O (network/database calls) rather than per-record round trips; (4) profile (**[Set 3 → Q5. How do you profile and speed up a slow Python research/reporting pipeline before it goes into daily production use?](./ANSWER5.md#q5-how-do-you-profile-and-speed-up-a-slow-python-researchreporting-pipeline-before-it-goes-into-daily-production-use)**) before optimizing — asymptotic complexity tells you how it scales, but the actual production bottleneck is often a constant-factor issue (e.g., I/O) that Big-O analysis alone won't reveal."

**D) Feynman summary:** Big-O tells you how the algorithm scales as data grows, but production latency is often dominated by constant factors Big-O ignores — allocation overhead, I/O round-trips, interpreter dispatch — so real optimization work is profiling first, then fixing whichever of "scales badly" or "has a big constant factor" is actually the bottleneck.

**E) Follow-ups:**
- *"When is Numba worth the added complexity vs. plain NumPy vectorization?"* → When the logic is inherently sequential/path-dependent (can't be expressed as array ops, like the streaming validator in Q2) and is on a genuine hot path — for anything vectorizable, plain NumPy/pandas is simpler and usually fast enough.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. How would you design the schema for a database storing multi-asset execution and market data?

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "kdb+ schema specifically, or asset-class-agnostic relational design first, with kdb+ as the physical implementation?"

**C) Detailed answer:**
> "Conceptual schema (asset-class-agnostic core, with asset-class-specific extension tables):
> - **`orders`**: order_id (PK), parent_id, sym, asset_class, side, intended_size, decision_time, decision_price, pm_group, strategy.
> - **`fills`**: fill_id (PK), order_id (FK), exchange, price, size, exec_time, broker, algo.
> - **`market_data`**: sym, asset_class, timestamp, bid, ask, last_price, volume (partitioned by date as in **[Set 4 → Q3. How would you structure a splayed/partitioned kdb+ database for multi-year tick-level futures data?](./ANSWER4.md#q3-how-would-you-structure-a-splayedpartitioned-kdb-database-for-multi-year-tick-level-futures-data)**).
> - **`instrument_ref`**: sym, asset_class, contract_multiplier, tick_value, roll_calendar_id — the normalization layer (**[Set 3 → Q4. How would you adapt an equities TCA framework to account for futures-specific costs (roll cost, spread cost)?](./ANSWER3.md#q4-how-would-you-adapt-an-equities-tca-framework-to-account-for-futures-specific-costs-roll-cost-spread-cost)**) that lets cost calculations be asset-class-generic while still respecting each instrument's economics.
> - **Asset-class extension tables** (rather than one giant table with mostly-null columns): `futures_roll_events`, `fx_settlement_conventions`, `equities_corporate_actions` — each joined back to the core tables by sym/date, so futures-specific concepts (Set 2) don't pollute the generic schema, but are available when needed (Set 9's cross-asset framework requirement).
> The physical implementation would follow the **[Set 4 → Q3. How would you structure a splayed/partitioned kdb+ database for multi-year tick-level futures data?](./ANSWER4.md#q3-how-would-you-structure-a-splayedpartitioned-kdb-database-for-multi-year-tick-level-futures-data)** kdb+ partitioning/attribute discipline for the tick-scale tables (`market_data`, `fills`), with the smaller reference tables (`instrument_ref`, `orders`) kept as in-memory or lightly-partitioned tables since they're much smaller and queried differently (more by ID/join, less by date-range tick scan)."

**D) Feynman summary:** Keep a lean, asset-class-agnostic core schema for the concepts every asset class shares (an order, a fill, a price), and bolt on asset-class-specific extension tables for the concepts that don't generalize (futures rolls, FX settlement) — that's what lets a cross-asset TCA framework (Set 9) stay maintainable instead of turning into one sprawling table full of mostly-empty columns.

**E) Follow-ups:**
- *"How would you handle a new asset class being added later?"* → Add a new extension table joined by sym/date without touching the core schema — this is exactly the extensibility goal behind separating core vs. extension tables.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. What testing/validation would you build into a production TCA pipeline before it goes live?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed — synthesizes prior answers; answer directly.

**C) Detailed answer:**
> "Layered validation, mirroring the pipeline stages (Q7): (1) **unit tests** on every pure calculation function (IS, VWAP, attribution regressions — **[Set 5 → Q7. How would you structure unit tests for a transaction-cost calculation function?](./ANSWER5.md#q7-how-would-you-structure-unit-tests-for-a-transaction-cost-calculation-function)**); (2) **data-quality gates** at ingestion (**[Set 3 → Q7. What data quality issues typically arise in tick-level futures execution data, and how do you address them?](./ANSWER3.md#q7-what-data-quality-issues-typically-arise-in-tick-level-futures-execution-data-and-how-do-you-address-them)** checks, running automatically, failing loudly per Q7's `passes_quality_checks`); (3) **regression tests against known-good historical output** — re-run the pipeline against a frozen historical dataset with a previously-validated expected output, and alert on any unexpected drift after code changes; (4) **reconciliation tests** — cross-check aggregated pipeline output against an independent source (e.g., broker-provided cost reports, or a manual spot-check of a sample of trades) before trusting the pipeline as the sole source of truth; (5) **statistical sanity checks** on the output itself — e.g., automated alerts if aggregate slippage for a PM group falls outside a plausible historical range, which could indicate either a genuine anomaly or a pipeline bug, both worth a human look before the report ships."

**D) Feynman summary:** Test the pieces (unit tests), test the whole thing against a known-good answer (regression tests), test it against an outside source of truth (reconciliation), and test whether the final output itself looks plausible (sanity checks) — four independent nets catching different failure modes, because no single type of test catches everything.

**E) Follow-ups:**
- *"Which of these would you prioritize if you only had time for one before initial launch?"* → Reconciliation against an independent source — it's the check most likely to catch a systemic methodology error that unit tests (which validate the code does what you *intended*, not that your intention was correct) would miss entirely.

[🔝 Back to Top](#-table-of-contents)

---
