<div align="center">

# 🧮 Important Equations Cheatsheet
Important **Quantitative Finace** formulae to keep handy during interviews.

</div>

---
---

[↩️ Back to ./README.md](./README.md#-additional-resources)

---
---

## 📋 Table of Contents

### 🧮 FIRST-PRINCIPLES MATHEMATICAL DERIVATIONS

## Basic Formula
* [A. Quantitative Finance: Probability & Statistics Equations Reference](#a-quantitative-finance-probability--statistics-equations-reference)
* [B. Structural laws of variance, standard deviation, products, total decomposition, and the behavior of i.i.d. breakdowns](#b-structural-laws-of-variance-standard-deviation-products-total-decomposition-and-the-behavior-of-iid-breakdowns)
* [C. Multivariate asset allocation and portfolio optimization laws using matrix and linear algebra notation](#c-multivariate-asset-allocation-and-portfolio-optimization-laws-using-matrix-and-linear-algebra-notation)

## Domain-Wise Formula
* [1. Market Structure, Pricing & Futures Mechanics](#1-market-structure-pricing--futures-mechanics)
* [2. Transaction Cost Analysis (TCA) & Implementation Shortfall (IS)](#2-transaction-cost-analysis-tca--implementation-shortfall-is)
* [3. Market Impact & Optimal Execution Trajectories](#3-market-impact--optimal-execution-trajectories)
* [4. Microstructure Metrics, Aggregations & Risk Signals](#4-microstructure-metrics-aggregations--risk-signals)
* [5. Linear Algebra & Markovian Dynamics](#5-linear-algebra--markovian-dynamics)
* [6. Stochastic Calculus & Measure Transformations](#6-stochastic-calculus--measure-transformations)
* [7. Asset Pricing, Option PDEs & Advanced Volatility Dynamics](#7-asset-pricing-option-pdes--advanced-volatility-dynamics)
* [8. Signal Processing, Time Series & State Space Models](#8-signal-processing-time-series--state-space-models)
* [9. Alpha Research, Signal Evaluation & Performance Metrics](#9-alpha-research-signal-evaluation--performance-metrics)
* [10. Factor Modeling, Portfolio Construction & Risk Management](#10-factor-modeling-portfolio-construction--risk-management)
* [11. Quantitative Statistics & Hypothesis Testing](#11-quantitative-statistics--hypothesis-testing)

[🔝 Back to Top](#-table-of-contents)

---
---

## Basic Formula

[🔝 Back to Top](#-table-of-contents)

---
---

### A. Quantitative Finance: Probability & Statistics Equations Reference

| Category | Concept / Metric | Equation / Formula | Key Variables & Notes |
| --- | --- | --- | --- |
| **Probability Basics** | Expected Value (Discrete) | $`E[X] = \sum_{i} x_i P(X = x_i)`$ | $`x_i`$: outcomes, $`P(X=x_i)`$: probability mass function |
| **Probability Basics** | Expected Value (Continuous) | $`E[X] = \int_{-\infty}^{\infty} x f(x) dx`$ | $`f(x)`$: probability density function (PDF) |
| **Probability Basics** | Bayes' Theorem | $`P(A \mid B) = \frac{P(B \mid A)P(A)}{P(B)}`$ | $`P(A \mid B)`$: posterior probability, $`P(A)`$: prior probability |
| **Descriptive Stats** | Variance | $`Var(X) = \sigma^2 = E[(X - \mu)^2] = E[X^2] - (E[X])^2`$ | Measure of spread; $`\mu = E[X]`$ |
| **Descriptive Stats** | Sample Variance | $`s^2 = \frac{1}{n-1} \sum_{i=1}^n (x_i - \bar{x})^2`$ | Unbiased estimator of population variance ($n-1`$ degrees of freedom) |
| **Descriptive Stats** | Covariance | $`Cov(X,Y) = \sigma_{XY} = E[(X - \mu_X)(Y - \mu_Y)]`$ | Direction of joint linear association between two random variables |
| **Descriptive Stats** | Pearson Correlation | $`\rho_{XY} = \frac{Cov(X,Y)}{\sigma_X \sigma_Y}`$ | Scale-free linear dependency metric bounded in $`[-1, 1]`$ |
| **Descriptive Stats** | Skewness (3rd Moment) | $`S = E\left[\left(\frac{X-\mu}{\sigma}\right)^3\right]`$ | Asymmetry metric ($S > 0`$: right-tailed, fat right tail) |
| **Descriptive Stats** | Excess Kurtosis | $`K_{ex} = E\left[\left(\frac{X-\mu}{\sigma}\right)^4\right] - 3`$ | Fat-tail risk metric ($K_{ex} > 0`$: leptokurtic / fat-tailed vs Gaussian) |
| **Return Modeling** | Arithmetic Return | $`R_t = \frac{P_t - P_{t-1}}{P_{t-1}} = \frac{P_t}{P_{t-1}} - 1`$ | One-period percentage price change |
| **Return Modeling** | Continuous Log Return | $`r_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(1 + R_t)`$ | Additive across time periods: $`r_{0,T} = \sum_{t=1}^T r_t`$ |
| **Distributions** | Bernoulli PMF | $`P(X = x) = p^x (1 - p)^{1-x}, \quad x \in \{0, 1\}`$ | Modeling binary events (e.g., single-period default vs non-default) |
| **Distributions** | Binomial PMF | $`P(X = k) = \binom{n}{k} p^k (1 - p)^{n-k}`$ | Number of defaults in portfolio of $`n`$ independent assets; Binomial tree options |
| **Distributions** | Poisson PMF | $`P(X = k) = \frac{\lambda^k e^{-\lambda}}{k!}`$ | Discrete jump arrival process; frequency of rare loss / credit events |
| **Distributions** | Exponential PDF | $`f(x) = \lambda e^{-\lambda x}, \quad x \ge 0`$ | Continuous time-to-default model with constant hazard rate $`\lambda`$ |
| **Distributions** | Continuous Uniform PDF | $`f(x) = \frac{1}{b - a}, \quad a \le x \le b`$ | Foundation for Inverse Transform Sampling in Monte Carlo simulations |
| **Distributions** | Gaussian (Normal) PDF | $`f(x) = \frac{1}{\sigma \sqrt{2\pi}} \exp\left(-\frac{(x - \mu)^2}{2\sigma^2}\right)`$ | Standard benchmark distribution for log returns |
| **Distributions** | Lognormal Stock Price | $`P_t = P_0 \exp\left(\left(\mu - \frac{1}{2}\sigma^2\right)t + \sigma W_t\right)`$ | Price distribution under Geometric Brownian Motion ($P_t > 0$) |
| **Distributions** | Student's t PDF | $`f(x) = \frac{\Gamma(\frac{\nu+1}{2})}{\sqrt{\nu\pi}\,\Gamma(\frac{\nu}{2})} \left(1 + \frac{x^2}{\nu}\right)^{-\frac{\nu+1}{2}}`$ | Models fat-tailed return distributions; $`\nu`$: degrees of freedom |
| **Stochastic Calculus** | Geometric Brownian Motion | $`dS_t = \mu S_t dt + \sigma S_t dW_t`$ | Standard SDE for asset prices; $`dW_t \sim \mathcal{N}(0, dt)`$ |
| **Stochastic Calculus** | Itô's Lemma (1D) | $`df(t,S_t) = \left(\frac{\partial f}{\partial t} + \mu S_t \frac{\partial f}{\partial S} + \frac{1}{2}\sigma^2 S_t^2 \frac{\partial^2 f}{\partial S^2}\right)dt + \sigma S_t \frac{\partial f}{\partial S} dW_t`$ | Fundamental rule of stochastic differentiation for option pricing |
| **Stochastic Calculus** | Ornstein-Uhlenbeck | $`dX_t = \theta(\mu - X_t)dt + \sigma dW_t`$ | Mean-reverting stochastic process (used for interest rates, volatility) |
| **Time Series** | AR(1) Process | $`X_t = c + \phi X_{t-1} + \epsilon_t`$ | Autoregressive process of order 1; $`\epsilon_t \sim \mathcal{N}(0, \sigma^2)`$ |
| **Time Series** | MA(1) Process | $`X_t = \mu + \epsilon_t + \theta \epsilon_{t-1}`$ | Moving average process of order 1 |
| **Time Series** | GARCH(1,1) Volatility | $`\sigma_t^2 = \omega + \alpha \epsilon_{t-1}^2 + \beta \sigma_{t-1}^2`$ | Time-varying volatility clustering model ($\omega > 0, \alpha+\beta < 1$) |
| **Portfolio Theory** | Expected Return | $`E[R_p] = \mathbf{w}^T \mathbf{\mu} = \sum_{i=1}^N w_i E[R_i]`$ | Weighted sum of individual asset expected returns |
| **Portfolio Theory** | Portfolio Variance | $`\sigma_p^2 = \mathbf{w}^T \mathbf{\Sigma} \mathbf{w} = \sum_{i}\sum_{j} w_i w_j \sigma_{ij}`$ | Asset covariance matrix $`\mathbf{\Sigma}`$ and weight vector $`\mathbf{w}`$ |
| **Portfolio Theory** | Sharpe Ratio | $`SR = \frac{E[R_p] - R_f}{\sigma_p}`$ | Risk-adjusted return metric relative to total volatility |
| **Portfolio Theory** | Sortino Ratio | $`Sortino = \frac{E[R_p] - R_f}{\sigma_d}`$ | Risk-adjusted return using downside deviation $`\sigma_d`$ |
| **Risk Metrics** | Parametric Value at Risk | $`VaR_{\alpha} = -(\mu + z_{\alpha} \sigma) \cdot V`$ | Maximum expected loss at confidence level $`\alpha`$ over horizon; $`V`$: portfolio value |
| **Risk Metrics** | Expected Shortfall (CVaR) | $`ES_{\alpha} = E[-R \mid -R \ge VaR_{\alpha}]`$ | Average loss conditional on exceeding VaR threshold (coherent risk measure) |
| **Asset Pricing** | CAPM Beta | $`\beta_i = \frac{Cov(R_i, R_m)}{Var(R_m)} = \frac{\sigma_{im}}{\sigma_m^2}`$ | Systematic market risk exposure metric |
| **Asset Pricing** | CAPM Expected Return | $`E[R_i] = R_f + \beta_i \left(E[R_m] - R_f\right)`$ | Capital Asset Pricing Model formula |
| **Regression Modeling** | OLS Estimator | $`\hat{\mathbf{\beta}} = (\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T \mathbf{y}`$ | Vector of estimated coefficients in multiple linear regression |
| **Regression Modeling** | Coefficient of Determination | $`R^2 = 1 - \frac{\sum (y_i - \hat{y}_i)^2}{\sum (y_i - \bar{y})^2} = 1 - \frac{SS_{res}}{SS_{tot}}`$ | Proportion of variance explained by the statistical model |
| **Option Valuation** | Black-Scholes PDE | $`\frac{\partial V}{\partial t} + \frac{1}{2}\sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} + r S \frac{\partial V}{\partial S} - r V = 0`$ | Fundamental partial differential equation for continuous derivative pricing |
| **Option Valuation** | Black-Scholes Call Price | $`C(S,t) = S_t N(d_1) - K e^{-r(T-t)} N(d_2)`$ | Analytical price for European call option ($N(\cdot)`$: cumulative standard normal CDF) |
| **Option Valuation** | Black-Scholes $`d_1`$ Parameter | $`d_1 = \frac{\ln(S_t/K) + \left(r + \frac{1}{2}\sigma^2\right)(T-t)}{\sigma \sqrt{T-t}}`$ | Normalized distance parameter for stock price path expectation |
| **Option Valuation** | Black-Scholes $`d_2`$ Parameter | $`d_2 = d_1 - \sigma \sqrt{T-t}`$ | Probability of option expiring in-the-money under risk-neutral measure |
| **Option Valuation** | Put-Call Parity | $`C_t - P_t = S_t - K e^{-r(T-t)}`$ | No-arbitrage relation between standard European call and put options |

[🔝 Back to Top](#-table-of-contents)

---
---

### B. Structural laws of variance, standard deviation, products, total decomposition, and the behavior of i.i.d. breakdowns

| Category | Concept / Metric | Equation / Formula | Key Variables & Notes |
|---|---|---|---|
| Linear Transforms | Variance Scaling | $`\text{Var}(cX + b) = c^2\text{Var}(X)`$ | $`c, b`$: constants. Variance scales quadratically; shifts do not alter spread. |
| Linear Transforms | Std Dev Scaling | $`\sigma_{cX+b} = \vert{}c\vert{}\sigma_X`$ | $`\sigma_X`$: standard deviation. Scales linearly; always absolute value (positive). |
| Linear Transforms | Covariance Scaling | $`\text{Cov}(aX, bY) = ab\,\text{Cov}(X,Y)`$ | $`a, b`$: constants scaling respective variables. |
| Joint Operations | General Sum Variance | $`\text{Var}(X + Y) = \text{Var}(X) + \text{Var}(Y) + 2\text{Cov}(X,Y)`$ | Dependent variables. Covariance handles joint directional variation. |
| Joint Operations | General Difference Var | $`\text{Var}(X - Y) = \text{Var}(X) + \text{Var}(Y) - 2\text{Cov}(X,Y)`$ | Dependent variables. Negative sign applies strictly to the covariance term. |
| Joint Operations | Independent Sum / Diff | $`\text{Var}(X \pm Y) = \text{Var}(X) + \text{Var}(Y)`$ | Independent or i.i.d. variables. Covariance term collapses to $`0`$. Variances always add. |
| Joint Operations | General Sum Std Dev | $`\sigma_{X \pm Y} = \sqrt{\text{Var}(X) + \text{Var}(Y) \pm 2\text{Cov}(X,Y)}`$ | Standard deviations cannot be added linearly ($\sigma_{X+Y} \neq \sigma_X + \sigma_Y$). |
| Joint Operations | Independent Product Var | $`\text{Var}(XY) = \mathbb{E}[X]^2\text{Var}(Y) + \mathbb{E}[Y]^2\text{Var}(X) + \text{Var}(X)\text{Var}(Y)`$ | Goodman's formula for independent variables. Fails if variables are dependent. |
| Decomposition | Law of Total Variance | $`\text{Var}(Y) = \mathbb{E}[\text{Var}(Y\vert{}X)] + \text{Var}(\mathbb{E}[Y\vert{}X])`$ | Direct analogue to Law of Total Probability. Breaks total risk into intra-regime noise and inter-regime variance. |
| Portfolio / i.i.d. | i.i.d. Portfolio Variance | $`\text{Var}(R_p) = \frac{\sigma^2}{n}`$ | Equally weighted asset returns. Variance drops to $`0`$ as asset count $`n \to \infty`$. |
| Portfolio / i.i.d. | Non-i.i.d. Portfolio Var | $`\text{Var}(R_p) = \frac{\sigma^2}{n} + \frac{n-1}{n}\bar{\rho}\sigma^2`$ | $`\bar{\rho}`$: average correlation. As $`n \to \infty$, variance converges to $`\bar{\rho}\sigma^2`$ (systemic risk floor). |
| i.i.d. Breakdowns | Autocorrelation | $`\text{Cov}(X_t, X_{t-k}) \neq 0`$ | Breaks "Independent" half. Serial dependency in time series (e.g., high-frequency execution). |
| i.i.d. Breakdowns | Heteroskedasticity | $`\text{Var}(X_t \vert{} \mathcal{F}_{t-1}) = \sigma_t^2`$ | Breaks "Identically Distributed" half. Volatility changes over time based on history (handled via GARCH). |
| i.i.d. Breakdowns | Regime-Switching | $`X_t \sim \mathcal{N}(\mu_{S_t}, \sigma_{S_t}^2)`$ | Breaks "Identically Distributed" half. Parameters depend on hidden discrete state $`S_t`$ (handled via HMM). |

[🔝 Back to Top](#-table-of-contents)

---
---

### C. Multivariate asset allocation and portfolio optimization laws using matrix and linear algebra notation

| Category | Concept / Metric | Equation / Formula | Key Variables & Notes |
|---|---|---|---|
| Portfolio Layout | Weights Vector | $`\mathbf{w} = \begin{bmatrix} w_1 & w_2 & \dots & w_n \end{bmatrix}^T`$ | $`n \times 1`$ column vector representing capital allocation percentages. |
| Portfolio Layout | Return Vector | $`\mathbf{R} = \begin{bmatrix} R_1 & R_2 & \dots & R_n \end{bmatrix}^T`$ | $`n \times 1`$ column vector of asset-level random returns. |
| Portfolio Layout | Expected Returns Vector | $`\boldsymbol{\mu} = \mathbb{E}[\mathbf{R}] = \begin{bmatrix} \mu_1 & \mu_2 & \dots & \mu_n \end{bmatrix}^T`$ | $`n \times 1`$ column vector containing historical or predicted asset means. |
| Risk Infrastructure | Covariance Matrix | $`\boldsymbol{\Sigma} = \mathbb{E}[(\mathbf{R} - \boldsymbol{\mu})(\mathbf{R} - \boldsymbol{\mu})^T]`$ | $`n \times n`$ symmetric, positive semi-definite matrix of asset covariances. |
| Risk Infrastructure | Correlation Matrix Relationship | $`\boldsymbol{\Sigma} = \mathbf{D} \mathbf{C} \mathbf{D}`$ | $`\mathbf{D}`$: diagonal matrix of standard deviations; $`\mathbf{C}`$: $`n \times n`$ asset correlation matrix. |
| Aggregation Laws | Expected Portfolio Return | $`\mu_p = \mathbb{E}[R_p] = \mathbf{w}^T \boldsymbol{\mu}`$ | Dot product reducing vector distributions to a single scalar expected return. |
| Aggregation Laws | Total Portfolio Variance | $`\sigma_p^2 = \text{Var}(R_p) = \mathbf{w}^T \boldsymbol{\Sigma} \mathbf{w}`$ | Quadratic form mapping asset-level interactions into a single total risk scalar. |
| Aggregation Laws | Portfolio Volatility | $`\sigma_p = \sqrt{\mathbf{w}^T \boldsymbol{\Sigma} \mathbf{w}}`$ | Standard deviation of the managed portfolio. Square root of the quadratic form. |
| Risk Attribution | Marginal Contribution to Risk | $`\text{MCR} = \frac{\boldsymbol{\Sigma} \mathbf{w}}{\sqrt{\mathbf{w}^T \boldsymbol{\Sigma} \mathbf{w}}}`$ | $`n \times 1`$ vector indicating the rate of risk change per unit increase in asset weight. |
| Risk Attribution | Component Contribution to Risk | $`\text{CCR} = \mathbf{w} \odot \left( \frac{\boldsymbol{\Sigma} \mathbf{w}}{\sqrt{\mathbf{w}^T \boldsymbol{\Sigma} \mathbf{w}}} \right)`$ | $`n \times 1`$ risk breakdown vector; uses Hadamard element-wise product ($\odot$). |
| Optimization Metrics | Mean-Variance Objective (Markowitz) | $`\max_{\mathbf{w}} \left( \mathbf{w}^T \boldsymbol{\mu} - \frac{\lambda}{2} \mathbf{w}^T \boldsymbol{\Sigma} \mathbf{w} \right)`$ | $`\lambda`$: risk aversion coefficient balancing return maximization and variance minimization. |
| Optimization Metrics | Fully Invested Constraint | $`\mathbf{w}^T \mathbf{1} = 1`$ | $`\mathbf{1}`$: $`n \times 1`$ column vector of ones. Forces weights to sum exactly to 100%. |
| Optimization Metrics | Global Minimum Variance (GMV) Weights | $`\mathbf{w}_{\text{GMV}} = \frac{\boldsymbol{\Sigma}^{-1} \mathbf{1}}{\mathbf{1}^T \boldsymbol{\Sigma}^{-1} \mathbf{1}}`$ | Left-tail optimization targeting the single lowest risk portfolio without regarding returns. |
| Optimization Metrics | Maximum Sharpe Ratio Weights | $`\mathbf{w}_{\text{MSR}} \propto \boldsymbol{\Sigma}^{-1}(\boldsymbol{\mu} - R_f \mathbf{1})`$ | Tangency portfolio calculation; $`R_f`$: risk-free scalar rate. Requires normalization. |
| Matrix Pathology | Dimensionality Breakdown | $`n \gg T \implies \det(\boldsymbol{\Sigma}) \to 0`$ | Number of assets ($n$) exceeds historical time steps ($T$). Matrix becomes uninvertible. |
| Matrix Correction | Ledoit-Wolf Shrinkage | $`\boldsymbol{\Sigma}_{\text{shrunk}} = \delta \mathbf{F} + (1 - \delta)\boldsymbol{\Sigma}`$ | $`\mathbf{F}`$: structured target matrix; $`\delta \in [0,1]`$: shrinkage constant to stabilize inversions. |

[🔝 Back to Top](#-table-of-contents)

---
---

## Domain-Wise Formula

[🔝 Back to Top](#-table-of-contents)

---
---

### 1. Market Structure, Pricing & Futures Mechanics

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **TAS Contract Pricing**<br> | $`P_{\text{TAS}} = S_{\text{settle}} + \delta`$<br> | • $`P_{\text{TAS}}`$: Execution fill price<br><br>• $`S_{\text{settle}}`$: Official closing settlement price<br><br>• $`\delta`$: Agreed basis differential | Locks execution price relative to the exchange settlement, eliminating intraday price volatility while introducing benchmark settlement risk. |
| **Variation Margin (VM)**<br> | $`\text{VM}_t = (F_t - F_{t-1}) \times M \times N`$<br> | • $`\text{VM}_t`$: Daily MTM cash flow<br><br>• $`F_t, F_{t-1}`$: Futures settlement prices<br><br>• $`M`$: Contract multiplier<br><br>• $`N`$: Contract quantity | Resets contract present value to zero daily, creating intraday cash funding demands distinct from OTC forward contracts. |
| **Annualized Futures Roll Yield**<br> | $`\text{Roll Yield} \approx -\frac{F(t,T_{\text{next}}) - F(t,T_{\text{front}})}{F(t,T_{\text{front}})} \times \frac{365}{T_{\text{next}}-T_{\text{front}}}`$<br> | • $`F(t,T)`$: Futures price at time $`t`$ for maturity $`T`$<br><br>• $`T_{\text{next}}, T_{\text{front}}`$: Expiry dates | Measures contango drag or backwardation yield when systematically rolling expiring futures positions via calendar spreads. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 2. Transaction Cost Analysis (TCA) & Implementation Shortfall (IS)

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Implementation Shortfall (Perold 1988)**<br> | $`\text{IS} = d \cdot \left[ (P_{\text{fill,avg}} - P_{\text{arrival}}) Q_{\text{filled}} + (P_{\text{final}} - P_{\text{arrival}}) Q_{\text{unfilled}} \right]`$<br> | • $`d \in \{+1, -1\}`$: Buy/Sell sign<br><br>• $`P_{\text{arrival}}`$: Benchmark mid-price<br><br>• $P_{\text{fill,avg}}$: VWAP of executed fills<br><br>• $Q_{\text{filled}}, Q_{\text{unfilled}}$: Executed vs unfilled size | Core benchmark quantifying execution friction against paper portfolio returns, penalizing market impact and opportunity cost. |
| **Four-Way IS Cost Decomposition**<br> | $`\text{IS} = \text{Delay Cost} + \text{Explicit Fees} + \text{Realized Impact} + \text{Opportunity Cost}`$<br> | • $`\text{Delay Cost} = d \cdot (P_{\text{release}} - P_{\text{decision}}) Q_{\text{parent}}`$<br><br>• $`\text{Explicit Fees}`$: Commissions, clearing, exchange fees| Isolates operational latency, broker commissions, active execution impact, and residual price drift. |
| **Slippage Attribution Waterfall**<br> | $`\text{Slippage} = d \cdot \left[ (P_{\text{release}} - P_{\text{decision}}) + (P_{\text{fill,avg}} - P_{\text{release}}) + (P_{\text{close}} - P_{\text{fill,avg}}) \right]`$<br> | • $P_{\text{decision}}$: PM decision mark<br><br>• $`P_{\text{release}}`$: Order release timestamp<br><br>• $`P_{\text{close}}`$: Session close mark | Decomposes pre-trade routing delay, execution trading friction, and post-trade adverse price selection. |
| **Normalized IS Components (bps)**<br> | $`\text{IS}_{\text{bps}} = \frac{Q_{\text{filled}} \cdot \text{ExecCost}_{\text{bps}} + Q_{\text{unfilled}} \cdot \text{OppCost}_{\text{bps}}}{Q_{\text{parent}}}`$<br><br>$`\text{ExecCost}_{\text{bps}} = d \cdot 10^4 \cdot \frac{P_{\text{fill,avg}} - P_{\text{arrival}}}{P_{\text{arrival}}}`$<br> | • $`\text{IS}_{\text{bps}}`$: Total shortfall in basis points<br><br>• $`Q_{\text{parent}}`$: Total parent order quantity | Standardizes relative execution cost metrics across multi-asset portfolios and varying contract scales. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 3. Market Impact & Optimal Execution Trajectories

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Kyle’s Lambda Microstructure Model** | $`\Delta P_t = \lambda \cdot y_t + \epsilon_t`$<br><br><br>$`\lambda = \frac{\text{Cov}(\Delta P, y)}{\text{Var}(y)} = \frac{\sigma_v}{2 \sigma_u}`$ | • $`\Delta P_t`$: Price change over interval $`t`$<br><br>• $`y_t`$: Net order flow (informed + noise)<br><br>• $`\sigma_v`$: Variance of asset fundamental value<br><br>• $\sigma_u$: Variance of noise trader order flow | Measures market illiquidity and adverse selection cost. Essential for sizing small orders, passive market-making, and HFT liquidity routing algorithms. |
| **Empirical Square-Root Market Impact Law**<br> | $`\Delta P = \sigma \cdot Y \cdot \sqrt{\frac{Q}{V}}`$<br> | • $`\Delta P`$: Percentage price impact<br><br>• $`\sigma`$: Daily volatility<br><br>• $`Y \in [0.5, 0.7]`$: Empirical constant<br><br>• $`Q`$: Parent volume; $`V`$: ADV | Estimates price displacement for parent order execution and pre-trade strategy participation rate scaling. |
| **Almgren-Chriss Total Cost Objective**<br> | $`\text{Total Cost} = \int_0^T \eta \dot{x}(t)^2 dt + \frac{1}{2}\gamma X^2 + \lambda \int_0^T \sigma^2 x(t)^2 dt`$<br> | • $`x(t)`$: Inventory trajectory<br><br>• $`\dot{x}(t)`$: Trading velocity<br><br>• $`\eta, \gamma`$: Temporary & permanent impact parameters<br><br>• $`\lambda`$: Risk aversion coefficient | Balances temporary trading impact costs against inventory volatility exposure over an execution schedule. |
| **Almgren-Chriss Optimal Trajectory (Single Asset)**<br> | $`x(t) = X \cdot \frac{\sinh\left(\kappa(T-t)\right)}{\sinh(\kappa T)}`$<br><br>$`\kappa = \sqrt{\frac{\lambda \sigma^2}{\eta}}`$<br> | • $`X`$: Initial inventory $`x(0)`$<br><br>• $`T`$: Execution horizon<br><br>• $`\kappa`$: Urgency parameter | Analytical solution for continuous inventory liquidation. Higher urgency $`\kappa`$ accelerates liquidation to avoid volatility risk. |
| **Almgren-Chriss Trajectory (Multi-Asset Matrix System)**<br> | $`\mathbf{x}(t) = \sinh\left(\boldsymbol{\Gamma}(T-t)\right) \left[\sinh(\boldsymbol{\Gamma} T)\right]^{-1} \mathbf{x}(0)`$<br><br>$`\boldsymbol{\Gamma} = \sqrt{\lambda \boldsymbol{\eta}^{-1} \boldsymbol{\Sigma}}`$<br> | • $`\mathbf{x}(t)`$: Inventory vector<br><br>• $`\boldsymbol{\Sigma}`$: Covariance matrix<br><br>• $`\boldsymbol{\eta}`$: Temporary impact matrix | Generalizes optimal execution to multi-asset portfolios, incorporating asset correlations and cross-asset market impact dynamics. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 4. Microstructure Metrics, Aggregations & Risk Signals

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Order Book Micro-Price**<br> | $`P_{\text{micro}} = \frac{P_{\text{bid}} \cdot S_{\text{ask}} + P_{\text{ask}} \cdot S_{\text{bid}}}{S_{\text{bid}} + S_{\text{ask}}}`$<br> | • $`P_{\text{bid}}, P_{\text{ask}}`$: Bid & Ask prices<br><br>• $`S_{\text{bid}}, S_{\text{ask}}`$: Displayed sizes | Size-weighted fair value indicator predicting short-term order book directional movement. |
| **Volume-Weighted Average Price (VWAP)**<br> | $`P_{\text{VWAP}} = \frac{\sum_{i=1}^N P_i \cdot Q_i}{\sum_{i=1}^N Q_i}`$<br> | • $`P_i`$: Trade fill price<br><br>• $`Q_i`$: Trade fill volume | Standard benchmark evaluating execution performance against volume-weighted market activity. |
| **Time-Weighted Average Price (TWAP)**<br> | $`P_{\text{TWAP}} = \frac{\sum_{i=1}^N P_i \cdot \Delta t_i}{\sum_{i=1}^N \Delta t_i}`$<br> | • $`P_i`$: Prevailing price<br><br>• $`\Delta t_i`$: Duration interval | Measures time-uniform execution quality across designated market windows. |
| **Peak-to-Trough Running Drawdown**<br> | $`\text{Drawdown}_t = \frac{P_t - \max_{\tau \le t} P_\tau}{\max_{\tau \le t} P_\tau} \times 100\%`$<br> | • $P_t$: Equity / asset value at time $t$<br><br>• $`\max_{\tau \le t} P_\tau`$: Historical peak value | Real-time risk management metric tracking peak-to-trough capital erosion. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 5. Linear Algebra & Markovian Dynamics

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Gauss-Jordan System Resolution** | $`\mathbf{A} \mathbf{x} = \mathbf{b} \implies [\mathbf{A} \mid \mathbf{I}] \xrightarrow{\text{RREF}} [\mathbf{I} \mid \mathbf{A}^{-1}]`$ | • $`\mathbf{A}`$: Coefficients matrix ($`n \times n`$)<br><br>• $`\mathbf{x}, \mathbf{b}`$: System vectors<br><br>• $\text{RREF}$: Reduced Row Echelon Form | Fundamental numerical linear algebra operation for solving portfolio weight systems and inverting covariance matrices. |
| **Chapman-Kolmogorov Equation** | $`P_{ij}(t+s) = \sum_{k \in S} P_{ik}(t) P_{kj}(s)`$<br><br><br>$`\mathbf{P}(t+s) = \mathbf{P}(t)\mathbf{P}(s)`$ | • $`P_{ij}(t)`$: Transition probability from state $`i`$ to state $`j`$<br><br>• $`S`$: Finite state space | Computes multi-step state transition probabilities across multi-period regime models and order book queuing models. |
| **Markov Transition Dynamics** | $`\mathbf{\pi}_{t+1} = \mathbf{\pi}_t \mathbf{P}, \quad \text{subject to } \sum_{j} P_{ij} = 1`$ | • $`\mathbf{\pi}_t`$: State distribution vector<br><br>• $\mathbf{P}$: Stochastic transition matrix | Models order book imbalance transitions, regime changes, and credit rating migrations. |
| **Gram-Schmidt Orthogonalization** | $`\mathbf{u}_k = \mathbf{v}_k - \sum_{j=1}^{k-1} \frac{\langle \mathbf{v}_k, \mathbf{u}_j \rangle}{\Vert{}\mathbf{u}_j\Vert{}^2} \mathbf{u}_j, \quad \mathbf{e}_k = \frac{\mathbf{u}_k}{\Vert{}\mathbf{u}_k\Vert{}}`$ | • $`\mathbf{v}_k`$: Raw signal vector<br><br>• $`\mathbf{u}_k, \mathbf{e}_k`$: Orthogonal / Orthonormal signal vectors | Removes collinearity across quantitative alpha signals to isolate incremental predictive power. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 6. Stochastic Calculus & Measure Transformations

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Itô’s Lemma (Multivariate)** | $`df(t, \mathbf{X}_t) = \left( \frac{\partial f}{\partial t} + \boldsymbol{\mu}^\top \nabla f + \frac{1}{2} \text{Tr}\left( \boldsymbol{\sigma}\boldsymbol{\sigma}^\top \mathbf{H}_f \right) \right) dt + (\nabla f)^\top \boldsymbol{\sigma} d\mathbf{W}_t`$ | • $`f`$: Smooth scalar function<br><br>• $`\boldsymbol{\mu}, \boldsymbol{\sigma}`$: Drift vector & Volatility matrix<br><br>• $`\mathbf{H}_f`$: Hessian matrix of $`f`$ | Derivative asset pricing, non-linear pay-off dynamics, and continuous-time portfolio volatility propagation. |
| **Martingale Property** | $`\mathbb{E}^{\mathbb{P}}[M_t \mid \mathcal{F}_s] = M_s, \quad \forall s \le t`$ | • $`M_t`$: Stochastic process<br><br>• $`\mathcal{F}_s`$: Information filtration up to time $`s`$<br><br>• $`\mathbb{P}`$: Probability measure | Foundation for arbitrage-free derivative pricing, where discounted asset prices form martingales under risk-neutral measures. |
| **Driftless Local Martingale Process** | $`dX_t = \sigma(t, X_t) dW_t`$ | • $X_t$: Driftless price or spread process<br><br>• $`W_t`$: Standard Wiener process | Models risk-neutral derivative dynamics and statistical arbitrage spreads stripped of structural trend drifts. |
| **Girsanov’s Theorem (Change of Measure)** | $`\frac{d\mathbb{Q}}{d\mathbb{P}} \Big\vert{}_{\mathcal{F}_T} = \mathcal{E}\left( -\int_0^T \theta_t dW_t^\mathbb{P} \right) = \text{exp}\biggl( -\int_0^T \theta_t dW_t^\mathbb{P} - \frac{1}{2}\int_0^T \theta_t^2 dt \biggl)`$ | • $`\mathbb{P}, \mathbb{Q}`$: Physical & Risk-Neutral measures<br><br>• $`\theta_t = \frac{\mu_t - r_t}{\sigma_t}`$: Market price of risk | Converts physical asset dynamics (with drift) into risk-neutral measure processes for derivative pricing. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 7. Asset Pricing, Option PDEs & Advanced Volatility Dynamics

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Black-Scholes PDE** | $`\frac{\partial V}{\partial t} + \frac{1}{2} \sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} + r S \frac{\partial V}{\partial S} - r V = 0`$ | • $`V(S,t)`$: Option price<br><br>• $`S`$: Underlying spot price<br><br>• $\sigma$: Constant volatility; $r$: Risk-free rate | Standard framework for European option pricing and continuous dynamic delta hedging. |
| **Bachelier Normal Option Model** | $`C = (F - K) \Phi(d) + \sigma_N \sqrt{T} \phi(d)`$<br>$`d = \frac{F - K}{\sigma_N \sqrt{T}}`$ | • $`\sigma_N`$: Normal (absolute) volatility<br><br>• $`F, K`$: Forward & Strike prices<br><br>• $`\Phi, \phi`$: Gaussian CDF & PDF | Standard model for fixed income, rate swaps, and negative price regimes (e.g., negative crude futures). |
| **Merton Jump-Diffusion Model** | $`dS_t = (\mu - \lambda k) S_t dt + \sigma S_t dW_t + (Y - 1) S_t dN_t`$ | • $`N_t`$: Poisson process with intensity $`\lambda`$<br><br>• $`Y`$: Random jump size multiplier<br><br>• $k = \mathbb{E}[Y - 1]$ | Captures systemic price gaps and fat-tailed asset return distributions driven by discrete news events. |
| **Heston Stochastic Volatility Model** | $`dS_t = \mu S_t dt + \sqrt{v_t} S_t dW_t^S`$<br><br>$`dv_t = \kappa(\theta - v_t) dt + \xi \sqrt{v_t} dW_t^v`$<br><br>$`d\langle W^S, W^v \rangle_t = \rho dt`$ | • $`v_t`$: Variance process<br><br>• $`\kappa, \theta, \xi`$: Reversion rate, mean variance, vol-of-vol<br><br>• $`\rho`$: Correlation parameter | Models dynamic volatility skew/smile and volatility clustering across derivative books. |
| **Dupire Local Volatility Model** | $`\sigma_{\text{loc}}^2(K, T) = \frac{\frac{\partial C}{\partial T} + r K \frac{\partial C}{\partial K}}{\frac{1}{2} K^2 \frac{\partial^2 C}{\partial K^2}}`$ | • $`\sigma_{\text{loc}}(K,T)`$: Deterministic local volatility surface<br><br>• $`C(K,T)`$: European Call price surface | Extracts a unique, deterministic volatility surface to exactly match all traded option strike/expiry quotes. |
| **Constant Elasticity of Variance (CEV)** | $dS_t = \mu S_t dt + \sigma S_t^{\beta} dW_t$ | • $\beta$: Elasticity parameter ($\beta < 1$ yields leverage effect) | Models price-dependent volatility without adding extra stochastic variance dimensions. |
| **SABR Stochastic Volatility Model** | $`dF_t = \alpha_t F_t^\beta dW_t^F`$<br><br>$`d\alpha_t = \nu \alpha_t dW_t^\alpha, \quad d\langle W^F, W^\alpha \rangle_t = \rho dt`$ | • $`\alpha_t`$: Volatility level; $`\beta`$: Constant elasticity<br><br>• $`\nu`$: Vol-of-vol; $`\rho`$: Spot-vol correlation | Standard framework for interest rate option swaptions and volatility smile dynamics modeling. |
| **Hull-White Stochastic Volatility Model** | $`dS_t = \mu S_t dt + \sqrt{V_t} S_t dW_t^S`$<br><br>$`dV_t = \mu_v V_t dt + \xi V_t dW_t^V`$ | • $`V_t`$: Lognormal variance process<br><br>• $`\mu_v`$: Volatility drift rate | Early stochastic volatility model assuming lognormal volatility growth for exotic option pricing. |
| **Variance Gamma (VG) Pure Jump Model** | $`X_t^{\text{VG}} = \theta \Gamma(t; 1, \nu) + \sigma W(\Gamma(t; 1, \nu))`$ | • $`\Gamma(t; 1, \nu)`$: Gamma process time-change parameter<br><br>• $`\theta, \nu`$: Skewness and kurtosis parameters | Models price processes via infinite activity jumps, capturing option smiles in short-dated contracts. |
| **CGMY Pure Jump Model** | $`k_{\text{CGMY}}(x) = \begin{cases} C \frac{e^{-G\vert{}x\vert{}}}{\vert{}x\vert{}^{1+Y}} & x < 0 \\ C \frac{e^{-Mx}}{\vert{}x\vert{}^{1+Y}} & x > 0 \end{cases}`$ | • $`C`$: Activity rate; $`G, M`$: Left/Right tail decay rates<br><br>• $`Y`$: Jump paths fine structure ($Y < 2$) | Flexible pure-jump Levy framework for control over jump frequency and tail risk decay rates. |
| **Rough Fractional Brownian Motion** | $`dv_t = \kappa(\theta - v_t) dt + \xi v_t^\alpha dW_t^H`$<br><br>$`\mathbb{E}\left[(W_t^H - W_s^H)^2\right] = \vert{}t - s\vert{}^{2H}`$ | • $`W_t^H`$: Fractional Brownian motion<br><br>• $`H \in (0, 1/2)`$: Hurst parameter | Models rough volatility paths to reproduce steep short-dated option smiles with few parameters. |
| **SVJD (Stochastic Volatility Jump-Diffusion)** | $`dS_t = (\mu - \lambda k) S_t dt + \sqrt{v_t} S_t dW_t^S + (Y - 1) S_t dN_t`$<br><br>$`dv_t = \kappa(\theta - v_t) dt + \xi \sqrt{v_t} dW_t^v`$ | • Integrates Heston stochastic variance with Merton discrete jumps | Institutional framework for option books: jumps handle short-term option crashes, while stochastic vol models long-term smiles. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 8. Signal Processing, Time Series & State Space Models

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Ornstein-Uhlenbeck (OU) Mean-Reversion** | $`dX_t = \theta (\mu - X_t) dt + \sigma dW_t`$ | • $`\theta`$: Reversion speed; $`\mu`$: Long-term mean<br><br>• $`\sigma`$: Process volatility | Standard continuous-time model for statistical arbitrage, pairs trading, and cross-asset spread trading. |
| **GARCH(1,1) Volatility Model** | $`\sigma_t^2 = \omega + \alpha \epsilon_{t-1}^2 + \beta \sigma_{t-1}^2, \quad \alpha + \beta < 1`$ | • $`\omega`$: Baseline variance; $`\alpha`$: ARCH parameter<br><br>• $`\beta`$: GARCH parameter | Primary discrete-time model for volatility clustering, risk scaling, and dynamic margin calculation. |
| **EGARCH (Exponential GARCH)** | $`\ln(\sigma_t^2) = \omega + \beta \ln(\sigma_{t-1}^2) + \alpha \left[ \frac{\vert{}\epsilon_{t-1}\vert{}}{\sigma_{t-1}} - \sqrt{\frac{2}{\pi}} \right] + \gamma \frac{\epsilon_{t-1}}{\sigma_{t-1}}`$ | • $`\gamma`$: Asymmetric leverage coefficient | Captures volatility asymmetry (the leverage effect), where negative returns induce higher volatility than positive returns. |
| **NMS-GARCH (Non-linear Mixture Switching GARCH)** | $`\sigma_t^2 = \sum_{k=1}^K p_{k,t} \left( \omega_k + \alpha_k \epsilon_{t-1}^2 + \beta_k \sigma_{t-1, k}^2 \right)`$ | • $`p_{k,t}`$: Regime probability for state $`k`$ | Captures non-linear volatility dynamics by blending GARCH parameters across distinct volatility regimes. |
| **Kalman Filter (State-Space Updating)** | $`\hat{\mathbf{x}}_{k \mid k} = \hat{\mathbf{x}}_{k \mid k-1} + \mathbf{K}_k \left( \mathbf{z}_k - \mathbf{H}_k \hat{\mathbf{x}}_{k \mid k-1} \right)`$<br><br>$`\mathbf{K}_k = \mathbf{P}_{k \mid k-1} \mathbf{H}_k^\top \left( \mathbf{H}_k \mathbf{P}_{k \mid k-1} \mathbf{H}_k^\top + \mathbf{R}_k \right)^{-1}`$ | • $`\hat{\mathbf{x}}`$: State estimate vector<br><br>• $`\mathbf{K}_k`$: Kalman Gain matrix<br><br>• $`\mathbf{H}_k`$: Observation matrix; $`\mathbf{R}_k`$: Noise covariance | Real-time parameter estimation for hedge ratios, dynamic beta tracking, and high-frequency signal filtration. |
| **Hidden Markov Model (MS-AR)** | $`y_t = c_{S_t} + \sum_{i=1}^p \phi_{i, S_t} y_{t-i} + \epsilon_t, \quad \epsilon_t \sim \mathcal{N}(0, \sigma_{S_t}^2)`$ | • $`S_t \in \{1, \dots, K\}`$: Unobserved discrete state<br><br>• $`c_{S_t}, \phi_{S_t}`$: State-dependent parameters | Identifies market regime shifts (e.g., low-vol trend vs high-vol mean-reversion) to dynamically reweight alpha models. |
| **Dynamic Linear Model (DLM)** | $`\mathbf{y}_t = \mathbf{F}_t^\top \boldsymbol{\theta}_t + \mathbf{v}_t, \quad \mathbf{v}_t \sim \mathcal{N}(\mathbf{0}, \mathbf{V}_t)`$<br><br>$`\boldsymbol{\theta}_t = \mathbf{G}_t \boldsymbol{\theta}_{t-1} + \mathbf{w}_t, \quad \mathbf{w}_t \sim \mathcal{N}(\mathbf{0}, \mathbf{W}_t)`$ | • $`\boldsymbol{\theta}_t`$: Time-varying parameter vector<br><br>• $`\mathbf{F}_t, \mathbf{G}_t`$: Measurement & System matrices | Bayesian state-space framework for modeling time-varying asset betas, factor loadings, and macro relationships. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 9. Alpha Research, Signal Evaluation & Performance Metrics

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Information Coefficient (IC) & Rank IC** | $`\text{IC} = \rho(\mathbf{s}, \mathbf{r})`$<br><br>$`\text{Rank IC} = 1 - \frac{6 \sum_{i=1}^N d_i^2}{N(N^2 - 1)}`$ | • $`\mathbf{s}`$: Forecast signal vector<br><br>• $`\mathbf{r}`$: Forward asset return vector<br><br>• $`d_i`$: Rank difference for asset $i$ | Evaluates signal cross-sectional predictive power; Rank IC provides robustness against return outliers. |
| **IC Information Ratio (ICIR)** | $`\text{ICIR} = \frac{\mathbb{E}[\text{IC}]}{\sigma_{\text{IC}}} \cdot \sqrt{N_{\text{periods}}}`$ | • $`\mathbb{E}[\text{IC}]`$: Mean IC; $`\sigma_{\text{IC}}`$: Volatility of IC<br><br>• $`N_{\text{periods}}`$: Annualized rebalance periods | Measures signal forecast consistency over time, standardizing alpha efficacy prior to portfolio construction. |
| **Annualized Sharpe Ratio** | $`\text{SR} = \frac{\mathbb{E}[R_p - R_f]}{\sigma_p} \cdot \sqrt{N_{\text{annual}}}`$ | • $`R_p, R_f`$: Strategy return vs risk-free rate<br><br>• $`\sigma_p`$: Strategy return standard deviation | Standard metric evaluating excess return per unit of total return volatility. |
| **Sortino Ratio** | $`\text{Sortino} = \frac{\mathbb{E}[R_p - R_f]}{\sigma_{\text{down}}} \cdot \sqrt{N_{\text{annual}}}`$<br><br>$`\sigma_{\text{down}} = \sqrt{\frac{1}{T}\sum_{t=1}^T \min(0, R_{p,t} - R_f)^2}`$ | • $`\sigma_{\text{down}}`$: Downside semi-variance | Measures risk-adjusted returns while penalizing only downside volatility. |
| **Calmar Ratio** | $`\text{Calmar} = \frac{\text{CAGR}}{\vert{}\text{Max Drawdown}\vert{}}`$ | • $`\text{CAGR}`$: Annual compound growth rate<br><br>• $`\text{Max Drawdown}`$: Maximum peak-to-trough drop | Measures return generation relative to maximum peak-to-trough drawdowns, popular in CTA/systematic macro strategies. |
| **Compound Annual Growth Rate (CAGR)** | $`\text{CAGR} = \left( \frac{V_{\text{end}}}{V_{\text{start}}} \right)^{\frac{1}{T}} - 1`$ | • $`V_{\text{start}}, V_{\text{end}}`$: Initial and final portfolio value<br><br>• $`T`$: Strategy duration in years | Measures the annualized geometric growth rate of investment capital over time. |
| **Hit Ratio (Win Rate)** | $`\text{Hit Ratio} = \frac{\sum_{k=1}^N \mathbb{I}(R_k > 0)}{N}`$ | • $`\mathbb{I}(\cdot)`$: Indicator function<br><br>• $`N`$: Total trade trades executed | Core trade distribution metric evaluating the proportion of positive-yielding executions. |
| **Portfolio Turnover Rate** | $`\text{Turnover} = \frac{1}{2} \sum_{i=1}^N \vert{}w_{i, t} - w_{i, t^-}\vert{}`$ | • $`w_{i,t}`$: Target weight of asset $`i`$<br><br>• $`w_{i,t^-}`$: Pre-rebalance portfolio weight | Quantifies portfolio rebalancing frequency to calculate expected transaction cost drag. |
| **Strategy Capital Capacity** | $`\text{Capacity} \approx V_{\text{ADV}} \cdot \left( \frac{\alpha_{\text{gross}}}{\gamma_{\text{impact}}} \right)^{\frac{1}{\beta}}`$ | • $`V_{\text{ADV}}`$: Average daily volume<br><br>• $`\alpha_{\text{gross}}`$: Unconstrained gross return edge<br><br>• $`\gamma_{\text{impact}}`$: Market impact scale coefficient | Determines maximum AUM scaling before transaction costs erode strategy alpha edge. |
| **Breakeven Transaction Cost** | $`\text{Cost}_{\text{breakeven}} = \frac{\mathbb{E}[R_{\text{gross}}]}{\text{Turnover Rate}}`$ | • $`\mathbb{E}[R_{\text{gross}}]`$: Annualized gross return | Maximum allowable trading cost per trade before net strategy return drops to zero. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 10. Factor Modeling, Portfolio Construction & Risk Management

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Markowitz Mean-Variance Optimization** | $`\max_{\mathbf{w}} \mathbf{w}^\top \boldsymbol{\mu} - \frac{\gamma}{2} \mathbf{w}^\top \boldsymbol{\Sigma} \mathbf{w}, \quad \text{s.t. } \mathbf{1}^\top \mathbf{w} = 1`$ | • $`\mathbf{w}`$: Asset weight vector<br><br>• $`\boldsymbol{\mu}`$: Expected return vector<br><br>• $`\boldsymbol{\Sigma}`$: Return covariance matrix | Classical framework optimizing trade-offs between portfolio expected return and variance. |
| **Efficient Frontier Solution** | $`\mathbf{w}^* = \lambda \boldsymbol{\Sigma}^{-1} \boldsymbol{\mu} + \gamma \boldsymbol{\Sigma}^{-1} \mathbf{1}`$ | • $`\lambda, \gamma`$: Analytical Lagrange multipliers | Traces the set of optimal portfolios offering the highest expected return for a defined risk level. |
| **Fama-French 5-Factor Model** | $`R_{it} - R_{ft} = \alpha_i + \beta_{i1}\text{MKT}_t + \beta_{i2}\text{SMB}_t + \beta_{i3}\text{HML}_t + \beta_{i4}\text{RMW}_t + \beta_{i5}\text{CMA}_t + \epsilon_{it}`$ | • $`\text{SMB}`$: Size; $`\text{HML}`$: Value<br><br>• $\text{RMW}$: Profitability; $\text{CMA}$: Investment | Isolates systematic factor risk exposures to extract true idiosyncratic manager alpha ($\alpha_i$). |
| **Multi-Signal Strategy Aggregation** | $`\alpha_{\text{composite}} = w_c Z(\text{Carry}) + w_t Z(\text{Trend}) + w_v Z(\text{Value}) + w_m Z(\text{Mom})`$ | • $`Z(\cdot)`$: Standardized Z-score of signal<br><br>• $`w_i`$: Signal combination weights | Combines orthogonal style factors to build systematic multi-asset strategies. |
| **Kelly Criterion (Fractional Kelly)** | $`f^* = c \cdot \frac{\boldsymbol{\mu} - r}{\sigma^2}, \quad c \in (0, 1]`$ | • $`f^*`$: Target leverage fraction<br><br>• $`c`$: Fractional Kelly scaling factor (e.g., $`c = 0.5`$) | Determines optimal leverage allocation to maximize long-term log-capital growth while controlling drawdown risk. |
| **Value at Risk (VaR)** | $`\mathbb{P}\left( L > \text{VaR}_\alpha \right) = 1 - \alpha`$ | • $`L`$: Portfolio loss random variable<br><br>• $`\alpha`$: Confidence level (e.g., 99%) | Standard risk metric estimating maximum expected portfolio loss over a specified time horizon at a given confidence level. |
| **Conditional VaR (CVaR) / Expected Shortfall** | $`\text{CVaR}_\alpha = \mathbb{E}\left[ L \mid L \ge \text{VaR}_\alpha \right] = \frac{1}{1-\alpha} \int_\alpha^1 \text{VaR}_u du`$ | • Coherent risk metric measuring tail loss severity | Measures expected loss in tail risk scenarios exceeding the VaR threshold; accounts for fat-tailed return distributions. |

[🔝 Back to Top](#-table-of-contents)

---
---

### 11. Quantitative Statistics & Hypothesis Testing

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **TCA Slippage Significance Test**<br> | $`t = \frac{\bar{X} - \mu_0}{s / \sqrt{n}} \sim t_{n-1}`$<br> | • $`\bar{X}`$: Sample mean realized slippage<br><br>• $`\mu_0 = 0`$: Null baseline<br><br>• $`s`$: Sample standard deviation; $n$: Order count | Determines whether observed execution slippage reflects structural broker/algo underperformance versus statistical noise. |

[🔝 Back to Top](#-table-of-contents)

---
---