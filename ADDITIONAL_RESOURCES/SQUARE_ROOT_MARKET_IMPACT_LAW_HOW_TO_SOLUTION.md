# Square-Root Market Impact Law - How to Solution in practice.

## How Top Quant Firms Structure This Engine

### 1. Total Cost vs. Per-Share Price Impact

When trade execution algorithms (e.g., VWAP/TWAP or Almgren-Chriss implementation shortfall algorithms) execute a block order of size $Q$, the price moves concavely according to the **Square-Root Law**:


$$\text{Price Impact } (\Delta P) \propto \sigma \cdot \sqrt{\frac{Q}{V}} = \sigma \cdot Q^{0.5} \cdot V^{-0.5}$$

However, the **Portfolio Manager / Optimization Engine** cares about the total dollar loss (slippage penalty) passed to the P&L:


$$\text{Total Market Impact Cost } (C) = Q \times \Delta P \propto \sigma \cdot Q^{1.5} \cdot V^{-0.5}$$

Top quant platforms decouple price impact (handled by execution desks) from total slippage cost penalty (handled by portfolio optimization engines). The optimizer applies the $1.5$ exponent ($Q^{1.5}$) directly to candidate trades $Q = \vert{}w_i - w_{\text{init}, i}\vert{}$.

### 2. Power Cones for Computational Efficiency

To solve this optimization at massive scale across thousands of assets within tight operational latency budgets, top firms avoid general non-linear solvers.

Instead, they recast the $Q^{1.5}$ power law into a **Power Cone Constraint**:


$$(t_i, 1, \vert{}w_i - w_{\text{init}, i}\vert{}) \in \mathcal{P}_{2/3, 1/3}$$


where $t_i \ge \vert{}w_i - w_{\text{init}, i}\vert{}^{1.5}$.

Interior-point solvers (like MOSEK) can solve power-cone formulations deterministically in polynomial time.

---

## Where Real-World Quant Trading Engine Implementations Differ

While the document’s implementation is structurally sound, top quant platforms enhance it in a few key ways:

| Feature | Document's Implementation | Institutional Production Baseline |
| --- | --- | --- |
| **Impact Exponent ($\alpha$)** | Hardcoded default to $\alpha = 1.5$<br> | **Empirically Calibrated**: $\alpha$ is dynamically estimated per asset class/venue (typically $1.3 \le \alpha \le 1.7$). |
| **Intraday Volatility/Volume** | Static daily ADV & Volatility inputs | **Intraday Profiles**: Real-time binning (e.g., 5-minute bucketed $V_t$ and $\sigma_t$) to account for U-shaped volume/volatility curves at market open/close. |
| **Cross-Asset Impact** | Assets treated independently in cost term | **Cross-Impact Matrices**: Off-diagonal impact terms for highly correlated assets (e.g., buying SPY moves IVV without direct trading in IVV). |
| **Solver Stack** | Python `CVXPY` + `ECOS`<br> | **Native C++ APIs**: Direct C++ calls to `MOSEK` or `OSQP` via power cones to avoid Python interpreter overhead during fast re-optimizations. |

---

**Yes, conceptually and mathematically, it is directly derived from the Square-Root Market Impact Law**, though the document models **total transaction cost** rather than per-unit price impact.

Here is how the two connect mathematically and why the document uses this specific power-law formulation:

---

## 1. Price Impact vs. Total Transaction Cost

* **Square-Root Law (Price Impact):** The empirical Square-Root Law states that the *per-share price impact* ($\Delta P$) of executing a trade of size $Q$ scales with the square root of the order size relative to average volume ($V$):

$$\Delta P \propto \sigma \cdot \sqrt{\frac{Q}{V}} = \sigma \cdot \left(\frac{Q}{V}\right)^{0.5}$$


* **Total Impact Cost (Slippage):** To penalize portfolio optimization, you need the **total dollar cost** ($C$) of executing trade size $Q$, which is the trade size multiplied by the per-share price impact:

