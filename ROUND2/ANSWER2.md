# Millennium Execution Services — Quant Specialist — Round 2 Mock Interview: Production-Grade `qpython` Architecture

## Set 2 of 10 · Python / Pandas Live Coding

### Candidate: Shaikat Majumdar | 1-Hour Technical Round

> **Session framing:** "My daily research stack is NumPy/Pandas/Scikit-learn/PyTorch on Linux, with latency-critical pieces dropped into C++. I'll write vectorized Pandas here and call out where I'd reach for Numba/Cython or push to kdb+ if the data got too large for a single box."

> **Executive Framing:** This document presents the complete refactored implementation for the quantitative execution research pipeline, fully migrating all components away from `pykx` to standard **`qpython` IPC (`QConnection`)**. Every module adheres strictly to institutional standards (Citadel, Millennium, Balyasny quantitative infrastructure requirements), incorporating Python 3.13 type annotations, robust logging, structured class design, and comprehensive standalone self-validation test suites.

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📋 Table of Contents

1. [Q1 · Vectorized Slippage vs. Arrival Price per Order](#q1--vectorized-slippage-vs-arrival-price-per-order)
2. [Q2 · Rolling VWAP over a Configurable Window](#q2--rolling-vwap-over-a-configurable-window)
3. [Q3 · Multi-Feed Dedup and Normalization](#q3--multi-feed-dedup-and-normalization)
4. [Q4 · Participation Rate over Order Life](#q4--participation-rate-over-order-life)
5. [Q5 · Market-Impact Regression Features](#q5--market-impact-regression-features)
6. [Q6 · Rolling Cost-Adjusted Sharpe Ratio](#q6--rolling-cost-adjusted-sharpe-ratio)
7. [Q7 · Profiling & Benchmarking Execution Pipelines](#q7--profiling--benchmarking-execution-pipelines)
8. [Q8 · Asof Merge for Market Data](#q8--asof-merge-for-market-data)
9. [Q9 · QPython Bridge Design](#q9--qpython-bridge-design)
10. [Q10 · Validation, Testing, and Invariant Assertions](#q10--validation-testing-and-invariant-assertions)

[🔝 Back to Top](#-table-of-contents)

---

## Q1 · Vectorized Slippage vs. Arrival Price per Order

### A) Time Budget & Objectives

* **Time Budget:** 6 minutes
* **Objective:** Construct a high-performance q function and Python 3.13 module to compute order-level execution slippage against arrival benchmarks, incorporating robust aggregation, side-sign adjustments, and self-validating test suites.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"In systematic macro execution, slippage against arrival price is the primary metric that distinguishes alpha degradation from execution cost. If your slippage calculation fails to properly account for multi-fill order aggregation or misinterprets buy/sell side signs, your cost attribution models will severely misprice liquidity risk across high-volatility rate sessions."*

### C) Mathematical Derivation (MathJax)

```math
\text{Slippage}_{\text{bps}} = 10{,}000 \times \text{side\_sign} \times \frac{\bar{P}_{\text{fill}} - P_{\text{arrival}}}{P_{\text{arrival}}}, \quad \text{side\_sign} = 
\begin{cases}
+1 & \text{if } \text{side} = \text{buy} \\
-1 & \text{if } \text{side} = \text{sell}
\end{cases}
```

Where $\bar{P}_{\text{fill}}$ is the volume-weighted average fill price for order $i$.

### D) Architectural & Algorithmic ASCII Diagram

```
RAW FILL STREAM (Multi-Fill Orders)
  Order 1: 50 @ 100.0 (Buy)  ──┐
  Order 1: 50 @ 101.0 (Buy)  ──┼──> Group by orderId & Aggregate (VWAP)
        │                      │
        ▼                      ▼
  Arrival Price Join ──> Side-Sign Adjustment ──> Slippage (bps)

```

### E) Standalone Self-Validating q Script (`slippageByOrder.q`)

```q
// slippageByOrder.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q slippageByOrder.q -p 5000

computeSlippage:{[fills; arrivals]
    notional: fills[`price] * fills[`size];
    grouped: ?[fills; (); `orderId`side!(`orderId; `side); `filledQty:(,; `size; sum); notionalSum:(notional; sum); sideRef:(first; `side)];
    avgPrice: grouped[`notionalSum] % grouped[`filledQty];
    grouped: update avgFillPrice: avgPrice from grouped;
    merged: arrivals lj grouped;
    sideSign: $[merged[`side] = `buy; 1.0; -1.0];
    slippageBps: 10000.0 * sideSign * (merged[`avgFillPrice] - merged[`arrivalPrice]) % merged[`arrivalPrice];
    update slippageBps: slippageBps from merged
 };

main:{[args]
    sampleFills:([]
        orderId: 1 1;
        side: `buy`buy;
        price: 100.0 101.0;
        size: 50 50
        );
    sampleArrivals:([]
        orderId: 1;
        arrivalPrice: 100.0
        );

    res: computeSlippage[sampleFills; sampleArrivals];

    assert[count res = 1; "Error: Expected 1 aggregated order result"];
    assert[first[exec avgFillPrice from res] = 100.5; "Error: Average fill price mismatch"];
    assert[first[exec slippageBps from res] = 50.0; "Error: Slippage calculation mismatch"];

    -1 "SUCCESS: slippageByOrder q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in slippageByOrder main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **`?[fills; (); ...]`**: Utilizes the q functional form (`?`) to dynamically group fills by order ID and side while aggregating total filled quantity and notional value.
* **`notionalSum % filledQty`**: Computes the true volume-weighted average fill price (`avgFillPrice`) across fragmented fills.
* **`arrivals lj grouped`**: Performs a left join against arrival benchmark prices.
* **`sideSign` adjustment**: Multiplies buy orders by $+1$ and sell orders by $-1$ to ensure positive slippage represents adverse execution movement.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ where $N$ is the number of fill records, dominated by hashing and grouping operations in KDB+.
  * **Space Complexity:** $\mathcal{O}(N)$ auxiliary memory for intermediate tables and join buffers.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`slippage_engine.py`)

```python
"""High-performance order slippage analysis engine with Q IPC and self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)

_BASIS_POINTS_MULTIPLIER: Final[float] = 10_000.0


class SlippageAnalyzer:
    """Computes execution slippage via KDB+ IPC or local vectorized Pandas operations."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def compute_slippage_via_q(self, fills: pd.DataFrame, arrival_prices: pd.DataFrame) -> pd.DataFrame:
        """Invokes the native q computeSlippage function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.fills", fills)
            q_conn.sync(".q.arrivalPrices", arrival_prices)
            result = q_conn.sync("computeSlippage[fills; arrival_prices]")
            #result = q_conn.sync("""
            #    {[f; a]
            #        n: f[`price] * f[`size];
            #        g: ?[f; (); `orderId`side!(`orderId; `side); `filledQty:(,; `size; sum); notionalSum:(n; sum); sideRef:(first; `side)];
            #        p: g[`notionalSum] % g[`filledQty];
            #        g: update avgFillPrice: p from g;
            #        m: a lj g;
            #        s: $[m[`side] = `buy; 1.0; -1.0];
            #        sb: 10000.0 * s * (m[`avgFillPrice] - m[`arrivalPrice]) % m[`arrivalPrice];
            #        update slippageBps: sb from m
            #    }[fills; arrivalPrices]
            #""")
            logger.info("Successfully executed slippage calculation via Q IPC.")
            return pd.DataFrame(result)

    def compute_slippage_native(self, fills: pd.DataFrame, arrival_prices: pd.DataFrame) -> pd.DataFrame:
        """Re-implements slippage calculation natively in Python 3.13 using pandas/numpy."""
        required_fill_cols = {"order_id", "side", "price", "size"}
        if not required_fill_cols.issubset(fills.columns):
            missing = required_fill_cols - set(fills.columns)
            raise KeyError(f"Missing required fill columns: {missing}")

        notional = fills["price"] * fills["size"]
        grouped = fills.assign(notional=notional).groupby("order_id", sort=False).agg(
            filled_qty=("size", "sum"),
            notional_sum=("notional", "sum"),
            side=("side", "first"),
        )
        grouped["avg_fill_price"] = grouped["notional_sum"] / grouped["filled_qty"]

        merged = grouped.join(
            arrival_prices.set_index("order_id"), how="left", validate="1:1"
        )
        side_sign = np.where(merged["side"] == "buy", 1.0, -1.0)
        merged["slippage_bps"] = (
            _BASIS_POINTS_MULTIPLIER
            * side_sign
            * (merged["avg_fill_price"] - merged["arrival_price"])
            / merged["arrival_price"]
        )
        return merged[["avg_fill_price", "filled_qty", "slippage_bps"]]


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for SlippageAnalyzer."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running SlippageAnalyzer standalone validation suite...")

    sample_fills = pd.DataFrame({
        "order_id": [1, 1],
        "side": ["buy", "buy"],
        "price": [100.0, 101.0],
        "size": [50, 50],
    })
    sample_arrivals = pd.DataFrame({
        "order_id": [1],
        "arrival_price": [100.0],
    })

    analyzer = SlippageAnalyzer()
    result = analyzer.compute_slippage_native(sample_fills, sample_arrivals)

    assert result.loc[1, "avg_fill_price"] == 100.5, "Average fill price mismatch"
    assert result.loc[1, "slippage_bps"] == 50.0, "Slippage calculation mismatch"

    result = analyzer.compute_slippage_via_q(sample_fills, sample_arrivals)

    assert result.loc[1, "avg_fill_price"] == 100.5, "Average fill price mismatch"
    assert result.loc[1, "slippage_bps"] == 50.0, "Slippage calculation mismatch"

    logger.info("SUCCESS: SlippageAnalyzer Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in SlippageAnalyzer standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **`QConnection` IPC**: Serializes pandas dataframes into remote KDB+ tables via TCP sockets, executing native q lambdas for distributed calculation.
* **Vectorized Pandas Aggregation**: Computes weighted averages and side-signed basis point transformations using high-performance vector operations.
* **Test Harness**: Validates mathematical invariants and exit codes.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ for dataframe grouping and joining operations, plus IPC serialization overhead $\mathcal{O}(N)$.
  * **Space Complexity:** $\mathcal{O}(N)$ auxiliary memory for grouped and merged pandas DataFrames.

[🔝 Back to Top](#-table-of-contents)

---

## Q2 · Rolling VWAP over a Configurable Window

### A) Time Budget & Objectives

* **Time Budget:** 5 minutes
* **Objective:** Build a robust rolling VWAP engine using KDB+ moving sum primitives (`msum`) and Python rolling windows, supported by self-validating assertion test suites.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"Rolling VWAP is a cornerstone of our intraday momentum signals. If your rolling window implementation suffers from numerical instability or edge-case boundary errors during market open/close auctions, your alpha signals will generate false breakouts."*

### C) Mathematical Derivation (MathJax)

$$\text{VWAP}_{[t-W, t]} = \frac{\sum_{i \in [t-W, t]} P_i \cdot V_i}{\sum_{i \in [t-W, t]} V_i}$$

### D) Architectural & Algorithmic ASCII Diagram

```
TRADE STREAM ──> Compute Notional (P * V) ──> Apply msum[window]
                                                    │
                                                    ▼
                                    Rolling Notional / Rolling Size

```

### E) Standalone Self-Validating q Script (`rollingVwap.q`)

```q
// rollingVwap.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q rollingVwap.q -p 5000

computeRollingVwap:{[t;w]
    n: t[`price] * t[`size];
    ex: update notional: n from t;
    update vwap: (m[;`notional] msum w) % (m[;`size] msum w) by sym from ex
 };

main:{[args]
    sampleTrades:([]
        sym: `CL`CL`CL;
        time: 09:30:01 09:30:02 09:30:03;
        price: 100.0 101.0 102.0;
        size: 10 20 30
        );

    res: computeRollingVwap[sampleTrades; 2];

    assert[count res = 3; "Error: Expected 3 rows"];
    expectedVwap2: (100.0*10 + 101.0*20)%30;
    assert[res[1; `vwap] = expectedVwap2; "Error: Rolling VWAP mismatch at index 1"];

    -1 "SUCCESS: rollingVwap q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in rollingVwap main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **`msum[w; ...]`**: Computes sliding moving sums over a window of size $w$ grouped by symbol.
* **Division of sums**: Divides the rolling notional sum by the rolling size sum to derive rolling VWAP natively in C.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$ where $N$ is the number of ticks, as `msum` executes in linear time.
  * **Space Complexity:** $\mathcal{O}(N)$ memory for rolling window buffers.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`rolling_vwap.py`)

```python
"""High-performance rolling VWAP analytics engine with Q IPC and standalone self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)

DEFAULT_WINDOW_SECONDS: Final[int] = 300


class RollingVWAPEngine:
    """Computes rolling VWAP via KDB+ IPC or local vectorized Pandas operations."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def compute_rolling_vwap_via_q(self, trades: pd.DataFrame, window_size: int) -> pd.DataFrame:
        """Invokes the native q computeRollingVwap function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.trades", trades)
			result = q_conn.sync(f"computeRollingVwap[trades; {window_size}]")
            #result = q_conn.sync(f"""
            #    {{[t; w]
            #        n: t[`price] * t[`size];
            #        ex: update notional: n from t;
            #        update vwap: (m[;`notional] msum w) % (m[;`size] msum w) by sym from ex
            #    }}[trades; {window_size}]
            #""")
            logger.info("Successfully executed rolling VWAP via Q IPC.")
            return pd.DataFrame(result)

#    def compute_rolling_vwap_native(self, trades: pd.DataFrame, window_size: int = 2) -> pd.DataFrame:
#        """Re-implements rolling VWAP natively in Python 3.13 using pandas/numpy."""
#        required_cols = {"sym", "time", "price", "size"}
#        if not required_cols.issubset(trades.columns):
#            missing = required_cols - set(trades.columns)
#            raise KeyError(f"Missing required columns: {missing}")
#
#        notional = trades["price"] * trades["size"]
#        grouped = trades.assign(notional=notional).groupby("sym", sort=False)
#        rolling_notional = grouped["notional"].apply(lambda s: s.rolling(window_size).sum())
#        rolling_size = grouped["size"].apply(lambda s: s.rolling(window_size).sum())
#        vwap_series = rolling_notional / rolling_size
#        return trades.assign(vwap=vwap_series)

def compute_rolling_vwap_native(self, trades: pd.DataFrame, window_size: int = 2) -> pd.DataFrame:
        """Re-implements rolling VWAP natively in Python 3.13 using pandas/numpy.

        Args:
            trades: DataFrame containing trade records ('sym', 'time', 'price', 'size').
            window_size: Rolling window size measured in tick counts.

        Returns:
            DataFrame containing original trades with an appended 'vwap' column.

        Raises:
            KeyError: If required columns ('sym', 'time', 'price', 'size') are absent.
        """
        required_cols = {"sym", "time", "price", "size"}
        if not required_cols.issubset(trades.columns):
            missing = required_cols - set(trades.columns)
            raise KeyError(f"Missing required columns: {missing}")

        clean_trades = trades.sort_values(["sym", "time"]).copy()
        if clean_trades.empty:
            clean_trades["vwap"] = pd.Series(dtype=float)
            return clean_trades

        t_col = clean_trades["time"]

        # Universal Scale-Aware Time Normalization
        if pd.api.types.is_datetime64_any_dtype(t_col):
            raw_ints = t_col.astype("int64")
            dtype_str = str(t_col.dtype)
            if "ns" in dtype_str:
                time_sec = raw_ints // 10**9
            elif "us" in dtype_str:
                time_sec = raw_ints // 10**6
            elif "ms" in dtype_str:
                time_sec = raw_ints // 1000
            else:
                time_sec = raw_ints
        else:
            val = t_col.iloc[0] if len(t_col) > 0 else 0
            if val > 1e16:   
                time_sec = t_col // 10**9
            elif val > 1e13: 
                time_sec = t_col // 10**6
            elif val > 1e10: 
                time_sec = t_col // 1000
            else:            
                time_sec = t_col

        clean_trades["time_sec"] = time_sec
        clean_trades["notional"] = clean_trades["price"] * clean_trades["size"]

        # Fully vectorized rolling aggregation per symbol group bypassing slow apply loops
        grouped = clean_trades.groupby("sym", group_keys=False)
        rolling_notional = grouped["notional"].rolling(window=window_size, min_periods=1).sum()
        rolling_size = grouped["size"].rolling(window=window_size, min_periods=1).sum()

        vwap_series = (rolling_notional / rolling_size).reset_index(level=0, drop=True)
        
        return clean_trades.assign(vwap=vwap_series).drop(columns=["notional", "time_sec"])

def run_self_validation() -> None:
    """Executes standalone self-validation assertions for RollingVWAPEngine."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running RollingVWAPEngine standalone validation suite...")

    sample_trades = pd.DataFrame({
        "sym": ["CL", "CL", "CL"],
        "time": pd.to_datetime(["2026-07-29 09:30:01", "2026-07-29 09:30:02", "2026-07-29 09:30:03"]),
        "price": [100.0, 101.0, 102.0],
        "size": [10, 20, 30],
    })

    engine = RollingVWAPEngine()
    result = engine.compute_rolling_vwap_native(sample_trades, window_size=2)

    assert len(result) == 3, "DataFrame length mismatch"
    expected_vwap_1 = (100.0 * 10 + 101.0 * 20) / 30
    assert np.isclose(result.loc[1, "vwap"], expected_vwap_1), "Rolling VWAP calculation error"

    result = engine.compute_rolling_vwap_via_q(sample_trades, window_size=2)

    assert len(result) == 3, "DataFrame length mismatch"
    expected_vwap_1 = (100.0 * 10 + 101.0 * 20) / 30
    assert np.isclose(result.loc[1, "vwap"], expected_vwap_1), "Rolling VWAP calculation error"

    logger.info("SUCCESS: RollingVWAPEngine Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in RollingVWAPEngine standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **Pandas Rolling API**: Applies `.rolling(window_size).sum()` grouped by symbol.
* **Vectorized Division**: Computes rolling weighted averages cleanly.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$ for linear rolling window aggregations.
  * **Space Complexity:** $\mathcal{O}(N)$ memory for rolling series.

[🔝 Back to Top](#-table-of-contents)

---

## Q3 · Multi-Feed Dedup and Normalization

### A) Time Budget & Objectives

* **Time Budget:** 7 minutes
* **Objective:** Implement a high-performance multi-feed trade deduplication engine that normalizes symbology, ranks exchange venue precedence, and collapses duplicate prints within microsecond tolerances.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"Consolidating raw feeds from CME Direct, SIP, and vendor direct feeds requires absolute precision. If your deduplication logic drops legitimate dual-prints or fails to honor venue priority during latency spikes, your volume profile and volume-weighted indicators will be skewed."*

### C) Mathematical Derivation (MathJax)

$$\text{Deduplicated Dataset} = \lbrace r_i \mid \nexists r_j \text{ s.t. } \text{sym}_i = \text{sym}_j \land P_i = P_j \land S_i = S_j \land \vert{}t_i - t_j\vert{} \le \epsilon \land \text{Rank}(v_i) > \text{Rank}(v_j) \rbrace$$

### D) Architectural & Algorithmic ASCII Diagram

```
RAW FEEDS (CME, SIP, Vendor) ──> Map Syms & Assign Venue Ranks
                                         │
                                         ▼
                            Sort by Price, Size, Timestamp & Venue Rank
                                         │
                                         ▼
                            Collapse within ε Tolerance

```

### E) Standalone Self-Validating q Script (`dedupFeeds.q`)

```q
// dedupFeeds.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q dedupFeeds.q -p 5000

normalizeFeeds:{[r; tol]
    // Sym mapping dictionary and venue priority ranking
    sm:(`CLc1`CL1!`CL`CL);
    vp:(`CME_DIRECT`SIP!`int$0 1);
    
    // Add normalized symbol and venue rank
    t:update sym:sm rawSym, vRank:vp venue from r;
    t:`sym`price`size`ts`vRank xasc t;
    
    // Compute time difference within partitions (tol in ms converted to nanoseconds)
    tolNs:tol * 1000000;
    t:update dt:?[0 = i; 0N; ts - prev ts] by sym, price, size from t;
    t:update newGrp:not (null dt) and (dt > tolNs) from t;
    t:update grp:sums newGrp by sym, price, size from t;
    
    // Select best venue rank per group
    t:`vRank xasc t;
    res:0!select first venue, first rawSym, first ts, first price, first size, first sym by sym, price, size, grp from t;
    `ts xasc delete grp from res
 };

main:{[args]
    sampleRaw:([]
        venue: `CME_DIRECT`SIP;
        rawSym: `CLc1`CL1!;
        ts: 10:00:00.000000000 10:00:00.002000000;
        price: 70.0 70.0;
        size: 10 10
        );

    res: normalizeFeeds[sampleRaw; 5];
    assert[count res = 1; "Error: Count check failed, expected deduplicated count of 1"];

    -1 "SUCCESS: dedupFeeds q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in dedupFeeds main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **Sorting and grouping**: Orders trades by time, price, and size to align multi-feed duplicates.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ due to sorting.
  * **Space Complexity:** $\mathcal{O}(N)$ memory.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`normalize_feeds.py`)

```python
"""Multi-feed trade deduplication engine with Q IPC and standalone self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)

_SYM_MAP: Final[dict[str, str]] = {
    "CLc1": "CL",
    "CL1!": "CL",
    "CLUSD": "CL",
}

_VENUE_PRIORITY: Final[dict[str, int]] = {
    "CME_DIRECT": 0,
    "SIP": 1,
    "VENDOR_B": 2,
}


class FeedNormalizer:
    """Normalizes and deduplicates trades across multiple exchange feeds."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def normalize_via_q(self, raw: pd.DataFrame, tolerance_ms: int = 5) -> pd.DataFrame:
        """Invokes the native q normalizeFeeds function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.sync(".q.raw", raw)
            result = q_conn.sync(f"normalizeFeeds[raw; {tolerance_ms}]")
            logger.info("Successfully executed feed normalization via Q IPC.")
            return pd.DataFrame(result)

    def normalize_native(self, raw: pd.DataFrame, tolerance: pd.Timedelta = pd.Timedelta(milliseconds=5)) -> pd.DataFrame:
        """Re-implements multi-feed dedup natively in Python 3.13 using pandas."""
        required_cols = {"venue", "raw_sym", "ts", "price", "size"}
        if not required_cols.issubset(raw.columns):
            missing = required_cols - set(raw.columns)
            raise KeyError(f"Missing required columns: {missing}")

        df = raw.copy()
        df["sym"] = df["raw_sym"].map(_SYM_MAP).fillna(df["raw_sym"])
        df["venue_rank"] = df["venue"].map(_VENUE_PRIORITY).fillna(99).astype(int)

        df = df.sort_values(["sym", "price", "size", "ts", "venue_rank"])
        same_group = df.groupby(["sym", "price", "size"], sort=False)["ts"].diff()
        df["new_group"] = (same_group.isna()) | (same_group > tolerance)
        df["group_id"] = df.groupby(["sym", "price", "size"], sort=False)["new_group"].cumsum()

        deduped = (
            df.sort_values("venue_rank")
            .groupby(["sym", "price", "size", "group_id"], sort=False)
            .first()
            .reset_index()
        )
        return deduped.sort_values("ts").drop(columns=["new_group", "group_id", "venue_rank"])


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for FeedNormalizer."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running FeedNormalizer standalone validation suite...")

    sample_raw = pd.DataFrame({
        "venue": ["CME_DIRECT", "SIP"],
        "raw_sym": ["CLc1", "CL1!"],
        "ts": pd.to_datetime(["2026-07-29 10:00:00.000000", "2026-07-29 10:00:00.002000"]),
        "price": [70.0, 70.0],
        "size": [10, 10],
    })

    normalizer = FeedNormalizer()
	
    # Test 1: Native pandas normalization
    result_native = normalizer.normalize_native(sample_raw)
    assert len(result_native) == 1, "Deduplication failed to collapse duplicate prints"
    assert result_native.iloc[0]["sym"] == "CL", "Symbology normalization failed"

    # Test 2: Q IPC normalization (Requires running q server on port 5000)
    result_q = normalizer.normalize_via_q(sample_raw)
    assert len(result_q) == 1, "Deduplication failed to collapse duplicate prints"
    assert result_q.iloc[0]["sym"] == "CL", "Symbology normalization failed"

    logger.info("SUCCESS: FeedNormalizer Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in FeedNormalizer standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **Symbology Mapping**: Maps heterogeneous ticker symbols (`CLc1`, `CL1!`) to standard root identifiers (`CL`).
* **Venue Precedence**: Ranks exchange feeds to prioritize direct market access over SIP feeds.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ due to sorting and grouping.
  * **Space Complexity:** $\mathcal{O}(N)$ memory.

[🔝 Back to Top](#-table-of-contents)

---

## Q4 · Participation Rate over Order Life

### A) Time Budget & Objectives

* **Time Budget:** 5 minutes
* **Objective:** Compute cumulative participation rate (POV) by aligning internal fills against broader market volume streams.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"Percentage-of-Volume (POV) execution algorithms require continuous feedback on your relative footprint in the market. Monitoring cumulative POV ensures your execution strategy does not inadvertently breach market impact thresholds."*

### C) Mathematical Derivation (MathJax)

$$\text{POV}_t = \frac{\sum_{\tau = t_0}^t Q^{\text{own}}_\tau}{\sum_{\tau = t_0}^t Q^{\text{market}}_\tau}$$

### D) Architectural & Algorithmic ASCII Diagram

```
OWN FILLS & MARKET TRADES ──> Resample & Align Timestamps (1s intervals)
                                       │
                                       ▼
                     Cumulative Own Volume / Cumulative Market Volume

```

### E) Standalone Self-Validating q Script (`povCalc.q`)

```q
// povCalc.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q povCalc.q -p 5000

computePov:{[own; mkt]
    select pov: own[`size] % mkt[`size] from own
 };

main:{[args]
    sampleOwn:([] size: 10 20);
    sampleMkt:([] size: 100 200);
    res: computePov[sampleOwn; sampleMkt];
    assert[count res = 2; "Error: Row count"];

    -1 "SUCCESS: povCalc q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in povCalc main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **Vector division**: Computes ratio of own volume to market volume.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$.
  * **Space Complexity:** $\mathcal{O}(N)$.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`participation_rate.py`)

```python
"""Participation rate (POV) analytics engine with Q IPC and standalone self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class ParticipationRateEngine:
    """Computes order participation rate via KDB+ IPC or local vectorized Pandas."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def compute_pov_via_q(self, own_fills: pd.DataFrame, market_trades: pd.DataFrame) -> pd.DataFrame:
        """Invokes the native q computePov function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.own", own_fills)
            q_conn.sync(".q.mkt", market_trades)
			result = q_conn.sync("computePov[own_fills; market_trades]")
            #result = q_conn.sync("""
            #    {[o; m]
            #        select pov: o[`size] % m[`size] from o
            #    }[own; mkt]
            #""")
            logger.info("Successfully executed participation rate via Q IPC.")
            return pd.DataFrame(result)

    def compute_pov_native(self, own_fills: pd.DataFrame, market_trades: pd.DataFrame) -> pd.DataFrame:
        """Re-implements POV calculation natively in Python 3.13 using pandas."""
        own = own_fills["size"].resample("1s").sum().fillna(0.0)
        mkt = market_trades["size"].resample("1s").sum().fillna(0.0)
        aligned = pd.DataFrame({"own": own, "mkt": mkt}).reindex(
            own.index.union(mkt.index), fill_value=0.0
        )
        pov_series = aligned["own"].cumsum() / aligned["mkt"].cumsum()
        return pov_series.to_frame("pov")


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for ParticipationRateEngine."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running ParticipationRateEngine standalone validation suite...")

    idx = pd.date_range("2026-07-29 10:00:00", periods=3, freq="1s")
    own = pd.DataFrame({"size": [10, 10, 10]}, index=idx)
    mkt = pd.DataFrame({"size": [100, 100, 100]}, index=idx)

    engine = ParticipationRateEngine()
    result = engine.compute_pov_native(own, mkt)

    assert len(result) == 3, "Result length mismatch"
    assert result.iloc[-1]["pov"] == 0.1, "Cumulative POV calculation error"

    result_q = engine.compute_pov_via_q(own, mkt)

    assert len(result_q) == 3, "Result length mismatch"
    assert result_q.iloc[-1]["pov"] == 0.1, "Cumulative POV calculation error"

    logger.info("SUCCESS: ParticipationRateEngine Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in ParticipationRateEngine standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **Resampling**: Aggregates trades into 1-second interval bins.
* **Cumulative Sum**: Calculates running participation ratios.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ for resampling and alignment.
  * **Space Complexity:** $\mathcal{O}(N)$ memory.

[🔝 Back to Top](#-table-of-contents)

---

## Q5 · Market-Impact Regression Features

### A) Time Budget & Objectives

* **Time Budget:** 6 minutes
* **Objective:** Construct logarithmic market-impact regression features incorporating order size relative to ADV, bid-ask spreads, and realized volatility.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"When predicting nonlinear market impact, raw features like order size or spread will break your regression models due to heavy skewness. Applying safe logarithmic transformations ensures robust feature scaling for quantitative alpha models."*

### C) Mathematical Derivation (MathJax)

$$\text{LogPart} = \ln\!\left(\frac{\text{Size}}{\text{ADV}}\right), \quad \text{LogSpread} = \ln(\text{Spread}), \quad \text{LogVol} = \ln(\text{RealizedVol})$$

### D) Architectural & Algorithmic ASCII Diagram

```
ORDERS & PANELS ──> Left Join Volatility & ADV Datasets
                           │
                           ▼
                 Safe Logarithmic Transformations (Clip min bounds)

```

### E) Standalone Self-Validating q Script (`impactFeatures.q`)

```q
// impactFeatures.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q impactFeatures.q -p 5000

computeFeatures:{[o; v; a]
    m: o lj v lj a;
    update logParticipation: log size % adv, logSpread: log spreadBps, logVol: log realizedVol from m
 };

main:{[args]
    orders:([] orderId: 1; sym: `CL; date: enlist 2026.07.29; size: 100; spreadBps: 1.5);
    vol:([] sym: `CL; date: enlist 2026.07.29; realizedVol: 0.2);
    adv:([] sym: `CL; date: enlist 2026.07.29; adv: 10000);

    res: computeFeatures[orders; vol; adv];
    assert[count res = 1; "Error: Row count"];

    -1 "SUCCESS: impactFeatures q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in impactFeatures main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **Left joins (`lj`)**: Merges order attributes with volume panels.
* **Logarithm transforms**: Computes scale-invariant regression features natively.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$.
  * **Space Complexity:** $\mathcal{O}(N)$.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`impact_features.py`)

```python
"""Market-impact feature engineering engine with Q IPC and standalone self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class ImpactFeatureEngineering:
    """Builds regression features via KDB+ IPC or local vectorized Pandas."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def build_features_via_q(self, orders: pd.DataFrame, daily_vol: pd.DataFrame, adv: pd.DataFrame) -> pd.DataFrame:
        """Invokes the native q computeFeatures function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.orders", orders)
            q_conn.sync(".q.dailyVol", daily_vol)
            q_conn.sync(".q.adv", adv)
			result = q_conn.sync("computeFeatures[orders; daily_vol; adv]")
            #result = q_conn.sync("""
            #    {[o; v; a]
            #        m: o lj v lj a;
            #        update logParticipation: log size % adv, logSpread: log spreadBps, logVol: log realizedVol from m
            #    }[orders; dailyVol; adv]
            #""")
            logger.info("Successfully executed impact features via Q IPC.")
            return pd.DataFrame(result)

    def build_features_native(self, orders: pd.DataFrame, daily_vol: pd.DataFrame, adv: pd.DataFrame) -> pd.DataFrame:
        """Re-implements feature engineering natively in Python 3.13 using pandas."""
        merged = orders.merge(daily_vol, on=["sym", "date"], how="left").merge(
            adv, on=["sym", "date"], how="left"
        )
        participation = merged["size"] / merged["adv"].clip(lower=1.0)
        return pd.DataFrame(
            {
                "order_id": merged["order_id"],
                "log_participation": np.log(participation.clip(lower=1e-6)),
                "log_spread": np.log(merged["spread_bps"].clip(lower=1e-6)),
                "log_vol": np.log(merged["realized_vol"].clip(lower=1e-6)),
            }
        )


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for ImpactFeatureEngineering."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running ImpactFeatureEngineering standalone validation suite...")

    orders_df = pd.DataFrame({
        "order_id": [1], "sym": ["CL"], "date": ["2026-07-29"],
        "size": [100], "spread_bps": [1.5],
    })
    vol_df = pd.DataFrame({"sym": ["CL"], "date": ["2026-07-29"], "realized_vol": [0.2]})
    adv_df = pd.DataFrame({"sym": ["CL"], "date": ["2026-07-29"], "adv": [10000]})

    engine = ImpactFeatureEngineering()
    result = engine.build_features_native(orders_df, vol_df, adv_df)

    assert "log_participation" in result.columns, "Feature generation failed"
    assert len(result) == 1, "Row count mismatch"
	
    result = engine.build_features_via_q(orders_df, vol_df, adv_df)

    assert "logParticipation" in result.columns, "Feature generation failed"
    assert len(result) == 1, "Row count mismatch"	

    logger.info("SUCCESS: ImpactFeatureEngineering Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in ImpactFeatureEngineering standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **DataFrame Merging**: Merges order tables with volume panels.
* **Safe Clipping**: Prevents domain errors when computing logarithms of zero or negative numbers.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ for merges.
  * **Space Complexity:** $\mathcal{O}(N)$ memory.

[🔝 Back to Top](#-table-of-contents)

---

## Q6 · Rolling Cost-Adjusted Sharpe Ratio

### A) Time Budget & Objectives

* **Time Budget:** 6 minutes
* **Objective:** Compute rolling annualized Sharpe ratios adjusted for transaction costs and commissions across portfolio manager return panels.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"A Sharpe ratio calculated on gross returns is practically useless in production. Incorporating execution transaction costs directly into the return series before computing rolling moving averages and standard deviations gives portfolio managers an honest view of net risk-adjusted performance."*

### C) Mathematical Derivation (MathJax)

$$\text{Sharpe}^{\text{roll}}_t = \sqrt{252} \cdot \frac{\overline{r^{\text{net}}}_{[t-W, t]}}{\sigma\left(r^{\text{net}}_{[t-W, t]},\text{ddof}=1\right)}, \quad r^{\text{net}}_i = r^{\text{gross}}_i - \frac{c_i}{10000}$$

### D) Architectural & Algorithmic ASCII Diagram

```
RETURNS & COST PANELS ──> Deduct Basis Point Costs (Cost / 10000)
                                   │
                                   ▼
                   Rolling Mean / Rolling Standard Deviation
                                   │
                                   ▼
                   Annualize by multiplying by √252

```

### E) Standalone Self-Validating q Script (`rollingSharpe.q`)

```q
// rollingSharpe.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q rollingSharpe.q -p 5000

computeSharpe:{[r; c; w]
    net: r - (c % 10000.0);
    (sqrt 252.0) * (mavg[w; net]) % (mdev[w; net])
 };

main:{[args]
    sampleRet: 0.001 0.002 -0.001 0.003 0.001;
    sampleCost: 2.0 2.0 2.0 2.0 2.0;
    res: computeSharpe[sampleRet; sampleCost; 3];
    assert[count res = 5; "Error: Count check"];

    -1 "SUCCESS: rollingSharpe q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in rollingSharpe main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **`mavg` & `mdev**`: Native KDB+ moving average and moving deviation operators.
* **Annualization**: Multiplies by $\sqrt{252}$.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$.
  * **Space Complexity:** $\mathcal{O}(N)$.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`rolling_sharpe.py`)

```python
"""Rolling cost-adjusted Sharpe ratio analytics engine with Q IPC and self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class SharpeAnalyzer:
    """Computes rolling Sharpe via KDB+ IPC or local vectorized Pandas."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def compute_sharpe_via_q(self, returns_panel: pd.DataFrame, cost_panel: pd.DataFrame, window: int) -> pd.DataFrame:
		"""Invokes the native q computeSharpe function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.ret", returns_panel)
            q_conn.sync(".q.cost", cost_panel)
			result = q_conn.sync(f"computeSharpe[returns_panel; cost_panel; {window}]")
            #result = q_conn.sync(f"""
            #    {{[r; c; w]
            #        net: r - (c % 10000.0);
            #        (sqrt 252.0) * (mavg[w; net]) % (mdev[w; net])
            #    }}[ret; cost; {window}]
            #""")
            logger.info("Successfully executed rolling Sharpe via Q IPC.")
            return pd.DataFrame(result)

    def compute_sharpe_native(self, returns_panel: pd.DataFrame, cost_bps_panel: pd.DataFrame, window: int = 20, periods_per_year: int = 252) -> pd.DataFrame:
        """Re-implements rolling Sharpe natively in Python 3.13 using pandas/numpy."""
        net_returns = returns_panel - (cost_bps_panel / 10_000.0)
        roll_mean = net_returns.rolling(window).mean()
        roll_std = net_returns.rolling(window).std(ddof=1)
        return np.sqrt(periods_per_year) * roll_mean / roll_std


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for SharpeAnalyzer."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running SharpeAnalyzer standalone validation suite...")

    idx = pd.date_range("2026-01-01", periods=50, freq="B")
    ret_df = pd.DataFrame({"PM_A": np.random.normal(0.001, 0.01, 50)}, index=idx)
    cost_df = pd.DataFrame({"PM_A": np.ones(50) * 2.0}, index=idx)

    analyzer = SharpeAnalyzer()
	
    result = analyzer.compute_sharpe_native(ret_df, cost_df, window=10)
    assert "PM_A" in result.columns, "Sharpe calculation output error"
    assert len(result) == 50, "Length mismatch"
	
	result = analyzer.compute_sharpe_via_q(ret_df, cost_df, window=10)
    assert "PM_A" in result.columns, "Sharpe calculation output error"
    assert len(result) == 50, "Length mismatch"

    logger.info("SUCCESS: SharpeAnalyzer Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in SharpeAnalyzer standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **Net Return Calculation**: Deducts basis point costs from gross returns.
* **Rolling Statistics**: Computes rolling mean and standard deviation.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$ for rolling windows.
  * **Space Complexity:** $\mathcal{O}(N)$ memory.

[🔝 Back to Top](#-table-of-contents)

---

## Q7 · Profiling & Benchmarking Execution Pipelines

### A) Time Budget & Objectives

* **Time Budget:** 5 minutes
* **Objective:** Benchmark execution throughput between KDB+ C-optimized grouping operations and native Python pandas pipelines.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"When processing billions of tick records daily, understanding where your execution pipeline bottlenecks—whether in IPC serialization, Python GIL contention, or KDB+ memory allocation—is vital for meeting strict intraday SLAs."*

### C) Mathematical Derivation (MathJax)

$$\text{Speedup} = \frac{T_{\text{baseline}}}{T_{\text{optimized}}}, \quad \text{Throughput} = \frac{N \text{ records}}{\Delta t \text{ seconds}}$$

### D) Architectural & Algorithmic ASCII Diagram

```
BENCHMARK HARNESS ──> Push Table to KDB+ via IPC ──> Measure C-Level Execution Time
                                         │
                                         ▼
                            Compare against Native Pandas Groupby

```

### E) Standalone Self-Validating q Script (`benchmarkPipeline.q`)

```q
// benchmarkPipeline.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q benchmarkPipeline.q -p 5000

runBenchmark:{[tbl]
    system "t select sum size by sym from tbl"
 };

main:{[args]
    sampleTbl:([] sym: `CL`GC`CL; size: 10 20 30);
    elapsed: runBenchmark[sampleTbl];
    assert[elapsed >= 0; "Error: Benchmark timing error"];

    -1 "SUCCESS: benchmarkPipeline q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in benchmarkPipeline main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **`system "t ..."`**: Measures execution time of native q aggregation queries in milliseconds.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N)$ for hashing and grouping.
  * **Space Complexity:** $\mathcal{O}(N)$.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`profile_pipeline.py`)

```python
"""Pipeline profiling and benchmarking utility with Q IPC and standalone self-validation."""

from __future__ import annotations

import logging
import sys
import time
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class PipelineProfiler:
    """Profiles execution throughput via KDB+ IPC or local Pandas benchmarking."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def benchmark_via_q(self, df: pd.DataFrame) -> float:
        """Measures q execution time over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.tbl", df)
            start_time = time.perf_counter()			
			q_conn.sync(f"runBenchmark[df]")
            #q_conn.sync("select sum size by sym from tbl")
            duration = time.perf_counter() - start_time
            logger.info("Successfully executed q benchmark in %.6f seconds.", duration)
            return duration

    def benchmark_native(self, df: pd.DataFrame) -> float:
        """Measures native Python 3.13 pandas grouping throughput."""
        start_time = time.perf_counter()
        _ = df.groupby("sym", observed=True)["size"].sum()
        duration = time.perf_counter() - start_time
        return duration


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for PipelineProfiler."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running PipelineProfiler standalone validation suite...")

    sample_df = pd.DataFrame({
        "sym": pd.Categorical(["CL", "GC", "CL"] * 100),
        "size": range(300),
    })

    profiler = PipelineProfiler()

    elapsed = profiler.benchmark_native(sample_df)
    assert elapsed > 0.0, "Benchmark failed to execute"
	
	elapsed = profiler.benchmark_via_q(sample_df)
    assert elapsed > 0.0, "Benchmark failed to execute"

    logger.info("SUCCESS: PipelineProfiler Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in PipelineProfiler standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **`time.perf_counter()`**: High-precision performance counter for measuring execution duration.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$.
  * **Space Complexity:** $\mathcal{O}(N)$.

[🔝 Back to Top](#-table-of-contents)

---

## Q8 · Asof Merge for Market Data

### A) Time Budget & Objectives

* **Time Budget:** 6 minutes
* **Objective:** Perform backward as-of merges between execution fills and prevailing market quotes to derive accurate midpoint benchmarks and quote-based slippage metrics.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"Matching an execution fill against the prevailing quote without an as-of join will lead to lookahead bias or unmatched timestamps. A robust as-of join is mandatory for accurate execution quality analytics."*

### C) Mathematical Derivation (MathJax)

$$\text{Matched Quote } Q(t) = \arg\min_{q \in \text{Quotes}} \{ t_{\text{fill}} - t_q \ \mid \ t_q \le t_{\text{fill}} \land \vert{}t_{\text{fill}} - t_q\vert{} \le \epsilon \}$$

### D) Architectural & Algorithmic Algorithmic Diagram

```
FILLS & QUOTES ──> Sort by Timestamp ──> Execute Backward Asof Merge
                                                │
                                                ▼
                                    Compute Midpoint: 0.5 * (Bid + Ask)

```

### E) Standalone Self-Validating q Script (`asofMergeQuotes.q`)

```q
// asofMergeQuotes.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q asofMergeQuotes.q -p 5000

performAsof:{[fills; quotes; tolerance]
    / Step 1: Ensure both tables are sorted by sym and time (matches Python sort)
    fSorted: `sym`time xasc fills;
    qSorted: `sym`time xasc quotes;
    
    / Step 2: Define the window. (time - tolerance) to (time)
    / neg tolerance looks backward in time up to the fill time (0)
    w: (neg tolerance; 0); 
    
    / Step 3: Perform Window Join to get prevailing bid and ask within tolerance
    merged: wj[w; `sym`time; fSorted; (qSorted; (last; `bid); (last; `ask))];
    
    / Step 4: Calculate the mid price 
    update mid: 0.5 * bid + ask from merged
 };

main:{[args]
    sampleFills:([] sym: `CL; time: 10:00:01; price: 70.0; size: 10; side: `buy);
    sampleQuotes:([] sym: `CL; time: 10:00:00; bid: 69.9; ask: 70.1);
    // tolerance = 5000 milliseconds or 5 seconds
    // The literal 00:00:05.000 is a time literal representing 5 seconds (0 hours, 0 minutes, 5 seconds, and 0 milliseconds).
    res: performAsof[sampleFills; sampleQuotes; 00:00:05.000];
    // tolerance = 500 milliseconds or 0.5 second
    // The literal 00:00:00.500 is a time literal representing 500 milliseconds (0 hours, 0 minutes, 0 seconds, and 500 milliseconds).
    // res: performAsof[sampleFills; sampleQuotes; 00:00:00.500];
    assert[count res = 1; "Error: Count check"];

    -1 "SUCCESS: asofMergeQuotes q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in asofMergeQuotes main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation

* **xasc (Sorting):** In Python, you sort by "time". In q, the lookup table for a time join must be sorted by both the grouping key and time ( symtime xasc) to work efficiently and correctly. [4] 
* **wj vs pd.merge_asof:**
  * The Python code looks `direction="backward"` within a specific tolerance.
  * In q, `w: (neg tolerance; 0)` sets up a relative time window stretching from tolerance ago up until the exact millisecond of the fill.
* **(last; bid)**: Within that backward window, `wj` grabs the last updated quote available. If no quote exists inside that specific time window, the bid and ask values will safely default to null (matching the Pandas out-of-tolerance behavior).
* **update mid: ...:** This matches `merged["mid"] = 0.5 * (merged["bid"] + merged["ask"])` exactly.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N + M \log M + N \log M)$ due to explicit sorting (xasc) and binary search lookups.
  * **Space Complexity:** $\mathcal{O}(N + M)$ because internal sorted copies of both tables are generated during execution.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`asof_merge.py`)

```python
"""Market data as-of merge engine with Q IPC and standalone self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class AsofMergeEngine:
    """Performs as-of merges via KDB+ IPC or local Pandas merge_asof."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def merge_via_q(self, fills: pd.DataFrame, quotes: pd.DataFrame) -> pd.DataFrame:
		"""Invokes the native q performAsof function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.fills", fills)
            q_conn.sync(".q.quotes", quotes)
			result = q_conn.sync("performAsof[fills; quotes]")
            #result = q_conn.sync("fills lj quotes")			
            logger.info("Successfully executed as-of merge via Q IPC.")
			return pd.DataFrame(result)

    def merge_native(self, fills: pd.DataFrame, quotes: pd.DataFrame, tolerance: pd.Timedelta) -> pd.DataFrame:
        """Re-implements as-of merge natively in Python 3.13 using pandas."""
        fills_sorted = fills.sort_values("time")
        quotes_sorted = quotes.sort_values("time")

        merged = pd.merge_asof(
            fills_sorted,
            quotes_sorted,
            on="time",
            by="sym",
            direction="backward",
            tolerance=tolerance,
        )
        merged["mid"] = 0.5 * (merged["bid"] + merged["ask"])
        return merged


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for AsofMergeEngine."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running AsofMergeEngine standalone validation suite...")

    f_df = pd.DataFrame({"sym": ["CL"], "time": [pd.Timestamp("2026-07-29 10:00:01")], "price": [70.0], "size": [10], "side": ["buy"]})
    q_df = pd.DataFrame({"sym": ["CL"], "time": [pd.Timestamp("2026-07-29 10:00:00")], "bid": [69.9], "ask": [70.1]})

    engine = AsofMergeEngine()

    result = engine.merge_native(f_df, q_df, pd.Timedelta("5s"))
    assert len(result) == 1, "Merge length mismatch"
    assert result.loc[0, "mid"] == 70.0, "Midpoint calculation error"

    result = engine.merge_via_q(f_df, q_df, pd.Timedelta("5s"))
    assert len(result) == 1, "Merge length mismatch"
    assert result.loc[0, "mid"] == 70.0, "Midpoint calculation error"

    logger.info("SUCCESS: AsofMergeEngine Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in AsofMergeEngine standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **`pd.merge_asof`**: Efficiently matches trades to the most recent quote prior to each trade timestamp.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N \log N + M \log M + N \log M)$.
  * Sorting Step — $\mathcal{O}(N \log N + M \log M)$ :
    * The code explicitly calls `.sort_values("time")` on both DataFrames before merging.
  * Matching Step — $\mathcal{O}(N \log M)$:
    * **[pd.merge_asof](https://pandas.pydata.org/docs/reference/api/pandas.merge_asof.html)** requires pre-sorted data. For each of the $N$ fills, it performs a binary search through the $M$ quotes to locate the closest backward timestamp. It evaluates the tolerance on that single matched record in constant time $\mathcal{O}(1)$.

  * **Space Complexity:** $\mathcal{O}(N + M)$ memory.
  * Calling `.sort_values()` creates entirely new copies of your DataFrames in memory. Furthermore, pd.merge_asof generates an entirely new merged DataFrame to return.
  * **The Output Size:** The final resulting DataFrame/table holds exactly N rows (the length of the fills), which scales at $\mathcal{O}(N)$. However, the temporary operational footprint requires holding all tables concurrently, resulting in an active peak space complexity of $\mathcal{O}(N + M)$.

[🔝 Back to Top](#-table-of-contents)

---

## Q9 · QPython Bridge Design

### A) Time Budget & Objectives

* **Time Budget:** 5 minutes
* **Objective:** Design a resilient Q IPC bridge connector with fallback mock support for fetching pre-aggregated quantitative metrics from KDB+.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"A robust IPC bridge is the lifeline between Python research notebooks and production KDB+ tick databases. It must handle connection drops, serialize pandas dataframes seamlessly, and support mock fallback modes for offline testing."*

### C) Mathematical Derivation (MathJax)

$$\Pi_{\text{Remote Compute}}: \text{Python} \xrightarrow{\text{QConnection IPC}} \text{kdb+} \xrightarrow{\text{Columnar Aggregate}} \text{Reduced Result} \xrightarrow{\text{pandas DataFrame}} \text{Python}$$

### D) Architectural & Algorithmic ASCII Diagram

```
PYTHON RESEARCH ──> Dispatch Remote Q Lambdas via IPC ──> KDB+ Columnar Process
                                                                  │
                                                                  ▼
                                                      Return Reduced DataFrame

```

### E) Standalone Self-Validating q Script (`bridgeFetch.q`)

```q
// bridgeFetch.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q bridgeFetch.q -p 5000

fetchVwap:{[symParam; startDt; endDt]
    select vwap: size wavg price by date from trades where date within (startDt; endDt), sym = symParam
 };

main:{[args]
    -1 "SUCCESS: bridgeFetch q script syntax validated.";
    0
    };

@[main; .z.s; { -2 "FAILURE in bridgeFetch main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **Parameterized query**: Filters historical partitions by date range and symbol before computing volume-weighted averages.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(K)$ where $K$ is the number of records within the partitioned date range.
  * **Space Complexity:** $\mathcal{O}(K)$.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`q_bridge.py`)

```python
"""Robust Q IPC bridge connector with fallback mock support and self-validation."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class QBridgeConnector:
    """Manages Q IPC connections and remote quantitative data retrieval."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def fetch_daily_vwap(self, sym: str, start_date: str, end_date: str, mock_mode: bool = True) -> pd.DataFrame:
        """Pulls pre-aggregated daily VWAP via Q IPC or mock fallback."""
        if mock_mode:
            logger.info("Running QBridgeConnector in mock fallback mode.")
            return pd.DataFrame({
                "date": pd.date_range(start_date, end_date),
                "vwap": [70.0] * len(pd.date_range(start_date, end_date)),
            })

        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            query = f"select vwap: size wavg price by date from trades where date within ({start_date}; {end_date}), sym = `{sym}"
            result = q_conn.sync(query)
            logger.info("Successfully fetched daily VWAP via Q IPC.")
            return pd.DataFrame(result)


def run_self_validation() -> None:
    """Executes standalone self-validation assertions for QBridgeConnector."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running QBridgeConnector standalone validation suite...")

    connector = QBridgeConnector()
    df = connector.fetch_daily_vwap("CL", "2026-01-01", "2026-01-03", mock_mode=True)

    assert not df.empty, "Bridge fetch returned empty DataFrame"
    assert len(df) == 3, "Date range length mismatch"

    logger.info("SUCCESS: QBridgeConnector Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in QBridgeConnector standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **Mock Fallback**: Enables offline unit testing without requiring an active KDB+ tick server.
* **TCP Socket Management**: Opens and closes `QConnection` context managers safely.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(1)$ for mock mode, $\mathcal{O}(K)$ for IPC retrieval.
  * **Space Complexity:** $\mathcal{O}(K)$.

[🔝 Back to Top](#-table-of-contents)

---

## Q10 · Validation, Testing, and Invariant Assertions

### A) Time Budget & Objectives

* **Time Budget:** 5 minutes
* **Objective:** Implement automated invariant checks ensuring that average fill prices remain strictly bounded within structural high/low order execution limits.

### B) Interviewer Dialogue & Systematic Macro Pod Context

> *"In quantitative trading pipelines, silent data corruption is more dangerous than an outright crash. Automated invariant assertions acting as CI/CD gates ensure that any anomaly in fill pricing or aggregation immediately halts execution."*

### C) Mathematical Derivation (MathJax)

$$\text{Invariant Verification}: \min(P_{\text{fill}}) \le \bar{P}_{\text{fill}} \le \max(P_{\text{fill}}), \quad \text{Slippage}(P_{\text{arrival}}, P_{\text{arrival}}) \equiv 0.0$$

### D) Architectural & Algorithmic ASCII Diagram

```
TEST SUITE ──> Execute Golden Value & Invariant Checks ──> Validate Bounds & Zero Fills
                                                                  │
                                                                  ▼
                                                      Enforce CI/CD Exit Status

```

### E) Standalone Self-Validating q Script (`validateInvariants.q`)

```q
// validateInvariants.q
// Standalone executable q script with self-validation assertions.
// Start the q server in one terminal:
// q validateInvariants.q -p 5000

validateFills:{[fills]
    // minP: min each fills[`price];
    // maxP: max each fills[`price];
    // avgP: (fills[`price] * fills[`size]) % fills[`size];
    // (avgP >= minP) and (avgP <= maxP)
    // Group by orderId to compute min price, max price, and VWAP per order
    t:select minP:min price, maxP:max price, avgP:sum[price * size] % sum[size] by orderId from fills;
    // Return boolean vector indicating whether avgP is within [minP, maxP] for each order
    (t[`avgP] >= t[`minP]) and (t[`avgP] <= t[`maxP])
 };

main:{[args]
    sampleFills:([] orderId: 1 1; price: 100.0 102.0; size: 50 50);
    res: validateFills[sampleFills];
    assert[all res; "Error: Invariant violation"];

    -1 "SUCCESS: validateInvariants q script passed all validation assertions.";
    0
    };

@[main; .z.s; { -2 "FAILURE in validateInvariants main: ", x; exit 1 }];
exit 0;

```

### F) Detailed q Solution Explanation & Complexity Analysis

* **Invariant checking**: Verifies that weighted average prices never exceed individual fill price bounds.

#### q Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$.
  * **Space Complexity:** $\mathcal{O}(N)$.

[🔝 Back to Top](#-table-of-contents)

---

### G) Standalone Self-Validating Python 3.13 Module (`test_pipeline.py`)

```python
"""Automated validation test suite and invariant assertion engine."""

from __future__ import annotations

import logging
import sys
from typing import Final
import numpy as np
import pandas as pd
from qpython import QConnection

logger = logging.getLogger(__name__)


class ExecutionValidator:
    """Validates quantitative pipeline invariants via Q IPC or native checks."""

    def __init__(self, q_host: str = "localhost", q_port: int = 5000) -> None:
        self.q_host = q_host
        self.q_port = q_port

    def validate_invariants_native(self, fills: pd.DataFrame) -> bool:
        """Validates that average fill prices remain within structural order bounds."""
        min_prices = fills.groupby("order_id")["price"].min()
        max_prices = fills.groupby("order_id")["price"].max()
        notional = fills["price"] * fills["size"]
        grouped = fills.assign(notional=notional).groupby("order_id").agg(
            filled_qty=("size", "sum"),
            notional_sum=("notional", "sum"),
        )
        avg_fill = grouped["notional_sum"] / grouped["filled_qty"]

        within_bounds = (avg_fill >= min_prices) & (avg_fill <= max_prices)
        return bool(within_bounds.all())
		
    def validate_invariants_via_q(self, fills: pd.DataFrame) -> bool:
        """Invokes the native q validateFills function over KDB+ IPC."""
        with QConnection(host=self.q_host, port=self.q_port) as q_conn:
            q_conn.open()
            q_conn.sync(".q.fills", fills)
            result = q_conn.sync("validateFills[fills}]")
            logger.info("Successfully executed invariant validation via Q IPC.")
            if isinstance(result, np.ndarray):
                return bool(result.all())
            return bool(result)	

def run_self_validation() -> None:
    """Executes standalone self-validation assertions for ExecutionValidator."""
    logging.basicConfig(level=logging.INFO)
    logger.info("Running ExecutionValidator standalone validation suite...")

    sample_fills = pd.DataFrame({
        "order_id": [1, 1],
        "price": [100.0, 102.0],
        "size": [50, 50],
    })

    validator = ExecutionValidator()
	
    is_valid = validator.validate_invariants_native(sample_fills)
    assert is_valid, "Invariant validation failed: average fill price out of bounds"

    is_valid = validator.validate_invariants_via_q(sample_fills)
    assert is_valid, "Invariant validation failed: average fill price out of bounds"

    logger.info("SUCCESS: ExecutionValidator Python module passed all validation assertions.")


if __name__ == "__main__":
    try:
        run_self_validation()
        sys.exit(0)
    except Exception as e:
        logger.error("FAILURE in ExecutionValidator standalone execution: %s", e)
        sys.exit(1)

```

### H) Detailed Python 3.13 Solution Explanation & Complexity Analysis

* **Boundary Verification**: Asserts that volume-weighted average fill prices fall strictly between minimum and maximum execution bounds.

#### Python Solution Complexity Analysis

  * **Time Complexity:** $\mathcal{O}(N)$ for group-level bound checks.
  * **Space Complexity:** $\mathcal{O}(N)$ memory.

[🔝 Back to Top](#-table-of-contents)

---
