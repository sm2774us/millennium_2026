# Set 5 — Python & Data Analysis Screen
**Total time budget: ~15 minutes** (expect live coding in VS Code, Python 3.13; keep talking while typing).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. Write Python/pandas code to compute rolling VWAP and slippage from a fills DataFrame.**](#q1-write-pythonpandas-code-to-compute-rolling-vwap-and-slippage-from-a-fills-dataframe)
- [§2 · **Q2. How would you clean duplicate or corrupted trade records in a multi-exchange execution dataset?**](#q2-how-would-you-clean-duplicate-or-corrupted-trade-records-in-a-multi-exchange-execution-dataset)
- [§3 · **Q3. Explain how you'd vectorize a slippage-attribution calculation instead of using a Python loop.**](#q3-explain-how-youd-vectorize-a-slippage-attribution-calculation-instead-of-using-a-python-loop)
- [§4 · **Q4. Walk through your approach to building a reusable execution-analytics reporting library.**](#q4-walk-through-your-approach-to-building-a-reusable-execution-analytics-reporting-library)
- [§5 · **Q5. How do you profile and speed up a slow Python research/reporting pipeline before it goes into daily production use?**](#q5-how-do-you-profile-and-speed-up-a-slow-python-researchreporting-pipeline-before-it-goes-into-daily-production-use)
- [§6 · **Q6. Describe your experience integrating Python with kdb+ (e.g., via pykx or qPython).**](#q6-describe-your-experience-integrating-python-with-kdb-eg-via-pykx-or-qpython)
- [§7 · **Q7. How would you structure unit tests for a transaction-cost calculation function?**](#q7-how-would-you-structure-unit-tests-for-a-transaction-cost-calculation-function)
- [§8 · **Q8. What matplotlib/visualization approach would you use to show a PM their execution quality over time?**](#q8-what-matplotlibvisualization-approach-would-you-use-to-show-a-pm-their-execution-quality-over-time)
- [§9 · **Q9. How do you handle timezone and exchange-calendar alignment across global futures markets in Python?**](#q9-how-do-you-handle-timezone-and-exchange-calendar-alignment-across-global-futures-markets-in-python)
- [§10 · **Q10. Describe a time you had to productionize a research notebook into a maintained tool.**](#q10-describe-a-time-you-had-to-productionize-a-research-notebook-into-a-maintained-tool)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. Write Python/pandas code to compute rolling VWAP and slippage from a fills DataFrame.

**A) Time budget:** 3 minutes.

**B) Follow-ups:** "Should slippage be against arrival price, or a rolling VWAP benchmark itself?" (Both are asked for — clarify which drives the final column.)

**C) Detailed answer — VS Code:**
```python
"""rolling_vwap_slippage.py

Compute rolling VWAP and arrival-price slippage from a futures fills
DataFrame, using Python 3.13 type-parametrized syntax.

Typical usage:
    result = compute_rolling_vwap_and_slippage(fills, window="5min")
"""

from __future__ import annotations

import logging
import sys
import numpy as np
import pandas as pd


#def compute_rolling_vwap_and_slippage(
#    fills: pd.DataFrame,
#    window: str = "5min",
#) -> pd.DataFrame:
#    """Compute rolling VWAP and slippage vs. arrival price per symbol.
#
#    Args:
#        fills: DataFrame indexed by timestamp with columns
#            ["sym", "price", "size", "arrival_price"].
#        window: Rolling time window for VWAP, e.g. "5min".
#
#    Returns:
#        A copy of `fills` with two additional columns:
#            "rolling_vwap": time-windowed volume-weighted average price.
#            "slippage_bps": (price - arrival_price) / arrival_price * 1e4.
#    """
#    out = fills.sort_index().copy()
#    out["pv"] = out["price"] * out["size"]
#
#    grouped = out.groupby("sym", group_keys=False)
#    rolling_pv = grouped["pv"].apply(lambda s: s.rolling(window).sum())
#    rolling_size = grouped["size"].apply(lambda s: s.rolling(window).sum())
#    out["rolling_vwap"] = rolling_pv / rolling_size
#
#    out["slippage_bps"] = (
#        (out["price"] - out["arrival_price"]) / out["arrival_price"] * 1e4
#    )
#    return out.drop(columns="pv")

def compute_rolling_vwap_and_slippage(
    fills: pd.DataFrame,
    window: str = "5min",
) -> pd.DataFrame:
    """Compute rolling VWAP and slippage vs. arrival price per symbol.

    Args:
        fills: DataFrame indexed by timestamp (or with a datetime index) with columns
            ["sym", "price", "size", "arrival_price"].
        window: Rolling time window for VWAP, e.g. "5min".

    Returns:
        A copy of `fills` with two additional columns:
            "rolling_vwap": time-windowed volume-weighted average price.
            "slippage_bps": signed basis point deviation from arrival price.

    Raises:
        KeyError: If required columns are absent from the input DataFrame.
    """
    required_cols = {"sym", "price", "size", "arrival_price"}
    if not required_cols.issubset(fills.columns):
        missing = required_cols - set(fills.columns)
        raise KeyError(f"Missing required columns: {missing}")

    out = fills.sort_index().copy()
    if out.empty:
        out["rolling_vwap"] = pd.Series(dtype=float)
        out["slippage_bps"] = pd.Series(dtype=float)
        return out

    # Ensure index is datetime for time-based rolling windows
    if not pd.api.types.is_datetime64_any_dtype(out.index):
        out.index = pd.to_datetime(out.index)

    out["pv"] = out["price"] * out["size"]

    # Fully vectorized rolling aggregation per symbol group bypassing slow apply loops
    grouped = out.groupby("sym", group_keys=False)
    rolling_pv = grouped["pv"].rolling(window).sum()
    rolling_size = grouped["size"].rolling(window).sum()

    # Reset index to align with original dataframe frame structure securely
    out["rolling_vwap"] = (rolling_pv / rolling_size).reset_index(level=0, drop=True)

    # Compute execution slippage in basis points (signed: positive means adverse relative to arrival)
    out["slippage_bps"] = (
        (out["price"] - out["arrival_price"]) / out["arrival_price"] * 1e4
    )
    return out.drop(columns="pv")

def run_self_validation() -> None:
    """Executes standalone self-validation assertions for rolling VWAP and slippage."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running rolling_vwap_slippage standalone validation...")

    dates = pd.date_range("2026-07-29 09:30:00", periods=5, freq="s")
    sample_fills = pd.DataFrame({
        "sym": ["CL", "CL", "CL", "CL", "CL"],
        "price": [75.0, 75.2, 75.1, 75.5, 75.6],
        "size": [100, 200, 150, 300, 100],
        "arrival_price": [74.9, 74.9, 74.9, 74.9, 74.9]
    }, index=dates)

    result = compute_rolling_vwap_and_slippage(sample_fills, window="10s")
    
    assert len(result) == 5, "Expected 5 rows"
    assert "rolling_vwap" in result.columns, "Missing 'rolling_vwap' column"
    assert "slippage_bps" in result.columns, "Missing 'slippage_bps' column"
    assert not result["rolling_vwap"].isna().any(), "Rolling VWAP contains unexpected NaNs"
    assert not result["slippage_bps"].isna().any(), "Slippage contains unexpected NaNs"

    logger.info("SUCCESS: rolling_vwap_slippage validation assertions passed.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in rolling_vwap_slippage execution: %s", e)
        sys.exit(1)
```

**D) Feynman summary:** Rolling VWAP is a moving weighted average — pandas's `.rolling(window)` does the "moving" part, and dividing rolling price×size by rolling size does the "weighted average" part; slippage is just the percentage gap between what you paid and what the market was at when you decided to trade, scaled to basis points so it's comparable across instruments with wildly different price levels.

**E) Follow-ups:**
- *"Why use time-based rolling ('5min') instead of a fixed row count?"* → Trade frequency varies through the day/across symbols; a time-based window keeps the economic meaning ("VWAP over the last 5 minutes") constant regardless of how many ticks occurred in that window.
- *"How would you vectorize this fully, avoiding the `.apply` per group?"* → Use `groupby().rolling()` chained natively, or precompute via `pd.core.window` primitives with `transform` — happy to rewrite live if wanted.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. How would you clean duplicate or corrupted trade records in a multi-exchange execution dataset?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Is 'corrupted' mostly bad prices/negative sizes, or also things like crossed quotes / clearly erroneous ticks?"

**C) Detailed answer:**
```python
"""clean_trades.py — deduplicate and flag corrupted trade records."""

import pandas as pd


def clean_trade_records(trades: pd.DataFrame) -> pd.DataFrame:
    """Deduplicate and filter corrupted trade records.

    Args:
        trades: Raw trades with columns
            ["exchange", "sym", "seq_num", "timestamp", "price", "size"].

    Returns:
        Cleaned DataFrame with duplicates removed and corrupted rows
        dropped (and logged for audit via the "_dropped" attribute).
    """
    deduped = trades.drop_duplicates(
        subset=["exchange", "sym", "seq_num", "timestamp", "price", "size"]
    )

    valid_mask = (
        (deduped["price"] > 0)
        & (deduped["size"] > 0)
        & deduped["price"].between(
            deduped.groupby("sym")["price"].transform("median") * 0.5,
            deduped.groupby("sym")["price"].transform("median") * 1.5,
        )
    )
    cleaned = deduped.loc[valid_mask].copy()
    cleaned.attrs["_dropped"] = deduped.loc[~valid_mask]
    return cleaned
```
> "Three layers: (1) exact-duplicate removal keyed on exchange sequence number + timestamp + price + size, mirroring the kdb+ dedup logic from Set 4 Q7; (2) sanity filters — non-positive price/size, and a median-band outlier filter per symbol to catch fat-finger/bad-tick prints; (3) **never silently drop** — retain dropped rows in an auditable side-table (`attrs['_dropped']` here, or a dedicated exceptions table in production) so data-quality issues are traceable and reviewable, not just invisible."

**D) Feynman summary:** Cleaning isn't about being aggressive with filters — it's about being disciplined: dedupe on what uniquely identifies a real trade, flag what's implausible relative to its own recent history, and always keep a receipt of what you threw away.

**E) Follow-ups:**
- *"What if a legitimate large move gets caught by the median-band filter?"* → Use a rolling/local median rather than a whole-day median, and widen bands during known high-volatility windows (e.g., economic releases) rather than a fixed static threshold.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. Explain how you'd vectorize a slippage-attribution calculation instead of using a Python loop.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```python
# Loop version (slow, O(n) Python-level overhead per row):
slippage = []
for _, row in fills.iterrows():
    slippage.append((row["price"] - row["arrival_price"]) / row["arrival_price"])

# Vectorized version (fast, single C-level pass via NumPy under the hood):
fills["slippage_bps"] = (
    (fills["price"] - fills["arrival_price"]) / fills["arrival_price"] * 1e4
)
```
> "`iterrows()` reconstructs a Python object per row and pays interpreter overhead on every iteration — for millions of fills, that's the dominant cost. The vectorized version expresses the same arithmetic as array operations that NumPy executes in compiled C across the entire column at once. The general principle: any calculation expressible as elementwise or grouped arithmetic should be written as pandas/NumPy operations, and `.apply`/`iterrows` should be a last resort reserved for genuinely row-dependent logic that can't be expressed as array ops."

**D) Feynman summary:** Same idea as the kdb+ vectorization question (Set 4 Q6) — looping in Python is picking up one sheet of paper at a time, vectorized NumPy/pandas operations run the whole stack through the cutter in one pass.

**E) Follow-ups:**
- *"When would `.apply` still be justified?"* → When the per-row logic is genuinely non-vectorizable (e.g., row depends on an external stateful lookup that can't be array-broadcast) — even then, prefer `numpy.vectorize` or a compiled path (Numba/Cython) over pure Python loops.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Walk through your approach to building a reusable execution-analytics reporting library.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Should this assume a single internal team consuming it, or should I design for external/multi-team reuse with a stable public API?"

**C) Detailed answer:**
> "I'd structure it in layers, mirroring the TCA framework design (Set 3 Q1):
> - **`data` module** — typed loaders/adapters (kdb+ via pykx, flat files, OMS exports) that normalize everything into a common internal schema (fills, orders, market data) regardless of source.
> - **`benchmarks` module** — pure functions computing VWAP/TWAP/Arrival Price/IS, each independently unit-testable (Q7) and documented with the exact formula it implements.
> - **`attribution` module** — decomposition and regression-based attribution (Set 3 Q2, Q6), built on top of `benchmarks`, never reaching back into raw data directly — clean separation of concerns.
> - **`reporting` module** — dashboards/report generation (Q8, Set 7 Q7), consuming `attribution` outputs only.
> I'd enforce this with Python 3.13 `Protocol` classes for the data-adapter interface (so a new data source just implements the protocol, no changes needed upstream), strict typing (`mypy`/`pyright` clean), and a versioned public API so PM-facing dashboards don't break silently when internals change."

**D) Feynman summary:** A reusable library is really just a strict one-way pipeline with clean interfaces at each stage — data in, benchmarks computed, attribution derived, reports rendered — the discipline of not letting layers reach past their neighbor is what makes it maintainable as new asset classes/venues get added (Set 9 Q10).

**E) Follow-ups:**
- *"Give a concrete example of the Protocol interface."* → See Q6 below for a similar `Protocol` pattern applied to data source adapters.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. How do you profile and speed up a slow Python research/reporting pipeline before it goes into daily production use?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```python
import cProfile
import pstats

def profile_pipeline(run_fn) -> None:
    """Profile a pipeline entry point and print the top time-consuming calls."""
    profiler = cProfile.Profile()
    profiler.enable()
    run_fn()
    profiler.disable()
    stats = pstats.Stats(profiler).sort_stats("cumulative")
    stats.print_stats(15)
```
> "Sequence: (1) profile with `cProfile`/`line_profiler` to find the actual bottleneck rather than guessing — usually it's a hidden `.apply`/loop, an unnecessary full-table read, or repeated re-computation of something cacheable; (2) vectorize anything loop-based (Q3); (3) push heavy aggregation to kdb+ if the pipeline is pulling raw ticks into Python unnecessarily (Set 4 Q10 principle — compute where the data lives); (4) cache/memoize expensive, repeated computations (e.g., `functools.cache` for pure functions on stable inputs); (5) for genuinely CPU-bound numeric loops that resist vectorization, drop to Numba (`@njit`) or move to a compiled extension; (6) for I/O-bound multi-source pulls, parallelize with `asyncio` or a thread/process pool since Python 3.13's free-threaded (no-GIL) build makes true multi-core parallelism viable for CPU-bound work without the multiprocessing overhead, if the deployment environment supports it."

**D) Feynman summary:** Never optimize by intuition — profile first, because the actual bottleneck is very often not where you'd guess; then apply the cheapest fix that solves it (vectorize) before reaching for heavier tools (Numba, no-GIL parallelism).

**E) Follow-ups:**
- *"What's Python 3.13's free-threaded mode and when would you actually use it here?"* → An experimental build removing the GIL, allowing true multi-core parallel CPU-bound execution in threads; useful for e.g. parallel per-symbol backtests/attribution runs in the reporting library, though I'd validate library compatibility (NumPy/pandas free-threaded support) before relying on it in production.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. Describe your experience integrating Python with kdb+ (e.g., via pykx or qPython).

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```python
"""kdb_adapter.py — Protocol-based data adapter so reporting code is
source-agnostic (kdb+ today, potentially another store tomorrow)."""

from typing import Protocol
import pandas as pd
import pykx as kx


class FillsSource(Protocol):
    """Interface any fills data source must implement."""

    def get_fills(self, start: str, end: str) -> pd.DataFrame:
        """Return fills between start and end (inclusive) as a DataFrame."""
        ...


class KdbFillsSource:
    """pykx-backed implementation of FillsSource."""

    def __init__(self, host: str, port: int) -> None:
        """Initialize a persistent pykx connection.

        Args:
            host: kdb+ process host.
            port: kdb+ process port.
        """
        self._conn = kx.QConnection(host=host, port=port)

    def get_fills(self, start: str, end: str) -> pd.DataFrame:
        """Fetch fills in [start, end] and return as a pandas DataFrame.

        Args:
            start: Inclusive start date string, e.g. "2026.01.01".
            end: Inclusive end date string, e.g. "2026.01.31".

        Returns:
            pandas DataFrame of fills for downstream analytics.
        """
        query = f"select from fills where date within ({start};{end})"
        return self._conn(query).pd()
```
> "I've used `pykx` (Anthropic-era successor to the older `qPython`/`PyQ`) to build exactly this kind of typed adapter — Python owns the reporting/statistics/visualization layer, kdb+ owns tick-scale storage and aggregation, and the `Protocol` boundary means the reporting code above never needs to know which backend it's talking to."

**D) Feynman summary:** pykx is the bridge that lets each tool do what it's best at — kdb+ handles massive columnar time-series data fast, Python handles statistics/ML/visualization/reporting — and a `Protocol` interface keeps the bridge from leaking implementation details into the rest of the codebase.

**E) Follow-ups:**
- *"What are the gotchas of pykx you've run into?"* → Type conversion nuances (kdb+ temporal types vs. pandas/NumPy datetime64), and being careful not to pull more rows over IPC than necessary — always aggregate in q first.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. How would you structure unit tests for a transaction-cost calculation function?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```python
"""test_implementation_shortfall.py — unit tests for the IS calculation."""

import pandas as pd
import pytest

from tca.benchmarks import implementation_shortfall


class TestImplementationShortfall:
    """Unit tests covering IS decomposition edge cases."""

    def test_full_fill_zero_delay_matches_pure_impact_cost(self) -> None:
        """A fully filled order with arrival == decision price should have
        zero delay cost, isolating pure market-impact cost."""
        fills = pd.DataFrame({"price": [101.0], "size": [100]})
        result = implementation_shortfall(
            decision_price=100.0, arrival_price=100.0,
            fills=fills, end_price=101.0, intended_size=100,
        )
        assert result.delay_cost == pytest.approx(0.0)
        assert result.opportunity_cost == pytest.approx(0.0)

    def test_partial_fill_produces_nonzero_opportunity_cost(self) -> None:
        """An order only 50% filled should show opportunity cost on the
        unfilled remainder, priced at the end-of-horizon price."""
        fills = pd.DataFrame({"price": [100.5], "size": [50]})
        result = implementation_shortfall(
            decision_price=100.0, arrival_price=100.0,
            fills=fills, end_price=102.0, intended_size=100,
        )
        assert result.opportunity_cost > 0

    def test_zero_intended_size_raises(self) -> None:
        """Zero intended size is a degenerate input and should raise,
        not silently return a divide-by-zero-derived NaN."""
        with pytest.raises(ValueError):
            implementation_shortfall(
                decision_price=100.0, arrival_price=100.0,
                fills=pd.DataFrame({"price": [], "size": []}),
                end_price=100.0, intended_size=0,
            )
```
> "Structure: (1) test each decomposition term (delay, impact, opportunity) in isolation by constructing synthetic inputs that zero out the other terms — this is the key trick, since IS is additive; (2) test edge cases explicitly: zero fills, full fills, partial fills, zero intended size; (3) test known-answer cases computed by hand for a small synthetic example, not just property checks; (4) treat it as financial-calculation code — favor explicit `pytest.approx` numeric tolerance checks over exact equality given floating point."

**D) Feynman summary:** Since Implementation Shortfall is a sum of three cleanly separable pieces, the best test strategy is to build synthetic scenarios that isolate one piece at a time by forcing the other two to be exactly zero — that way a bug in one component can't hide behind cancellation with another.

**E) Follow-ups:**
- *"How would you test the market-impact model fit (Set 3 Q5) rather than just the IS formula?"* → Property-based tests (e.g., `hypothesis`) checking monotonicity (impact should increase with size for fixed volatility) plus out-of-sample backtest error thresholds, since the model has no single "known answer" to assert against.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. What matplotlib/visualization approach would you use to show a PM their execution quality over time?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** "Static report artifact, or an interactive dashboard the PM can drill into themselves?"

**C) Detailed answer:**
```python
"""slippage_trend_chart.py — monthly slippage trend with confidence band."""

import matplotlib.pyplot as plt
import pandas as pd


def plot_slippage_trend(monthly: pd.DataFrame, pm_group: str) -> plt.Figure:
    """Plot mean slippage with a 95% CI band over time for one PM group.

    Args:
        monthly: DataFrame indexed by month with columns
            ["mean_bps", "ci_lower", "ci_upper"].
        pm_group: Label for the chart title.

    Returns:
        The matplotlib Figure object.
    """
    fig, ax = plt.subplots(figsize=(9, 4.5))
    ax.plot(monthly.index, monthly["mean_bps"], marker="o", label="Mean slippage (bps)")
    ax.fill_between(
        monthly.index, monthly["ci_lower"], monthly["ci_upper"],
        alpha=0.2, label="95% CI",
    )
    ax.axhline(0, linestyle="--", linewidth=0.8, color="gray")
    ax.set_title(f"Execution Slippage Trend — {pm_group}")
    ax.set_ylabel("Slippage (bps)")
    ax.legend()
    fig.tight_layout()
    return fig
```
> "I'd deliberately show the trend **with a confidence band, not a bare line** — a single-line chart invites over-reading noise as signal; the CI band visually communicates when a month's result is a real deviation vs. within normal variance, which ties directly into the statistical-rigor requirement from Set 6/Set 3 Q8 (never present a finding, visual or numeric, without honestly representing its uncertainty)."

**D) Feynman summary:** A chart without uncertainty bands quietly lies by implying every wiggle is meaningful; adding the CI band is the visual equivalent of saying "here's the signal, and here's how much of this could just be noise" in one glance.

**E) Follow-ups:**
- *"How would you extend this to an interactive dashboard?"* → Plotly/Dash or Streamlit for drill-down by broker/instrument/time-of-day, keeping matplotlib for static PDF/email reports and reserving interactivity for ad hoc investigation tools.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. How do you handle timezone and exchange-calendar alignment across global futures markets in Python?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
```python
"""session_alignment.py — normalize timestamps to exchange-local sessions."""

import pandas as pd


def to_exchange_session_time(
    ts: pd.Series, exchange_tz: str
) -> pd.Series:
    """Convert UTC timestamps to exchange-local time for session bucketing.

    Args:
        ts: UTC-aware timestamp Series.
        exchange_tz: IANA timezone string, e.g. "America/Chicago" for CME.

    Returns:
        Timestamp Series converted to exchange-local time.
    """
    if ts.dt.tz is None:
        ts = ts.dt.tz_localize("UTC")
    return ts.dt.tz_convert(exchange_tz)
```
> "Rule of thumb: **store everything internally in UTC**, and only convert to exchange-local time at the point of session-bucketing/reporting (e.g., computing a session-relative VWAP curve, Set 2 Q8's session-liquidity point). For exchange calendars/holidays, I'd use a maintained calendar library (e.g., `pandas_market_calendars` or an exchange-provided calendar file) rather than hand-rolled holiday lists, since futures calendars (CME/ICE) have idiosyncratic early-close/holiday schedules that drift over time and are easy to get subtly wrong by hand."

**D) Feynman summary:** Keep one universal clock (UTC) as the source of truth everywhere in the data pipeline, and only translate to "local time" at the very last mile where a human or a session-based calculation needs it — mixing timezones earlier in the pipeline is one of the most common, hardest-to-detect classes of bugs in multi-market trading systems.

**E) Follow-ups:**
- *"What's a concrete bug this discipline prevents?"* → A VWAP calculation accidentally spanning two different exchange sessions because one data source was in local time and another in UTC, silently producing a nonsensical benchmark.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. Describe a time you had to productionize a research notebook into a maintained tool.

**A) Time budget:** 2 minutes (STAR).

**B) Follow-ups:** "Would you like this framed around a TCA/execution example specifically, or is a general research-to-production example fine?"

**C) Detailed answer:**
> "**Situation:** At Highbridge, an adverse-selection model (Set 2/Resume reference) started as an exploratory Jupyter notebook — useful for one-off analysis but fragile: hardcoded paths, no tests, manual re-runs.
> **Task:** Turn it into a tool the desk could run daily without me manually babysitting it.
> **Action:** I refactored the notebook into a proper package: pure functions extracted from notebook cells into a tested module (mirroring the Q7 testing discipline), configuration externalized (no hardcoded dates/paths), a typed `Protocol`-based data-adapter layer (Q6) so it could run against either historical or live data sources, and a scheduled entry point with logging and failure alerting instead of manual execution.
> **Result:** The tool ran unattended daily, caught two real adverse-selection anomalies within the first month that would have been missed with the old ad hoc cadence, and became a template other researchers on the desk copied for their own notebook-to-production migrations."

**D) Feynman summary:** A notebook proves an idea works once, in front of you; production code has to prove the idea keeps working when you're not watching — the conversion is really about removing every implicit assumption (a path that happens to exist on your laptop, a manual step you remember to run) and replacing it with something explicit, tested, and scheduled.

**E) Follow-ups:**
- *"What's the single biggest risk in that migration?"* → Silent behavioral drift — the productionized version subtly computing something different from the notebook due to a refactoring slip; mitigated by regression-testing the new pipeline's outputs against the original notebook's known outputs before cutting over.

[🔝 Back to Top](#-table-of-contents)

---
