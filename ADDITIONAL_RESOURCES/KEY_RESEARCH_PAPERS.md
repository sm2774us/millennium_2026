<div align="center">

# 📚 Quant Finance & Macro — Key Research Papers Summarized

> **How to use:** One section per source. Read the 🔑 Key Idea first, then skim bullet points. MathJax renders on GitHub with a browser extension (e.g. MathJax Plugin for GitHub).

</div>

---
---

[↩️ Back to README.md](./README.md)

---
---

## Table of Contents

1. [Bachelier (1900) — Theory of Speculation](#1-bachelier-1900--theory-of-speculation)
2. [Markowitz (1952) — Mean-Variance Optimisation](#2-markowitz-1952--mean-variance-optimisation)
3. [Sharpe (1964) — CAPM](#3-sharpe-1964--capm)
4. [Fama & French (1993) — Three-Factor Model](#4-fama--french-1993--three-factor-model)
5. [Black & Scholes (1973) — Option Pricing](#5-black--scholes-1973--option-pricing)
6. [Dupire (1994) — Local Volatility](#6-dupire-1994--local-volatility)
7. [Carr & Madan (1999) — Option Valuation via FFT](#7-carr--madan-1999--option-valuation-via-fft)
8. [Kelly (1956) — Kelly Criterion](#8-kelly-1956--kelly-criterion)
9. [López de Prado — Advances in Financial ML](#9-lópez-de-prado--advances-in-financial-ml)
10. [Marchenko–Pastur Law (RMT)](#10-marchenkopasstur-law-rmt)
11. [Bailey & López de Prado — Deflated Sharpe Ratio](#11-bailey--lópez-de-prado--deflated-sharpe-ratio-2014)
12. [Almgren & Chriss — Optimal Execution](#12-almgren--chriss--optimal-execution-2001)
13. [Gârleanu & Pedersen — Dynamic Trading with Predictable Returns](#13-gârleanu--pedersen--dynamic-trading-with-predictable-returns-2013)
14. [Ledoit & Wolf — Covariance Shrinkage](#14-ledoit--wolf-2004--covariance-shrinkage)
15. [Andersen, Bollerslev et al. — Realised Variance](#15-andersen-bollerslev-et-al--answering-the-skeptics-1998)
16. [Leland — Option Pricing with Transaction Costs](#16-leland-1985--option-pricing-with-transaction-costs)
17. [Basel III / FRTB — Regulatory VaR](#17-basel-iii--frtb--regulatory-var)
18. [JP Morgan RiskMetrics — EWMA](#18-jp-morgan-riskmetrics-1996--ewma)
19. [Graham Capital — Imbalances, Consolidation & Choke Points](#19-graham-capital--imbalances-consolidation--choke-points-2026)

[🔝 Back to Top](#table-of-contents)

---
---

## 1. Bachelier (1900) — Theory of Speculation

**🔑 Key Idea:** Prices follow a random walk (arithmetic Brownian motion). The first mathematical model of financial markets — predates Einstein's Brownian motion by 5 years.

### The Model

$$S_t = S_0 + \sigma W_t, \qquad W_t \sim \mathcal{N}(0, t)$$

- Arithmetic BM → prices can go **negative** (flaw, later fixed by GBM)
- Option price = expected payoff under the physical measure (no discounting, no risk-neutral argument yet)

**Bachelier option price (call):**

$$C = (F - K)\,\Phi\!\left(\frac{F-K}{\sigma\sqrt{T}}\right) + \sigma\sqrt{T}\,\phi\!\left(\frac{F-K}{\sigma\sqrt{T}}\right)$$

where $F$ = forward price, $\Phi$ = standard normal CDF, $\phi$ = PDF.

### Legacy

```
Bachelier (1900) ──→ Wiener (1923) formal BM ──→ Itô (1944) stochastic calculus
      └─────────────────────────────────────────→ Black–Scholes (1973)
```

- **Bachelier vol** (basis-point vol, $\sigma_B$) used today in **interest rate options** (swaptions, caps) where rates near zero make log-normal assumptions break down
- Conversion: $\sigma_B \approx \sigma_{LN} \cdot F$ (at-the-money approximation)

[🔝 Back to Top](#table-of-contents)

---
---

## 2. Markowitz (1952) — Mean-Variance Optimisation

**🔑 Key Idea:** Investors care only about mean and variance of portfolio returns. Diversification is the only free lunch — the efficient frontier is the set of portfolios with maximum return per unit of risk.

### Setup

Portfolio of $N$ assets with weights $\mathbf{w}$, $\mathbf{1}^T\mathbf{w}=1$:

$$\mu_p = \mathbf{w}^T \boldsymbol{\mu}, \qquad \sigma_p^2 = \mathbf{w}^T \Sigma \mathbf{w}$$

**Efficient frontier** (parametric, risk-aversion $\gamma$):

$$\max_{\mathbf{w}} \mathbf{w}^T\boldsymbol{\mu} - \frac{\gamma}{2}\,\mathbf{w}^T\Sigma\,\mathbf{w}$$

$$\Rightarrow \mathbf{w}^* = \frac{1}{\gamma}\Sigma^{-1}\boldsymbol{\mu} \quad \text{(unconstrained)}$$

### The Efficient Frontier

```
E[r]
  │                        * ← Maximum Sharpe (tangency)
  │                     *
  │                  *    ← Efficient Frontier
  │               *
  │      *──────*         ← Minimum Variance Portfolio
  │   (dominated)
  └─────────────────────── σ
```

### Key Insights & Failure Modes

| Property | Detail |
|----------|--------|
| Inputs | $\boldsymbol{\mu}$, $\Sigma$ — both estimated with error |
| Sensitivity | Weights are **hyper-sensitive** to $\boldsymbol{\mu}$ estimates ("error maximiser") |
| Fix | Black-Litterman, shrinkage (Ledoit–Wolf), robust optimisation |
| Two-fund theorem | Any efficient portfolio = combo of risk-free + tangency portfolio |
| Tangency portfolio | $\mathbf{w}^* \propto \Sigma^{-1}(\boldsymbol{\mu} - r_f \mathbf{1})$ |

[🔝 Back to Top](#table-of-contents)

---
---

## 3. Sharpe (1964) — CAPM

**🔑 Key Idea:** In equilibrium, the only priced risk is **market beta** — all idiosyncratic risk is diversifiable and earns zero expected return.

### The Model

$$E[R_i] - r_f = \beta_i \bigl(E[R_m] - r_f\bigr)$$

$$\beta_i = \frac{\text{Cov}(R_i, R_m)}{\text{Var}(R_m)}$$

**Security Market Line (SML):**

```
E[R]
  │                         * High-beta assets
  │                    * ↗
  │          SML  ↗  *
  │         ↗  *
  │   r_f ─*─────────────────── β
  │         0         1
                      ↑ Market portfolio
```

### Assumptions & Violations

- Single period, mean-variance investors, homogeneous expectations, no taxes/frictions
- **CAPM alpha** ($\alpha_i$): excess return unexplained by beta → the hunt for alpha is the entire active management industry
- **Jensen's alpha:** $\alpha = E[R_i] - [r_f + \beta_i(E[R_m]-r_f)]$
- **Sharpe Ratio:** $SR = (E[R_p] - r_f)/\sigma_p$ — measures reward per unit of *total* risk (not just beta)

### Empirical Failures → Factor Zoo

- Low-beta stocks outperform on risk-adjusted basis (**betting against beta**)
- Size and value effects not explained by $\beta$ alone → led directly to Fama–French

[🔝 Back to Top](#table-of-contents)

---
---

## 4. Fama & French (1993) — Three-Factor Model

**🔑 Key Idea:** Two additional risk factors — size (SMB) and value (HML) — explain returns that CAPM cannot. Excess returns are compensation for systematic risk exposure.

### The Model

$$E[R_i] - r_f = \alpha_i + \beta_i^{MKT}(R_m - r_f) + \beta_i^{SMB}\cdot SMB + \beta_i^{HML}\cdot HML$$

| Factor | Long | Short | Economic rationale |
|--------|------|-------|-------------------|
| **MKT** | Market | Risk-free | Systematic risk |
| **SMB** (Small Minus Big) | Small-cap | Large-cap | Distress risk / illiquidity |
| **HML** (High Minus Low) | High B/M (value) | Low B/M (growth) | Financial distress risk |

### Factor Construction

```
Stocks sorted by Size × Book-to-Market:

        Small   Big
Value    SH      BH   }
Neutral  SN      BN   }  SMB = (SH+SN+SL)/3 - (BH+BN+BL)/3
Growth   SL      BL   }  HML = (SH+BH)/2   - (SL+BL)/2
```

### Extensions

- **Carhart (1997):** + Momentum (MOM / UMD) → 4-factor
- **FF5 (2015):** + Profitability (RMW) + Investment (CMA) → 5-factor
- **Q-factor (Hou, Xue, Zhang):** Investment + ROE
- Modern "factor zoo": 300+ published factors; most don't survive multiple testing (→ Deflated SR)

[🔝 Back to Top](#table-of-contents)

---
---

## 5. Black & Scholes (1973) — Option Pricing

**🔑 Key Idea:** By continuously delta-hedging, option risk can be perfectly eliminated — the option price is the cost of the replicating portfolio. No preference parameters appear.

### The PDE

Assume $dS = \mu S\,dt + \sigma S\,dW$ (GBM). By Itô's lemma + no-arbitrage:

$$\frac{\partial V}{\partial t} + \frac{1}{2}\sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} + rS\frac{\partial V}{\partial S} - rV = 0$$

### Closed-Form (European Call)

$$C = S_0\,\Phi(d_1) - K e^{-rT}\Phi(d_2)$$

$$d_{1,2} = \frac{\ln(S_0/K) + (r \pm \tfrac{1}{2}\sigma^2)T}{\sigma\sqrt{T}}$$

### The Greeks

| Greek | Definition | Intuition |
|-------|-----------|-----------|
| $\Delta = \partial C/\partial S$ | $\Phi(d_1)$ | Hedge ratio — shares per option |
| $\Gamma = \partial^2 C/\partial S^2$ | $\phi(d_1)/S\sigma\sqrt{T}$ | Convexity; cost of rehedging |
| $\Theta = \partial C/\partial t$ | Negative (decay) | Time value bleed |
| $\mathcal{V}$ (Vega) $= \partial C/\partial\sigma$ | $S\phi(d_1)\sqrt{T}$ | Vol sensitivity |
| $\rho = \partial C/\partial r$ | $KTe^{-rT}\Phi(d_2)$ | Rate sensitivity |

**P&L of delta-hedged position:**

$$d\Pi \approx \underbrace{\frac{1}{2}\Gamma(\delta S)^2}_{\text{realised var gain}} - \underbrace{\Theta\,dt}_{\text{theta decay}}$$

Profit when $\sigma_{\text{realised}} > \sigma_{\text{implied}}$.

### Assumptions & Violations

```
BS Assumption          Reality                  Model Fix
─────────────────────────────────────────────────────────────
Constant σ             Vol smile/skew           Dupire local vol, SV
Continuous hedging     Transaction costs        Leland (1985)
Log-normal returns     Fat tails / jumps        Jump-diffusion (Merton)
No dividends           Discrete dividends       Forward adjustment
```

[🔝 Back to Top](#table-of-contents)

---
---

## 6. Dupire (1994) — Local Volatility

**🔑 Key Idea:** Given the full surface of observed option prices $C(K,T)$, there exists a **unique** diffusion $\sigma(S,t)$ (local vol) consistent with all prices simultaneously.

### Dupire's Equation

From the market call price surface, the local vol is:

$$\sigma^2(K,T) = \frac{\dfrac{\partial C}{\partial T} + r K \dfrac{\partial C}{\partial K}}{\dfrac{1}{2}K^2\dfrac{\partial^2 C}{\partial K^2}}$$

- Numerator: time decay of option price (Dupire forward equation)
- Denominator: $K^2 \times$ the **risk-neutral density** (Breeden–Litzenberger)

### Intuition

```
Implied vol surface (market):         Local vol surface (model):

    σ_imp                                 σ_loc(S,t)
     ↑  ╲  smile                           ↑  smoother, flatter
     │   ╲___/                             │  ────────────────
     └──────────→ K                        └──────────────→ S,t
```

- BS assumes $\sigma = $ const → flat surface; contradicted by market
- Local vol is a **complete model** — prices all European options exactly
- Weakness: **forward smile dynamics** are unrealistic (smile flattens forward in time, unlike stochastic vol)

### Breeden–Litzenberger (key identity)

$$q(K,T) = e^{rT}\frac{\partial^2 C}{\partial K^2}$$

Risk-neutral density is the second derivative of call prices w.r.t. strike — allows model-free extraction of market-implied distributions.

[🔝 Back to Top](#table-of-contents)

---
---

## 7. Carr & Madan (1999) — Option Valuation via FFT

**🔑 Key Idea:** If the **characteristic function** of log-returns is known analytically, European option prices can be computed in $O(N \log N)$ via FFT — enabling fast calibration of complex models (Heston, VG, CGMY).

### Setup

For a model with log-return characteristic function:

$$\psi_T(u) = E\!\left[e^{iu \ln(S_T/S_0)}\right]$$

The (dampened) call price has Fourier transform:

$$\zeta_T(v) = \int_{-\infty}^{\infty} e^{ivk} c_T(k)\,dk = \frac{e^{-rT}\psi_T(v-(α+1)i)}{α^2 + α - v^2 + i(2α+1)v}$$

where $k = \ln(K/S_0)$, $\alpha > 0$ is a damping factor.

**Invert via FFT:**

$$c_T(k) = \frac{e^{-\alpha k}}{\pi}\int_0^{\infty} e^{-ivk}\zeta_T(v)\,dv \approx \text{FFT}\{\zeta_T\}$$

### Why It Matters

```
Without FFT (naive):         For each (K, T): numerical integration O(N) → slow
With Carr-Madan FFT:         All strikes at once via FFT → O(N log N) → fast calibration
```

| Model | Char. function $\psi_T$ available? |
|-------|-----------------------------------|
| Black–Scholes | ✓ (closed form) |
| Heston (SV) | ✓ (closed form) |
| Variance Gamma | ✓ (closed form) |
| CGMY / Kou jumps | ✓ (closed form) |
| Local vol (Dupire) | ✗ (PDE required) |

- **Heston char. function:** involves complex-valued Riccati ODE — closed form, but branch-cut careful
- Modern implementations: Lewis (2001) contour, Lipton (2002) — avoid $\alpha$ instability

[🔝 Back to Top](#table-of-contents)

---
---

## 8. Kelly (1956) — Kelly Criterion

**🔑 Key Idea:** The fraction of wealth to bet that maximises long-run geometric growth rate is $f^* = \text{edge}/\text{odds}$. Overbetting is worse than underbetting — it leads to ruin.

### Discrete Case (binary bet)

Win probability $p$, lose probability $q = 1-p$, odds $b$ (win $b$ per unit staked):

$$f^* = \frac{p \cdot b - q}{b} = \frac{pb - (1-p)}{b} = p - \frac{q}{b}$$

**Special case ($b=1$, even odds):** $f^* = p - q = 2p - 1$

### Continuous Case (Gaussian returns)

$$f^* = \frac{\mu - r}{\sigma^2} = \frac{\text{Sharpe Ratio} \times \sigma_{\text{excess}}}{\sigma^2} = \frac{SR}{\sigma}$$

This is exactly the **Markowitz tangency weight** (unconstrained, single asset).

### Growth Rate vs. Bet Size

```
G(f) — long-run growth rate:

G(f)  │         * ← f* (Kelly optimal)
      │       *   *
      │     *       *
      │   *           *
      │ *               *
 0 ───*─────────────────────*─── f
      0       f*           2f*
                            ↑ "double Kelly" → G=0, eventual ruin
```

$$G(f) \approx \underbrace{r + f\mu}_{\text{expected return}} - \underbrace{\frac{f^2\sigma^2}{2}}_{\text{variance drag}}$$

Maximum at $f^* = \mu/\sigma^2$, where $G^* = r + \frac{\mu^2}{2\sigma^2} = r + \frac{SR^2}{2}$.

### Practical Modifications

| Issue | Fix |
|-------|-----|
| Estimated $\mu$, $\sigma$ have error | Use **fractional Kelly** (e.g. $f^*/2$ or $f^*/4$) |
| Multiple assets | $\mathbf{f}^* = \Sigma^{-1}\boldsymbol{\mu}$ (same as Markowitz unconstrained) |
| Non-Gaussian returns | Use log-utility maximisation numerically |
| Drawdown constraints | Kelly gives max growth but drawdowns can be severe; reduce fraction |

> **Half-Kelly** halves both the growth rate reduction *and* the variance — asymmetric benefit: most of the return, half the risk.

---

## Quick Reference Cheat Sheet

```
┌──────────────────────────────────────────────────────────────────────────┐
│  TOPIC                   FORMULA / NUMBER             SOURCE             │
├──────────────────────────────────────────────────────────────────────────┤
│  BM price model          S_t = S_0 + σW_t             Bachelier          │
│  Efficient frontier      w* = (1/γ)Σ⁻¹μ              Markowitz          │
│  CAPM                    E[Ri]-rf = βi(Rm-rf)          Sharpe            │
│  Three factors           MKT + SMB + HML               Fama–French       │
│  BS call                 SΦ(d₁) - Ke⁻ʳᵀΦ(d₂)          Black–Scholes     │
│  Local vol               σ²(K,T) = (∂C/∂T +rK∂C/∂K) / (½K²∂²C/∂K²)    │
│                                                         Dupire            │
│  FFT pricing             O(N log N) via char. fn ψ_T   Carr–Madan        │
│  Kelly fraction          f* = μ/σ² = SR/σ              Kelly             │
│  Kelly multi-asset       f* = Σ⁻¹μ (= Markowitz!)      Kelly             │
│  Half-Kelly              f*/2 → ~75% growth, ~50% var  Practical rule    │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Cross-Reference: How the Papers Connect

```
Bachelier (1900) ─── Random walk ───────────────────────────────────────────┐
                                                                             ↓
Markowitz (1952) ── Mean-variance ── Tangency portfolio = Kelly multi-asset  │
      │                                                                      │
      ↓                                                                      │
Sharpe (1964) ─────── CAPM: beta prices only systematic risk                 │
      │                                                                      │
      ↓                                                                      │
Fama-French (1993) ── CAPM fails → factors: SMB, HML, MOM                   │
                                                                             │
Black-Scholes (1973) ← GBM (extends Bachelier to log-normal) ───────────────┘
      │
      ├── Constant σ assumed ──→ Dupire (1994): σ(S,t) from market surface
      │
      └── Slow pricing ─────→ Carr-Madan (1999): FFT via char. function
```

[🔝 Back to Top](#table-of-contents)

---
---

## 9. López de Prado — Advances in Financial ML

**🔑 Key Idea:** Standard ML pipelines are broken for financial time series — leakage, non-stationarity, and small samples demand bespoke methods.

### Core Contributions

**Financial Data Structures**
- Raw OHLCV bars are time-sampled → autocorrelated, heteroskedastic
- Prefer **dollar bars** (sample every $V$ notional traded) → IID-closer, better for ML features

```
Time bars:  |--1min--|--1min--|  ← variable volume, clustered vol
Dollar bars:|---$1M--|--$1M--|  ← uniform information content
```

**Triple-Barrier Labelling**
- Label each observation by whichever barrier is hit first:

```
        Upper barrier  ─────────── +τ_profit
        ──────────────────────────
Entry → ·
        ──────────────────────────
        Lower barrier  ─────────── -τ_stop
                   |
                   t_max (time barrier)
```

- Labels ∈ {+1, 0, -1}; assign sample weights by uniqueness of information

**Purged K-Fold Cross-Validation**
- Standard CV leaks future into past via overlapping labels
- **Purge:** remove train samples whose labels overlap in time with test set
- **Embargo:** additionally drop `h` bars after test fold boundary

```
Train | [PURGE] | TEST | [EMBARGO] | Train ...
```

**Fractional Differentiation**
- Integer differencing destroys memory; raw prices are non-stationary
- Find minimum $d$ s.t. series passes ADF but retains maximum memory:

$$\tilde{x}_t = \sum_{k=0}^{\infty} \binom{d}{k}(-1)^k x_{t-k}, \quad d \in (0,1)$$

**Feature Importance**
- Mean-Decrease Impurity (MDI) biased toward high-cardinality features
- Prefer **Mean-Decrease Accuracy (MDA)** + **Shapley values** (SHAP)

**Bet Sizing / Position Sizing**
- Size ∝ predicted probability distance from 0.5:
$$m = \text{signal strength} = 2F(p) - 1, \quad F = \text{CDF of predicted prob}$$

[🔝 Back to Top](#table-of-contents)

---
---

## 10. Marchenko–Pastur Law (RMT)

**🔑 Key Idea:** Most eigenvalues of a sample covariance matrix from *noise* fall within a known band — eigenvalues outside this band carry genuine signal.

### The Distribution

For an $N \times T$ matrix of i.i.d. entries, the empirical spectral distribution converges to the **Marchenko–Pastur law**:

$$\rho(\lambda) = \frac{T}{2\pi N \sigma^2} \cdot \frac{\sqrt{(\lambda_+ - \lambda)(\lambda - \lambda_-)}  }{\lambda}$$

$$\lambda_{\pm} = \sigma^2\!\left(1 \pm \sqrt{\frac{N}{T}}\right)^2$$

where $q = N/T$ is the ratio of variables to observations.

### Practical Use in Finance

```
Eigenvalue spectrum of sample cov Σ̂:

    ██████                ← Bulk: Marchenko-Pastur noise band [λ-, λ+]
    ██████
    ██████         ·  ·  ← Outliers: genuine factors (market, sectors)
─────────────────────────────────────── λ
    λ-            λ+
```

- **Denoise:** Replace eigenvalues inside $[\lambda_-, \lambda_+]$ with their average; keep outliers
- **Detone:** Remove the market (largest) eigenvector to improve conditioning
- Result: cleaner correlation matrix → better Markowitz portfolios, more stable risk models

### Key Numbers

| Parameter | Typical value |
|-----------|--------------|
| $N$ (assets) | 500 |
| $T$ (days) | 252 |
| $q = N/T$ | ~2 |
| Noise band upper bound $\lambda_+$ | $\sigma^2(1+\sqrt{q})^2$ |

[🔝 Back to Top](#table-of-contents)

---
---

## 11. Bailey & López de Prado — Deflated Sharpe Ratio (2014)

**🔑 Key Idea:** When you test many strategies and report the best Sharpe, you are selecting from a distribution — the Deflated SR corrects for this multiple-testing bias.

### The Problem

$$\hat{SR} = \frac{\hat{\mu} - r_f}{\hat{\sigma}}$$

Under $n$ independent trials, the expected maximum SR grows as:

$$E[\max_n SR] \approx \left(1 - \gamma_E\right) Z^{-1}\!\left(1 - \frac{1}{n}\right) + \gamma_E \cdot Z^{-1}\!\left(1 - \frac{1}{ne}\right)$$

(Euler–Mascheroni constant $\gamma_E \approx 0.5772$)

### The Deflated SR Formula

$$DSR = \Phi\!\left(\frac{(\hat{SR} - SR^*)\sqrt{T-1}}{\sqrt{1 - \hat{\gamma}_3 \hat{SR} + \frac{\hat{\gamma}_4 - 1}{4}\hat{SR}^2}}\right)$$

where:
- $SR^*$ = benchmark (expected max SR under repeated trials)
- $\hat{\gamma}_3$ = skewness of returns
- $\hat{\gamma}_4$ = excess kurtosis of returns
- $T$ = number of observations

### Practical Rules

```
Number of trials tested (n):   1    10   100   1000
Minimum SR to survive DSR:     0.5  1.0  1.4   1.8  (approximate, annualised)
```

- Non-normal returns (fat tails, negative skew) → further inflate the required SR
- Always report the **number of strategies tried**, not just the winner

[🔝 Back to Top](#table-of-contents)

---
---

## 12. Almgren & Chriss — Optimal Execution (2001)

**🔑 Key Idea:** There is an efficient frontier of execution strategies trading off market impact (cost) against timing risk (variance). The optimal trajectory is a deterministic schedule.

### Model Setup

Sell $X$ shares over $[0, T]$ in $N$ slices. At time $t_k$:
- Remaining inventory: $x_k$
- Trade size: $n_k = x_k - x_{k+1}$

**Price impact decomposition:**

$$\underbrace{\text{Permanent impact}}_{\propto \text{ total volume}} + \underbrace{\text{Temporary impact}}_{\propto \text{ trade rate}}$$

$$\tilde{S}_k = S_{k-1} + \sigma \xi_k - \gamma \frac{n_k}{\tau} \quad \text{(temporary, linear in rate)}$$

### Efficient Frontier

Minimise expected cost $E[C]$ subject to variance $V[C] = \text{const}$:

$$\min_{\{n_k\}} E[C] + \lambda \cdot V[C]$$

Optimal trajectory (continuous limit):

$$x^*(t) = X \cdot \frac{\sinh\!\left[\kappa(T-t)\right]}{\sinh(\kappa T)}, \qquad \kappa = \sqrt{\frac{\lambda \sigma^2}{\eta}}$$

where $\eta$ = temporary impact coefficient, $\lambda$ = risk aversion.

```
Execution trajectory x*(t):

X ─┐
   │╲  κ large (risk-averse) → front-load trades
   │  ╲
   │    ──── κ small (patient) → linear TWAP
   │        ╲
0  └─────────── t
   0           T
```

### Key Takeaways

| Regime | Optimal Strategy |
|--------|-----------------|
| High $\lambda$ (risk averse) | Accelerate early (avoid variance) |
| Low $\lambda$ (patient) | Spread evenly — approaches TWAP |
| High $\sigma$ | More front-loading |
| High market impact $\eta$ | Slower execution |

- **VWAP** is suboptimal unless volume profile perfectly matches risk-aversion
- Model underpins all modern TCA (Transaction Cost Analysis)

[🔝 Back to Top](#table-of-contents)

---
---

## 13. Gârleanu & Pedersen — Dynamic Trading with Predictable Returns (2013)

**🔑 Key Idea:** With transaction costs, you never fully trade to the Markowitz target — you trade a fraction toward it each period. The optimal rule is **aim at a lead-ahead portfolio**.

### Setup

- Expected return signal $f_t$ follows AR(1): $f_{t+1} = \Phi f_t + \varepsilon_{t+1}$
- Quadratic transaction costs: $\frac{1}{2}(x_t - x_{t-1})^T \Lambda (x_t - x_{t-1})$
- Maximise discounted CARA utility

### The Optimal Policy

$$x_t = (1 - a_\Lambda) x_{t-1} + a_\Lambda \cdot x^{\text{aim}}_t$$

$$x^{\text{aim}}_t = \sum_{\tau=0}^{\infty}(1-a_\Lambda)^\tau \cdot \bar{x}_{t+\tau \mid t}$$

where $\bar{x}_t = \Sigma^{-1} f_t / \rho$ is the frictionless Markowitz portfolio.

```
Frictionless target ────────────────────────────  x̄_t
                                     ↗ aim ahead here
Actual position ──────────────────·
                              ↑ trade fraction a toward aim
Previous position ────────────
```

**Intuition:** Aim *ahead* of current Markowitz target (lead) to reduce future costs; trade speed governed by $a_\Lambda \propto \Lambda^{-1/2}$.

### Key Implications

- **Never fully rebalance** when costs > 0 — optimal $a_\Lambda < 1$
- Higher signal persistence (high $\Phi$) → more aggressive trading (signals last longer)
- Higher costs ($\Lambda$) → trade slower, aim further ahead
- Generalises to multi-asset; covariance of signals matters for netting costs

[🔝 Back to Top](#table-of-contents)

---
---

## 14. Ledoit & Wolf (2004) — Covariance Shrinkage

**🔑 Key Idea:** Sample covariance $\hat{\Sigma}$ is noisy for large $N$. Shrink it toward a structured target $F$ to get a well-conditioned estimator.

### The Estimator

$$\hat{\Sigma}^* = (1 - \alpha)\,\hat{\Sigma} + \alpha F$$

Common target $F$: **single-index model** (market factor) or **constant correlation** matrix.

**Optimal shrinkage intensity** (Oracle, then estimated):

$$\alpha^* = \frac{\sum_{i \neq j} \text{AsyVar}[\hat{\sigma}_{ij}]}{\|\hat{\Sigma} - F\|^2_F}$$

Ledoit–Wolf provide a consistent estimator of $\alpha^*$ with no free parameters.

### Why It Works

```
Bias-Variance tradeoff:

Error = Bias² + Variance

Sample Σ̂:   Bias=0,  Variance=HIGH  (extreme eigenvalues)
Shrunk  Σ*:  Bias>0,  Variance=LOW   → net improvement in MSE
```

- Extreme eigenvalues of $\hat{\Sigma}$ are too large/small → portfolio weights blow up
- Shrinkage pulls eigenvalues toward a common value → better out-of-sample Sharpe

### Variants

| Method | Target $F$ |
|--------|-----------|
| Ledoit–Wolf (2004) | Single-index / constant corr |
| Oracle Approx. Shrinkage (OAS) | Identity scaled |
| Nonlinear shrinkage (LW 2012) | Per-eigenvalue transformation |
| RMT denoising (López de Prado) | Marchenko–Pastur bulk zeroed |

[🔝 Back to Top](#table-of-contents)

---
---

## 15. Andersen, Bollerslev et al. — "Answering the Skeptics" (1998)

**🔑 Key Idea:** Realised variance from high-frequency (5-min) returns is a consistent, model-free estimator of integrated variance — far superior to GARCH for forecasting.

### Realised Variance

Partition $[0,T]$ into $M$ intervals of length $\Delta = T/M$. Return over interval $k$:

$$r_{t,k} = \ln P_{t+k\Delta} - \ln P_{t+(k-1)\Delta}$$

Realised Variance:
$$RV_t = \sum_{k=1}^{M} r_{t,k}^2 \xrightarrow{M\to\infty} \int_0^T \sigma^2_s ds \quad \text{(Integrated Variance)}$$

### The 5-Minute Rule

```
Sampling frequency vs. RV bias-variance:

          Bias (microstructure noise)
    ↑      ────────────────────
    │    ╱
    │   ╱ MSE
    │  ╱────────── Variance (estimation)
    │ ╱
    └────────────────────────────→ Δ (sampling interval)
     1s  5s  30s  5min 30min
                   ↑
              Sweet spot: ~5 min
```

- Tick data → bid-ask bounce inflates $RV$ (noise-in, variance-out)
- 5-minute intervals balance noise vs. estimation variance
- Daily $RV = \sum_{k=1}^{78} r_{5\text{min},k}^2$ (US equity, 6.5hr session)

### Key Results

- $\log(RV_t)$ is **approximately Gaussian** → enables easy forecasting (HAR model)
- HAR-RV: $RV_{t+1} = \beta_0 + \beta_d RV_t + \beta_w \overline{RV}_{t-5:t} + \beta_m \overline{RV}_{t-22:t} + \varepsilon$
- GARCH forecasts correlate poorly with $RV$ → model misspecification confirmed

[🔝 Back to Top](#table-of-contents)

---
---

## 16. Leland (1985) — Option Pricing with Transaction Costs

**🔑 Key Idea:** With proportional transaction costs $k$, you cannot hedge continuously — instead use a bandwidth rule and a **modified volatility** for pricing.

### The Model

Standard Black–Scholes delta hedge rebalanced at fixed intervals $\Delta t$.

**Modified volatility** for pricing:

$$\hat{\sigma}^2 = \sigma^2\!\left(1 + \underbrace{\frac{k}{\sigma\sqrt{\Delta t}} \cdot \text{sgn}(\Gamma)}_{\text{cost adjustment}}\right)$$

- Long $\Gamma$ (long option): $\hat{\sigma} > \sigma$ → option is **more expensive**
- Short $\Gamma$ (short option): $\hat{\sigma} < \sigma$ → option is **cheaper** to replicate

### Hedge Bandwidth

```
Delta:
         ─────────────────────────── Δ + δ  ← rebalance trigger
              ╱
             ╱  no-trade zone (bandwidth 2δ)
            ╱
         ─────────────────────────── Δ - δ
```

$$\delta \propto \left(\frac{3k \cdot \Gamma \cdot \sigma^2 \cdot S^2 \cdot \Delta t}{2}\right)^{1/3}$$

Wider bandwidth → fewer trades → lower cost but more replication error.

### Key Takeaways

- Transaction costs break the BS replication argument exactly
- At-the-money, short-term options most affected (high $\Gamma$)
- Connects to **volatility spread**: ask vol > mid > bid vol in practice
- Modern extensions: Hodges & Neuberger utility-based hedging, Whalley & Wilmott asymptotic band

[🔝 Back to Top](#table-of-contents)

---
---

## 17. Basel III / FRTB — Regulatory VaR

**🔑 Key Idea:** FRTB replaces VaR with Expected Shortfall (ES) at 97.5%, computed under stressed conditions, with an Internal Models vs. Standardised Approach choice.

### Key Numbers to Memorise

| Metric | Confidence | Horizon | Context |
|--------|-----------|---------|---------|
| VaR (Basel II) | 99% | 10-day | Legacy MRC |
| ES (FRTB / Basel III) | 97.5% | 10-day | New MRC |
| SVaR | 99% | 10-day | Stressed period |
| Backtesting | 99% | 1-day | 250-day window |

> Note: ES at 97.5% ≈ VaR at 99% for normal distributions but captures tail better.

$$\text{ES}_\alpha = \frac{1}{1-\alpha}\int_\alpha^1 \text{VaR}_u du = E[L \mid L > \text{VaR}_\alpha]$$

### FRTB Architecture

```
FRTB Capital Charge
├── Internal Models Approach (IMA)
│   ├── ES (97.5%, 10-day, stressed)
│   ├── Non-modellable risk factors (NMRF) → stress scenario
│   └── P&L attribution test (must pass to use IMA)
└── Standardised Approach (SA)
    ├── Sensitivity-based method (delta, vega, curvature)
    └── Residual risk add-on
```

### Trading Book vs. Banking Book

- **Trading Book:** marked-to-market, held for short-term resale → IMA/SA capital
- **Banking Book:** accrual, hold-to-maturity → credit risk framework
- FRTB tightens the boundary (harder to switch)

[🔝 Back to Top](#table-of-contents)

---
---

## 18. JP Morgan RiskMetrics (1996) — EWMA

**🔑 Key Idea:** Volatility and covariance are time-varying. EWMA with $\lambda = 0.94$ (daily) is a simple, practical model that decays old observations exponentially.

### The Model

**EWMA variance:**

$$\sigma_t^2 = \lambda \sigma_{t-1}^2 + (1-\lambda) r_{t-1}^2$$

**EWMA covariance:**

$$\sigma_{xy,t} = \lambda \sigma_{xy,t-1} + (1-\lambda) r_{x,t-1} r_{y,t-1}$$

Equivalent to infinite MA with exponentially decaying weights:

$$\sigma_t^2 = (1-\lambda)\sum_{k=0}^{\infty} \lambda^k r_{t-1-k}^2$$

### Parameter Choices

| Series | $\lambda$ |
|--------|----------|
| Daily returns | **0.94** (RiskMetrics standard) |
| Monthly returns | **0.97** |
| Implied by half-life | $h_{1/2} = \ln 2 / \ln(1/\lambda)$ |

At $\lambda = 0.94$: half-life $\approx 11$ trading days.

```
Weight on day k ago:  w_k = (1-λ)λ^k

1.0 ─┐
     │
0.5  │ · · · · · · · ·
     │           ↑ half-life ≈ 11 days
0.0  └──────────────────────────────→ k (days ago)
     0    5    10    15    20    25
```

### Limitations & Successors

- No mean reversion → variance can diverge
- No separate long-run mean (unlike GARCH)
- GARCH(1,1): $\sigma_t^2 = \omega + \alpha r_{t-1}^2 + \beta \sigma_{t-1}^2$; EWMA is nested at $\omega=0, \alpha+\beta=1$
- Modern practice: DCC-GARCH, realized-GARCH, or ML vol models

[🔝 Back to Top](#table-of-contents)

---
---

## 19. Graham Capital — Imbalances, Consolidation & Choke Points (2026)

**🔑 Key Idea:** The modern macro regime is the cumulative product of demographics, policy-put conditioning, globalization, and now geopolitical choke points — single directional trades are inferior to **sequencing frameworks**.

### The Chain of Causality

```
Demographics → Policy Put → Globalization → Financialization
     ↓               ↓            ↓                ↓
Lower real rates  Leverage   Low term premia   Duration dependence
     │
     └──────────────────────────────────────────────────────────────→
                Post-COVID Rupture → Choke Points → Consolidation
                (inflation shock)   (availability  (US-China rivalry)
                                     premium)
```

| Stage | Mechanism | Market Expression |
|-------|-----------|------------------|
| Demographics | Aging → demand for safe income | Lower $r^*$, fiscal resistance to austerity |
| Policy Put | Crises train markets to expect curve repair | Leverage, maturity transformation |
| Globalization | China WTO + surplus recycling | Low term premia, goods deflation |
| Post-COVID | Fiscal impulse meets real constraints | Inflation, positive stock–bond correlation |
| Choke Points | Concentrated inputs = strategic leverage | Availability premia, vol spikes |
| Consolidation | US rebuilds industrial base vs. China | Steeper curves, fiscal credibility risk |

### The Inverse-Floater Economy

> The financial system owns long-duration, illiquid, leveraged claims financed through short-rate-sensitive liabilities (repo, deposits).

$$\text{Net Interest Margin} \propto \underbrace{r_{\text{long}} - r_{\text{short}}}_{\text{curve slope}}$$

- **Positively sloped curve + low real rates** → carry hides fragility
- **Bear flattening** → funding costs ↑, collateral ↓, margin calls ↑ → systemic stress

### The Policy Put — Now Conditional

```
Pre-2022: Shock (deflation) → CB cuts → curve steepens → carry restored ✓
Post-2022: Shock (inflation) → CB must hike → curve stays flat/inverts
                                           → long end may not cooperate ✗
```

The put **still exists** but is conditional on inflation allowing it to be exercised.

### Sequencing Framework for Macro Trading

```
Inflationary choke-point shock:

Move 1:  ↑ Front-end rates  |  ↑ USD  |  ↓ Risk assets  |  Hedges fail
Move 2:  Growth damage  |  Credit stress  |  Front-end rally
Move 3:  Policy repair  |  BUT long end constrained by deficits + term premia
```

**Opportunity set:** Curves, real rates, inflation convexity, commodity optionality, FX dispersion, vol, cross-asset RV.  
**Not:** Simple long/short beta.

### Key Interview Points

- **Iran war disruption** = inflationary energy tax consuming inputs the US needs for consolidation
- **Choke points** price *availability*, not just price levels — hoarding dynamics differ from standard supply shocks
- US "consolidation policy" = narrow commitments vs. resources + rebuild domestic industrial base for China competition
- The paper cites Autor, Dorn & Hanson (2013) on China trade shock; Bernanke (2005) global savings glut; Goodhart & Pradhan (2020) on demographics

---

## Quick Reference Cheat Sheet

```
┌─────────────────────────────────────────────────────────────────────────┐
│  TOPIC                  FORMULA / NUMBER          SOURCE                │
├─────────────────────────────────────────────────────────────────────────┤
│  Noise eigenvalue band  λ± = σ²(1 ± √(N/T))²     Marchenko–Pastur      │
│  EWMA daily decay       λ = 0.94, t½ ≈ 11d        RiskMetrics           │
│  ES confidence          97.5% (≈VaR 99% normal)   Basel III/FRTB        │
│  Realised var interval  5-minute returns           Andersen et al.       │
│  Shrinkage target       Single-index / const corr  Ledoit & Wolf         │
│  Deflated SR            Correct for n trials       Bailey & LdP          │
│  Opt. execution         Sinh trajectory            Almgren & Chriss      │
│  Dynamic rebalance      Aim ahead by lead          Gârleanu & Pedersen   │
│  Transaction cost vol   σ̂² = σ²(1 + k/σ√Δt·sgn(Γ)) Leland             │
│  Frac diff order        d ∈ (0,1), min ADF pass    LdP AFML              │
│  Macro regime           Inverse-floater economy    Graham Capital        │
└─────────────────────────────────────────────────────────────────────────┘
```

[🔝 Back to Top](#table-of-contents)

---
---