$$C(Q) = Q \times \Delta P \propto Q \cdot \left( \sigma \cdot \frac{Q^{0.5}}{V^{0.5}} \right) \propto \left(\frac{\sigma}{V^{0.5}}\right) \cdot Q^{1.5}$$



---

## 2. Why Module I Sets $\alpha = 1.5$

If you look at **Module I** in the document:

* The default code parameter is set to `power_exponent: float = 1.5`.


* The penalty term in the code is written as `cp.power(turnover[i], power_exponent)` multiplied by the liquidity factor.



Because $1.5 = 1 + 0.5$, an exponent of $\alpha = 1.5$ on trade turnover ($\vert{}w_i - w_{\text{init}, i}\vert{}$) in the **total cost objective** directly corresponds to an underlying **$\mathbf{0.5}$ square-root price impact** on per-share execution.

---

## 3. Convexity Requirements in Portfolio Optimization

The document specifically notes that the power exponent $\alpha$ *must be $\ge 1.0$ to guarantee strict mathematical convexity*.

* Per-share price impact $Q^{0.5}$ is a **concave** function, which would make standard optimization non-convex and NP-hard to solve globally.
* Total execution cost $Q^{1.5}$ is a **convex** function ($\alpha = 1.5 \ge 1.0$).

By penalizing total cost ($Q^{1.5}$) rather than per-share impact ($Q^{0.5}$), the objective function remains strictly convex, allowing solvers like `ECOS` or `MOSEK` via `CVXPY` to guarantee a globally optimal portfolio solution in polynomial time.

---

## How it has been implemented in Quant Engine Blueprint

The standard institutional practice across top quant firms (e.g., Millennium, Citadel, Point72, Two Sigma, DE Shaw) as **[outlined above](#how-top-quant-firms-structure-this-engine) has been followed as shown below.

Here is how the two (**Power-Law** and **Square-Root Market Impact Law**) connect mathematically and why the document uses this specific power-law formulation:

---

### 1. Price Impact vs. Total Transaction Cost

* **Square-Root Law (Price Impact):** The empirical Square-Root Law states that the *per-share price impact* ($\Delta P$) of executing a trade of size $Q$ scales with the square root of the order size relative to average volume ($V$):

$$\Delta P \propto \sigma \cdot \sqrt{\frac{Q}{V}} = \sigma \cdot \left(\frac{Q}{V}\right)^{0.5}$$


* **Total Impact Cost (Slippage):** To penalize portfolio optimization, you need the **total dollar cost** ($C$) of executing trade size $Q$, which is the trade size multiplied by the per-share price impact:

$$C(Q) = Q \times \Delta P \propto Q \cdot \left( \sigma \cdot \frac{Q^{0.5}}{V^{0.5}} \right) \propto \left(\frac{\sigma}{V^{0.5}}\right) \cdot Q^{1.5}$$



---

### 2. Why Module I Sets $\alpha = 1.5$

If you look at **Module I** in the document:

* The default code parameter is set to `power_exponent: float = 1.5`.


* The penalty term in the code is written as `cp.power(turnover[i], power_exponent)` multiplied by the liquidity factor.



Because $1.5 = 1 + 0.5$, an exponent of $\alpha = 1.5$ on trade turnover ($\vert{}w_i - w_{\text{init}, i}\vert{}$) in the **total cost objective** directly corresponds to an underlying **$\mathbf{0.5}$ square-root price impact** on per-share execution.

---

### 3. Convexity Requirements in Portfolio Optimization

The document specifically notes that the power exponent $\alpha$ *must be $\ge 1.0$ to guarantee strict mathematical convexity*.

* Per-share price impact $Q^{0.5}$ is a **concave** function, which would make standard optimization non-convex and NP-hard to solve globally.
* Total execution cost $Q^{1.5}$ is a **convex** function ($\alpha = 1.5 \ge 1.0$).

By penalizing total cost ($Q^{1.5}$) rather than per-share impact ($Q^{0.5}$), the objective function remains strictly convex, allowing solvers like `ECOS` or `MOSEK` via `CVXPY` to guarantee a globally optimal portfolio solution in polynomial time.

---