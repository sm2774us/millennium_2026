<div align="center">

# 🧮 QUANT FINANCE IMPORTANT MATHEMATICAL DERIVATIONS

> **Document synopsis.** Important mathematical derivations in the field of **Quantitative Finance**.

</div>

---
---

[↩️ Back to ./README.md](./README.md#-additional-resources)

---
---

## 📋 Table of Contents

### 🧮 FIRST-PRINCIPLES MATHEMATICAL DERIVATIONS

* [Derivation 1: Perold Implementation Shortfall & Attribution Decomposition](#derivation-1-perold-implementation-shortfall--attribution-decomposition)
* [Derivation 2: Kyle's Lambda & Hasbrouck Vector Autoregression Impact Equations](#derivation-2-kyles-lambda--hasbrouck-vector-autoregression-impact-equations)
* [Derivation 3: Continuous-Time Almgren-Chriss Optimal Execution Trajectory (`sinh` derivation)](#derivation-3-continuous-time-almgren-chriss-optimal-execution-trajectory-sinh-derivation)
* [Derivation 4: Discrete-Time Almgren-Chriss Optimal Execution Framework (`sinh` derivation)](#derivation-4-discrete-time-almgren-chriss-optimal-execution-framework-sinh-derivation)
* [Derivation 5: Continuous-Time Almgren-Chriss Cost Function Derivation](#derivation-5-continuous-time-almgren-chriss-cost-function-derivation)
* [Derivation 6: Discrete-Time Almgren-Chriss Cost Function Derivation](#derivation-6-discrete-time-almgren-chriss-cost-function-derivation)
* [Derivation 7: Deflated Sharpe Ratio (DSR) & Multiple Testing Correction](#derivation-7-deflated-sharpe-ratio-dsr--multiple-testing-correction)
* [Derivation 8: Order Flow Imbalance (OFI) & Micro-Price Formulation](#derivation-8-order-flow-imbalance-ofi--micro-price-formulation)
* [Derivation 9: The Square-Root Law of Market Impact](#derivation-9-the-square-root-law-of-market-impact)
* [Derivation 10: TCA Statistical Significance & Hypothesis Testing (CLT / `t`-Test)](#derivation-10-tca-statistical-significance--hypothesis-testing-clt--t-test)
* [Derivation 11: Dynamic Programming & Minimax Solution for the Two Eggs Problem](#derivation-11-dynamic-programming--minimax-solution-for-the-two-eggs-problem)
* [Derivation 12: Negative-Binomial Model for Partial Fill Dynamics](#derivation-12-negative-binomial-model-for-partial-fill-dynamics)
* [Derivation 13: Bayesian Information Updating in the Monty Hall Problem](#derivation-13-bayesian-information-updating-in-the-monty-hall-problem)
* [Derivation 14: First-Step Analysis for Gambler's Ruin & Absorption Probabilities](#derivation-14-first-step-analysis-for-gamblers-ruin--absorption-probabilities)
* [Derivation 15: Central Limit Theorem for Cumulative VWAP Slippage Variance](#derivation-15-central-limit-theorem-for-cumulative-vwap-slippage-variance)
* [Derivation 16: Covariance & Variance Decomposition of Correlated Bernoulli Trials](#derivation-16-covariance--variance-decomposition-of-correlated-bernoulli-trials)
* [Derivation 17: Fermi Estimation Framework for CME Futures Daily Volume](#derivation-17-fermi-estimation-framework-for-cme-futures-daily-volume)
* [Derivation 18: Multi-Period Kelly Criterion & Growth Rate Maximization](#derivation-18-multi-period-kelly-criterion--growth-rate-maximization)

[🔝 Back to Top](#-table-of-contents)

---
---

## 🧮 FIRST-PRINCIPLES MATHEMATICAL DERIVATIONS

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 1: Perold Implementation Shortfall & Attribution Decomposition

#### First-Principles Derivation of Perold Implementation Shortfall & Attribution Decomposition

To understand Implementation Shortfall (IS) from first principles, we must return to Andre Perold’s foundational 1988 framework. Implementation shortfall measures the total performance cost of executing a trade compared to a frictionless, instantaneous decision in the market. It captures explicit costs (commissions, taxes) and implicit costs (market impact, opportunity cost, and delay).

#### 1. The Core Economic Objective: Paper Portfolio vs. Real Portfolio

Imagine a portfolio manager (PM) who makes a **decision** at time $t_d$ to buy $Q$ shares of an asset at decision price $P_d$.

* **The Paper Portfolio (Hypothetical):** If the trade could be executed instantaneously with infinite liquidity at $P_d$, the total cost would simply be $Q \times P_d$.
* **The Real Portfolio (Actual):** Because liquidity is finite and orders take time to route, fill, and clear, the actual execution happens across multiple prices ($P_f$) over time, and some portion of the order may go unfilled by the end of the horizon ($Q - Q_f$), settling at the closing price ($P_c$).

The **Total Implementation Shortfall** is defined as the monetary difference between the value realized by the actual trading process and the hypothetical paper portfolio value.

#### 2. Formulating Total Implementation Shortfall

Let:

* $Q$: Total shares intended to be bought.
* $Q_f$: Total shares actually filled ($Q_f \le Q$).
* $P_d$: **Decision Price** (the mid-price or market price at the exact moment the PM decides to trade).
* $P_f$: **Fill Price** (the actual average execution price for the filled quantity $Q_f$).
* $P_c$: **Closing Price** (or benchmark price at the end of the trading horizon, used to value unexecuted shares).

For a **buy order**, the cost components are structured as follows:

$$
\text{Total IS} = \underbrace{Q_f (P_f - P_d)}_{\text{Cost of filled shares}} + \underbrace{(Q - Q_f)(P_c - P_d)}_{\text{Opportunity cost of unfilled shares}}
$$

* *First Term:* Measures how much worse off we are buying $Q_f$ shares at the execution prices $P_f$ relative to the decision price $P_d$.
* *Second Term:* Measures the opportunity cost of failing to fill $(Q - Q_f)$ shares. If the price rose from $P_d$ to $P_c$, missing out on those shares cost the portfolio $(P_c - P_d)$ per share.

#### 3. Decomposing the Execution Component via Telescoping Sums

To evaluate *why* the execution cost $Q_f (P_f - P_d)$ occurred, Perold introduced a chronological decomposition along the order lifecycle. We introduce intermediate benchmark prices:

1. $P_a$: **Arrival Price** (market price when the order actually arrives at the trading desk or broker).
2. $P_r$: **Order Release / Execution Start Price** (market price when the broker actually routes or starts working the order in the market).
3. $P_f$: **Fill Price** (final execution price).

We can decompose the total price difference $(P_f - P_d)$ using a telescoping sum by adding and subtracting intermediate prices ($P_a$ and $P_r$):

$$
P_f - P_d = (P_a - P_d) + (P_r - P_a) + (P_f - P_r)
$$

#### Economic Meaning of Each Term:

* $$(P_a - P_d)$$

 **Decision-to-Arrival Spread:** Captures the price drift that occurs between the portfolio manager making the decision and the order arriving at the trading desk (often due to internal communication lags or manual sign-offs).

* $$(P_r - P_a)$$

 **Arrival-to-Release Spread:** Captures the delay introduced by the trading desk or algorithmic strategy scheduler before actively releasing the order into the market.

* $$(P_f - P_r)$$

 **Release-to-Fill Spread:** Captures the active execution phase, reflecting market impact (price movement caused by our own order execution) and half-spread crossing costs.

#### 4. Multiplying by Filled Quantity to Obtain Cost Attribution

Multiplying the price decomposition by the filled quantity $Q_f$ breaks down the total execution cost into actionable performance attribution buckets:

$$
\text{Execution Cost} = Q_f (P_f - P_d)
$$

$$
\text{Execution Cost} = \underbrace{Q_f (P_a - P_d)}_{\text{Delay Cost}} + \underbrace{Q_f (P_r - P_a)}_{\text{Routing Cost}} + \underbrace{Q_f (P_f - P_r)}_{\text{Impact + Spread Cost}} \quad \blacksquare
$$

|     |
| :-- |
| $`\text{Execution Cost} = \underbrace{Q_f (P_a - P_d)}_{\text{Delay Cost}} + \underbrace{Q_f (P_r - P_a)}_{\text{Routing Cost}} + \underbrace{Q_f (P_f - P_r)}_{\text{Impact + Spread Cost}}`$ |

#### Final Summary of Attribution Components:

1. **Delay Cost [ $Q_f (P_a - P_d)$ ]:** Measures the cost of waiting to send the order to the market. High delay costs imply internal latency or slow decision-to-desk workflows.
2. **Routing/Scheduler Cost [ $Q_f (P_r - P_a)$ ]:** Measures the cost accrued while the desk holds or stages the order before market entry. High costs here point toward suboptimal order management system (OMS) routing or poor algorithmic scheduling choices.
3. **Impact + Spread Cost [ $Q_f (P_f - P_r)$ ]:** Measures the direct market footprint left by the trader or algorithm executing the shares against the order book. High costs here suggest an aggressive execution style, poor liquidity selection, or high market volatility.

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 2: Kyle's Lambda & Hasbrouck Vector Autoregression Impact Equations

#### First-Principles Mathematical Derivation: Kyle’s Lambda & Structural Optimization

To derive Kyle’s Lambda ($\lambda$) rigorously from first principles, we must solve Robert Kyle’s (1985) continuous-time/single-period sequential auction model as a Bayesian Nash Equilibrium (BNE) between an **informed trader**, **noise traders**, and **competitive risk-neutral market makers**.

#### 1. Model Setup and Information Structure

Consider a single-asset market operating over one trading period.

* **Terminal Asset Value ($v$):** The true liquidation value of the asset is a random variable drawn from a normal prior distribution:

$$
v \sim \mathcal{N}(P_0, \Sigma_0)
$$

where $P_0$ is the initial price (prior mean) and $\Sigma_0 = \text{Var}(v)$ is the prior variance.

* **Informed Trader:** Observes the true liquidation value $v$ perfectly before trading. The informed trader submits an order size $x$.

* **Noise / Liquidity Traders:** Submit random, uninformative net order flow $u$, distributed independently of $v$:

$$
u \sim \mathcal{N}(0, \sigma_u^2)
$$

* **Total Order Flow ($y$):** Market makers only observe the aggregate order flow, not the individual components:

$$
y = x + u
$$

* **Market Makers:** Bertrand-competitive, risk-neutral market makers observe total order flow $y$ and set the clearing price $P_1$ equal to expected value conditional on $y$:

$$
P_1 = \mathbb{E}[v \mid y]
$$

#### 2. The Informed Trader's Optimization Problem

The informed trader chooses order size $x$ to maximize expected terminal wealth, taking the market maker's pricing rule as given. Assume the market maker uses a linear pricing rule of the general form:

$$
P_1(y) = \mu + \lambda y
$$

where $\mu$ is the intercept and $\lambda$ is Kyle’s lambda (price impact coefficient).

The informed trader's objective function is:

$$
\max_{x} \mathbb{E}\left[ (v - P_1(x + u)) x \mid v \right]
$$

Substitute the linear pricing rule:

$$
\max_{x} \mathbb{E}\left[ (v - (\mu + \lambda(x + u))) x \mid v \right]
$$

Since $\mathbb{E}[u \mid v] = 0$ (noise trading is independent of the asset value):

$$
\max_{x} x (v - \mu - \lambda x)
$$

To find the optimal order size $x^{\*}(v)$, take the first-order condition (FOC) with respect to $x$:

$$
\frac{\partial}{\partial x} \left[ x v - x \mu - \lambda x^2 \right] = v - \mu - 2\lambda x = 0
$$

Solving for $x$:

$$
x^{\*}(v) = \frac{v - \mu}{2\lambda}
$$

#### 3. Market Maker Pricing Rule & Bayesian Updating

Market makers set prices competitively such that expected economic profits are zero, yielding:

$$
P_1(y) = \mathbb{E}[v \mid y]
$$

Because $v$ and $y$ are jointly normally distributed, we apply linear projection (multivariate Gaussian conditional expectation / updating):

$$
\mathbb{E}[v \mid y] = \mathbb{E}[v] + \frac{\text{Cov}(v, y)}{\text{Var}(y)} \left( y - \mathbb{E}[y] \right)
$$

Let us calculate each component explicitly:

1. **Prior Expectation of $v$:**

$$
\mathbb{E}[v] = P_0
$$

2. **Expected Order Flow $\mathbb{E}[y]$:**

Since $x^{\*}(v) = \frac{v - \mu}{2\lambda}$ and $\mathbb{E}[v] = P_0$:

$$
\mathbb{E}[y] = \mathbb{E}[x^{\*}(v)] + \mathbb{E}[u] = \frac{P_0 - \mu}{2\lambda} + 0 = \frac{P_0 - \mu}{2\lambda}
$$

3. **Covariance between $v$ and $y$:**

$$
\text{Cov}(v, y) = \text{Cov}(v, x^{\*}(v) + u) = \text{Cov}\left(v, \frac{v - \mu}{2\lambda}\right) + 0
$$

$$
\text{Cov}(v, y) = \frac{1}{2\lambda} \text{Var}(v) = \frac{\Sigma_0}{2\lambda}
$$

4. **Variance of Order Flow $\text{Var}(y)$:**

$$
\text{Var}(y) = \text{Var}(x^{\*}(v)) + \text{Var}(u) = \text{Var}\left(\frac{v - \mu}{2\lambda}\right) + \sigma_u^2
$$

$$
\text{Var}(y) = \frac{\text{Var}(v)}{4\lambda^2} + \sigma_u^2 = \frac{\Sigma_0}{4\lambda^2} + \sigma_u^2
$$

Substituting these back into the conditional expectation equation:

$$
P_1(y) = P_0 + \frac{\frac{\Sigma_0}{2\lambda}}{\frac{\Sigma_0}{4\lambda^2} + \sigma_u^2} \left( y - \frac{P_0 - \mu}{2\lambda} \right)
$$

#### 4. Equilibrium Fixed-Point Solution

For the market maker's pricing rule $P_1(y) = \mu + \lambda y$ to be internally consistent with the linear projection derived above, the coefficients must match across states.

Equating the intercept $\mu$ and slope $\lambda$:

* **Intercept matching:** $\mu = P_0 - \frac{\text{Cov}(v,y)}{\text{Var}(y)} \frac{P_0 - \mu}{2\lambda}$

In equilibrium, centering around priors gives $\mu = P_0$. Thus, the intercept collapses to $P_0$.

* **Slope matching ($\lambda$):**

$$
\lambda = \frac{\frac{\Sigma_0}{2\lambda}}{\frac{\Sigma_0}{4\lambda^2} + \sigma_u^2}
$$

Multiply both sides by the denominator:

$$
\lambda \left( \frac{\Sigma_0}{4\lambda^2} + \sigma_u^2 \right) = \frac{\Sigma_0}{2\lambda}
$$

$$
\frac{\Sigma_0}{4\lambda} + \lambda \sigma_u^2 = \frac{\Sigma_0}{2\lambda}
$$

Subtract $\frac{\Sigma_0}{4\lambda}$ from both sides:

$$
\lambda \sigma_u^2 = \frac{\Sigma_0}{2\lambda} - \frac{\Sigma_0}{4\lambda} = \frac{\Sigma_0}{4\lambda}
$$

Multiply both sides by $4\lambda$:

$$
4 \lambda^2 \sigma_u^2 = \Sigma_0
$$

$$
\lambda^2 = \frac{\Sigma_0}{4 \sigma_u^2}
$$

Taking the positive root (since price impact must be non-negative):

$$
\lambda_{\text{Kyle}} = \frac{\sqrt{\Sigma_0}}{2 \sigma_u}
$$

Noting that $\sigma_v = \sqrt{\Sigma_0}$ (the standard deviation of the asset value), we arrive at the standard formulation:

$$
\lambda_{\text{Kyle}} = \frac{\sigma_v}{2 \sigma_u} \quad \blacksquare
$$

#### 5. Economic Interpretation of Kyle’s Lambda

$$
\lambda = \frac{\sigma_v}{2 \sigma_u}
$$

|     |
| :-- |
| $`\lambda = \frac{\sigma_v}{2 \sigma_u}`$ |

* **Numerator ($\sigma_v$):** Higher fundamental uncertainty (information asymmetry) makes adverse selection more severe. Market makers must protect themselves by raising price impact per unit of volume.
* **Denominator ($\sigma_u$):** Higher noise trading volume provides "camouflage" for the informed trader. As noise variance increases, order flow becomes less informative, causing market makers to reduce price impact ($\lambda$ decreases).

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 3: Continuous-Time Almgren-Chriss Optimal Execution Trajectory (`sinh` derivation)

#### First-Principles Mathematical Derivation: Continuous-Time Almgren-Chriss Optimal Execution Trajectory

To derive the continuous-time optimal liquidation trajectory from first principles, we formulate Robert Almgren and Neil Chriss’s (2000) framework as a **calculus of variations** (or optimal control) problem. The goal is to balance the reduction of temporary/permanent market impact against inventory risk over a fixed horizon $[0, T]$.

#### 1. Problem Formulation & Objective Functional

Let:

* $X_0$: Initial inventory held by the trader at time $t = 0$.
* $x(t)$: Inventory remaining at time $t$, with boundary conditions $x(0) = X_0$ and $x(T) = 0$ (fully liquidated by horizon $T$).
* $\dot{x}(t) = \frac{dx}{dt}$: Trading speed (rate of liquidation, where selling implies $\dot{x}(t) \le 0$).
* $\eta$: Temporary market impact parameter (linear cost coefficient associated with trading speed).
* $\lambda$: Risk aversion parameter of the mean-variance utility framework.
* $\sigma^2$: Volatility of the underlying asset.

The trader seeks to minimize the expected cost plus a penalty for price risk (variance of the execution price path). Integrating the continuous-time cost functional over the trading interval $[0, T]$ yields the objective function:

$$
\min_{x(t)} J[x(t)] = \int_0^T \left( \eta \dot{x}(t)^2 + \lambda \sigma^2 x(t)^2 \right) dt
$$

* **First Term ($\eta \dot{x}^2$):** Temporary price impact cost rate. Executing faster ($\dot{x}$ is large in magnitude) incurs quadratic penalties.
* **Second Term ($\lambda \sigma^2 x^2$):** Holding cost / inventory risk penalty. Keeping large inventory positions exposed to market volatility ($\sigma^2$) over time accumulates variance risk.

#### 2. Calculus of Variations: The Euler-Lagrange Equation

Let the integrand be defined as the Lagrangian function $L(x, \dot{x}, t)$:

$$
L(x, \dot{x}, t) = \eta \dot{x}(t)^2 + \lambda \sigma^2 x(t)^2
$$

To find the trajectory $x(t)$ that extremizes (minimizes) the functional $J[x(t)]$, we apply the classical **Euler-Lagrange equation**:

$$
\frac{\partial L}{\partial x} - \frac{d}{dt}\left(\frac{\partial L}{\partial \dot{x}}\right) = 0
$$

Computing the required partial derivatives:

1. Partial derivative with respect to inventory $x$:

$$
\frac{\partial L}{\partial x} = \frac{\partial}{\partial x} \left( \eta \dot{x}^2 + \lambda \sigma^2 x^2 \right) = 2 \lambda \sigma^2 x
$$

2. Partial derivative with respect to trading velocity $\dot{x}$:

$$
\frac{\partial L}{\partial \dot{x}} = \frac{\partial}{\partial \dot{x}} \left( \eta \dot{x}^2 + \lambda \sigma^2 x^2 \right) = 2 \eta \dot{x}
$$

3. Time derivative of the velocity partial:

$$
\frac{d}{dt}\left(\frac{\partial L}{\partial \dot{x}}\right) = \frac{d}{dt} (2 \eta \dot{x}) = 2 \eta \ddot{x}
$$

Substituting these back into the Euler-Lagrange equation:

$$
2 \lambda \sigma^2 x(t) - 2 \eta \ddot{x}(t) = 0
$$

Dividing through by $2\eta$ gives the homogeneous second-order linear ordinary differential equation (ODE):

$$
\ddot{x}(t) - \frac{\lambda \sigma^2}{\eta} x(t) = 0
$$

#### 3. Solving the Second-Order Ordinary Differential Equation

Let us define the constant parameter $\kappa^2$ representing the trade-off between inventory risk ($\lambda \sigma^2$) and execution cost ($\eta$):

$$
\kappa^2 = \frac{\lambda \sigma^2}{\eta} \implies \kappa = \sqrt{\frac{\lambda \sigma^2}{\eta}}
$$

The differential equation simplifies to:

$$
\ddot{x}(t) - \kappa^2 x(t) = 0
$$

The auxiliary characteristic equation for this linear ODE is:

$$
r^2 - \kappa^2 = 0 \implies r = \pm \kappa
$$

Because the roots are real and distinct ($\pm \kappa$), the general solution is expressed in terms of hyperbolic functions ($\cosh$ and $\sinh$):

$$
x(t) = A \cosh(\kappa t) + B \sinh(\kappa t)
$$

where $A$ and $B$ are integration constants determined by the boundary conditions.

#### 4. Applying Boundary Conditions

We impose the two boundary constraints of the liquidation problem:

1. **Terminal condition:** $x(T) = 0$ (complete liquidation at final time $T$).
2. **Initial condition:** $x(0) = X_0$ (starting inventory at time $0$).

#### Step A: Applying the Terminal Condition $x(T) = 0$

Substitute $t = T$ into the general solution:

$$
x(T) = A \cosh(\kappa T) + B \sinh(\kappa T) = 0
$$

Solve for constant $B$ in terms of $A$:

$$
B = -A \frac{\cosh(\kappa T)}{\sinh(\kappa T)}
$$

Substitute $B$ back into the general solution for $x(t)$:

$$
x(t) = A \cosh(\kappa t) + \left( -A \frac{\cosh(\kappa T)}{\sinh(\kappa T)} \right) \sinh(\kappa t)
$$

Factor out constant $A$:

$$
x(t) = A \left( \cosh(\kappa t) - \frac{\cosh(\kappa T)}{\sinh(\kappa T)} \sinh(\kappa t) \right)
$$

Find a common denominator ( $\sinh(\kappa T)$ ):

$$
x(t) = A \left( \frac{\sinh(\kappa T) \cosh(\kappa t) - \cosh(\kappa T) \sinh(\kappa t)}{\sinh(\kappa T)} \right)
$$

Using the hyperbolic subtraction trigonometric identity $\sinh(\alpha - \beta) = \sinh(\alpha)\cosh(\beta) - \cosh(\alpha)\sinh(\beta)$, where $\alpha = \kappa T$ and $\beta = \kappa t$:

$$
x(t) = A \frac{\sinh(\kappa(T - t))}{\sinh(\kappa T)}
$$

#### Step B: Applying the Initial Condition $x(0) = X_0$

Substitute $t = 0$ into the equation:

$$
x(0) = A \frac{\sinh(\kappa(T - 0))}{\sinh(\kappa T)} = A \frac{\sinh(\kappa T)}{\sinh(\kappa T)} = A
$$

Since $x(0) = X_0$, it follows directly that:

$$A = X_0$$

#### Final Closed-Form Trajectory Equation:

Substituting $A$ back into the solution yields the optimal Almgren-Chriss liquidation trajectory:

$$
x(t) = X_0 \frac{\sinh(\kappa(T - t))}{\sinh(\kappa T)} \quad \blacksquare
$$

|     |
| :-- |
| $`x(t) = X_0 \frac{\sinh(\kappa(T - t))}{\sinh(\kappa T)}`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 4: Discrete-Time Almgren-Chriss Optimal Execution Framework (`sinh` derivation)

#### First-Principles Mathematical Derivation: Discrete-Time Almgren-Chriss Optimal Execution

While continuous-time executions utilize continuous calculus of variations, quantitative execution systems operate over discrete decision intervals $t_k = k \tau$ for $k = 0, 1, \dots, N$, where $\tau = \frac{T}{N}$.

#### 1. Discrete Model Setup

Let:

* $X_0$: Total shares to liquidate.
* $x_k = x(t_k)$: Unexecuted inventory remaining at time $t_k$, with boundary conditions $x_0 = X_0$ and $x_N = 0$.
* $n_k = x_{k-1} - x_k$: Shares sold in interval $(t_{k-1}, t_k]$.
* $v_k = \frac{n_k}{\tau}$: Execution speed during step $k$.

The asset price follows the discrete stochastic process:

$$S_k = S_{k-1} - \tau \gamma v_k + \sigma \tau^{1/2} \xi_k$$

where:

* $\gamma$: Permanent market impact parameter.
* $\sigma$: Asset volatility.
* $\xi_k \sim \mathcal{N}(0, 1)$ i.i.d. random variables.

The actual capture price $\tilde{S}_k$ incorporating temporary market impact parameter $\eta$ (with linear impact $\eta v_k = \frac{\eta n_k}{\tau}$) is:

$$\tilde{S}_k = S_{k-1} - \frac{1}{2}\tau \gamma v_k - \frac{\eta}{\tau} n_k$$

#### 2. Total Execution Cost and Mean-Variance Utility

Total revenue $R = \sum_{k=1}^N n_k \tilde{S}_k$. The expected total shortfall $\mathbb{E}[x]$ relative to initial value $X_0 S_0$ is:

$$\mathbb{E}[E] = X_0 S_0 - \mathbb{E}[R] = \frac{1}{2} \gamma X_0^2 + \tilde{\eta} \sum_{k=1}^N n_k^2$$

where $\tilde{\eta} = \frac{\eta}{\tau} - \frac{1}{2}\gamma$.

The variance of total execution cost $\mathbb{V}[E]$ arises purely from unexecuted inventory risk exposure over time:

$$\mathbb{V}[E] = \sigma^2 \tau \sum_{k=1}^N x_k^2$$

We form the mean-variance objective functional with risk-aversion coefficient $\lambda$:

$$U(x_1, \dots, x_{N-1}) = \mathbb{E}[E] + \lambda \mathbb{V}[E] = \tilde{\eta} \sum_{k=1}^N (x_{k-1} - x_k)^2 + \lambda \sigma^2 \tau \sum_{k=1}^N x_k^2$$

#### 3. Optimization via Unconstrained Partial Derivatives

To minimize $U$, set partial derivatives $\frac{\partial U}{\partial x_j} = 0$ for each interior time step $j = 1, \dots, N-1$:

$$\frac{\partial U}{\partial x_j} = \tilde{\eta} \left[ 2(x_j - x_{j-1}) - 2(x_{j+1} - x_j) \right] + 2 \lambda \sigma^2 \tau x_j = 0$$

Dividing by $2 \tilde{\eta}$:

$$-x_{j-1} + 2 x_j - x_{j+1} + \frac{\lambda \sigma^2 \tau}{\tilde{\eta}} x_j = 0$$

Rearranging into a second-order linear difference equation:

$$x_{j+1} - \left(2 + \kappa^2 \tau^2\right) x_j + x_{j-1} = 0$$

where $\kappa^2 = \frac{\lambda \sigma^2}{\tilde{\eta} \tau} \approx \frac{\lambda \sigma^2}{\eta}$.

#### 4. Solving the Difference Equation

Substitute trial solution $x_j = r^j$:

$$r^2 - (2 + \kappa^2 \tau^2) r + 1 = 0$$

The characteristic roots $r_1, r_2$ satisfy $r_1 r_2 = 1$. Let $r_1 = e^{\kappa \tau + \mathcal{O}(\tau^2)}$ and $r_2 = e^{-\kappa \tau + \mathcal{O}(\tau^2)}$.

General solution imposing boundary conditions $x_0 = X_0$ and $x_N = 0$:

$$x_j = X_0 \frac{\sinh(\kappa (T - t_j))}{\sinh(\kappa T)} \quad \blacksquare$$

|  |
| --- |
| $`x_j = X_0 \frac{\sinh(\kappa (T - t_j))}{\sinh(\kappa T)}`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 5: Continuous-Time Almgren-Chriss Cost Function Derivation

#### First-Principles Mathematical Derivation: Objective Functional & Cost Structure

To mathematically derive the total cost functional in the Almgren-Chriss (2000) framework, we construct the total implementation shortfall from continuous-time asset price dynamics, account for market impact costs, and evaluate the mean-variance trade-off using Itô calculus.

---

#### 1. Price Process & Execution Dynamics Setup

Let:

* $X$: Initial total inventory held at time $t = 0$.
* $x(t)$: Unexecuted inventory remaining at time $t \in [0, T]$, with boundary conditions $x(0) = X$ and $x(T) = 0$.
* $\dot{x}(t) = \frac{dx}{dt}$: Rate of change of inventory. Since shares are being sold over time, $\dot{x}(t) \le 0$. The trading velocity is $v(t) = -\dot{x}(t) \ge 0$.
* $S_0$: Unaffected asset price at $t = 0$.

The asset price $S(t)$ evolves according to a continuous stochastic process driven by permanent market impact and standard volatility:

$$dS(t) = -\gamma v(t) dt + \sigma dW(t) = \gamma \dot{x}(t) dt + \sigma dW(t)$$

where:

* $\gamma$: Permanent market impact parameter.
* $\sigma$: Absolute asset price volatility per unit time.
* $W(t)$: Standard 1D Brownian motion (Wiener process).

Integrating $dS(t)$ from $0$ to $t$ gives the unaffected price path affected by permanent impact:

$$S(t) = S_0 + \gamma \int_0^t \dot{x}(s) ds + \sigma W(t) = S_0 + \gamma (x(t) - X) + \sigma W(t)$$

The actual price captured per share executed at time $t$, denoted as $\tilde{S}(t)$, incorporates temporary market impact proportional to instantaneous trading speed $v(t) = -\dot{x}(t)$:

$$\tilde{S}(t) = S(t) - \eta v(t) = S(t) + \eta \dot{x}(t)$$

where $\eta$ is the temporary market impact parameter.

---

#### 2. Revenue & Implementation Shortfall Formulation

Total cash revenue $R$ realized from selling the entire block over time horizon $[0, T]$ is the integral of instantaneous sales rate $v(t) = -\dot{x}(t)$ multiplied by the capture price $\tilde{S}(t)$:

$$R = \int_0^T v(t) \tilde{S}(t) dt = -\int_0^T \dot{x}(t) \tilde{S}(t) dt$$

Substituting the expression for capture price $\tilde{S}(t) = S(t) + \eta \dot{x}(t)$:

$$R = -\int_0^T \dot{x}(t) \left[ S(t) + \eta \dot{x}(t) \right] dt = -\int_0^T S(t) \dot{x}(t) dt - \int_0^T \eta \dot{x}(t)^2 dt$$

---

#### 3. Integration by Parts on the Asset Price Term

We expand the asset price integral $-\int_0^T S(t) \dot{x}(t) dt$ using integration by parts ($\int u \, dv = uv - \int v \, du$), setting $u = S(t)$ and $dv = \dot{x}(t) dt$:

$$-\int_0^T S(t) \dot{x}(t) dt = - \Big[ x(t) S(t) \Big]_0^T + \int_0^T x(t) dS(t)$$

Evaluating the boundary term using $x(0) = X$, $x(T) = 0$, and $S(0) = S_0$:

$$- \Big[ x(T) S(T) - x(0) S(0) \Big] = - \Big[ 0 - X S_0 \Big] = X S_0$$

Substituting $dS(t) = \gamma \dot{x}(t) dt + \sigma dW(t)$ into the remaining integral:

$$-\int_0^T S(t) \dot{x}(t) dt = X S_0 + \gamma \int_0^T x(t) \dot{x}(t) dt + \sigma \int_0^T x(t) dW(t)$$

Evaluating the deterministic calculus integral $\int_0^T x(t) \dot{x}(t) dt$:

$$\int_0^T x(t) \dot{x}(t) dt = \left[ \frac{1}{2} x(t)^2 \right]_0^T = \frac{1}{2} x(T)^2 - \frac{1}{2} x(0)^2 = 0 - \frac{1}{2} X^2 = -\frac{1}{2} X^2$$

Substituting this result back into the expression yields:

$$-\int_0^T S(t) \dot{x}(t) dt = X S_0 - \frac{1}{2} \gamma X^2 + \sigma \int_0^T x(t) dW(t)$$

Thus, total realized revenue $R$ equals:

$$R = X S_0 - \frac{1}{2} \gamma X^2 - \int_0^T \eta \dot{x}(t)^2 dt + \sigma \int_0^T x(t) dW(t)$$

Implementation Shortfall $E$ (total execution cost relative to the initial market value $X S_0$) is defined as:

$$E = X S_0 - R$$

$$E = \frac{1}{2} \gamma X^2 + \int_0^T \eta \dot{x}(t)^2 dt - \sigma \int_0^T x(t) dW(t)$$

---

#### 4. Expected Execution Cost $\mathbb{E}[E]$

We take the expectation of $E$. Because $x(t)$ is a deterministic trading path and $W(t)$ is a standard Wiener process, the stochastic Itô integral has an expectation of zero:

$$\mathbb{E}\left[ \int_0^T x(t) dW(t) \right] = 0$$

Therefore, the expected shortfall $\mathbb{E}[E]$ decomposes into permanent and temporary impact costs:

$$\mathbb{E}[E] = \int_0^T \eta \dot{x}(t)^2 dt + \frac{1}{2} \gamma X^2$$

* **Temporary Impact Cost:** $\int_0^T \eta \dot{x}(t)^2 dt$
* **Permanent Impact Cost:** $\frac{1}{2} \gamma X^2$

---

#### 5. Variance of Execution Cost $\mathbb{V}[E]$

The variance of total execution cost $\mathbb{V}[E]$ arises strictly from unexecuted inventory exposure to asset price volatility over time. Taking the variance of $E$:

$$\mathbb{V}[E] = \mathbb{V}\left[ \frac{1}{2} \gamma X^2 + \int_0^T \eta \dot{x}(t)^2 dt - \sigma \int_0^T x(t) dW(t) \right]$$

Since the impact terms are deterministic, they contribute zero variance. Applying the **Itô Isometry**:

$$\mathbb{V}[E] = \mathbb{V}\left[ -\sigma \int_0^T x(t) dW(t) \right] = \sigma^2 \mathbb{E}\left[ \left( \int_0^T x(t) dW(t) \right)^2 \right] = \sigma^2 \int_0^T x(t)^2 dt$$

---

#### 6. Mean-Variance Objective Functional Synthesis

Following Almgren and Chriss, the optimal strategy minimizes expected shortfall penalized by the variance of shortfall scaled by risk-aversion coefficient $\lambda$:

$$\text{Total Cost} = \mathbb{E}[E] + \lambda \mathbb{V}[E]$$

Substituting expected shortfall $\mathbb{E}[E]$ and variance $\mathbb{V}[E]$:

$$\text{Total Cost} = \int_0^T \eta \dot{x}(t)^2 dt + \frac{1}{2}\gamma X^2 + \lambda \int_0^T \sigma^2 x(t)^2 dt \quad \blacksquare$$

|  |
| --- |
| $`\text{Total Cost} = \underbrace{\int_0^T \Big[\eta \dot{x}(t)^2 \Big] dt}_{\text{Term 1: Temporary Impact}} + \underbrace{\frac{1}{2}\gamma X^2}_{\text{Term 2: Permanent Impact}} + \underbrace{\lambda \int_0^T \sigma^2 x(t)^2 dt}_{\text{Term 3: Risk Penalty}}`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 6: Discrete-Time Almgren-Chriss Cost Function Derivation

#### First-Principles Mathematical Derivation: Discrete Objective Functional & Cost Structure

To derive the discrete-time Almgren-Chriss cost functional from first principles, we divide the trading horizon $[0, T]$ into $N$ equal time steps of duration $\tau = \frac{T}{N}$, where $t_k = k \tau$ for $k = 0, 1, \dots, N$. We then compute total implementation shortfall and apply discrete stochastic summation by parts.

---

#### 1. Discrete Model Setup & Price Dynamics

Let:

* $X$: Total initial shares to liquidate at $t_0 = 0$.
* $x_k = x(t_k)$: Unexecuted inventory remaining at step $k$, with boundary conditions $x_0 = X$ and $x_N = 0$.
* $n_k = x_{k-1} - x_k$: Number of shares sold during the $k$-th time interval $(t_{k-1}, t_k]$. Note that $\sum_{k=1}^N n_k = X$.
* $v_k = \frac{n_k}{\tau}$: Discrete trading speed in interval $k$.
* $S_0$: Unaffected asset price at $t = 0$.

The unaffected asset price $S_k$ at time $t_k$ evolves as a discrete random walk with permanent market impact:

$$S_k = S_{k-1} - \gamma n_k + \sigma \tau^{1/2} \xi_k$$

where:

* $\gamma$: Permanent impact parameter per share executed.
* $\sigma$: Asset price volatility rate.
* $\xi_k \sim \mathcal{N}(0, 1)$ i.i.d. standard normal random variables for $k = 1, \dots, N$.

By unrolling the recursion for $S_{k-1}$, the price at the beginning of interval $k$ is:

$$S_{k-1} = S_0 - \gamma \sum_{j=1}^{k-1} n_j + \sigma \tau^{1/2} \sum_{j=1}^{k-1} \xi_j$$

The actual captured price $\tilde{S}_k$ received for the block $n_k$ executed during $(t_{k-1}, t_k]$ incorporates both temporary market impact ($\frac{\eta}{\tau} n_k = \eta v_k$) and half of the permanent market impact incurred during that interval:

$$\tilde{S}_k = S_{k-1} - \frac{1}{2}\gamma n_k - \frac{\eta}{\tau} n_k$$

---

#### 2. Total Revenue & Implementation Shortfall Formulation

The total revenue $R$ realized across all $N$ execution intervals is:

$$R = \sum_{k=1}^N n_k \tilde{S}_k = \sum_{k=1}^N n_k \left( S_{k-1} - \frac{1}{2}\gamma n_k - \frac{\eta}{\tau} n_k \right)$$

Substituting the unrolled expression for $S_{k-1}$:

$$R = \sum_{k=1}^N n_k \left( S_0 - \gamma \sum_{j=1}^{k-1} n_j + \sigma \tau^{1/2} \sum_{j=1}^{k-1} \xi_j - \frac{1}{2}\gamma n_k - \frac{\eta}{\tau} n_k \right)$$

Expanding $R$ term-by-term yields four distinct components:

$$R = \underbrace{S_0 \sum_{k=1}^N n_k}_{\text{Initial Value}} - \underbrace{\gamma \sum_{k=1}^N n_k \left( \sum_{j=1}^{k-1} n_j + \frac{1}{2} n_k \right)}_{\text{Permanent Impact Term}} - \underbrace{\frac{\eta}{\tau} \sum_{k=1}^N n_k^2}_{\text{Temporary Impact Term}} + \underbrace{\sigma \tau^{1/2} \sum_{k=1}^N n_k \sum_{j=1}^{k-1} \xi_j}_{\text{Stochastic Volatility Term}}$$

---

#### 3. Simplification via Discrete Algebraic Identities

#### A. Initial Value Term

Since total shares sold must equal $X$:

$$S_0 \sum_{k=1}^N n_k = X S_0$$

#### B. Permanent Impact Summation Identity

Consider the square of total executed inventory $X^2 = \left( \sum_{k=1}^N n_k \right)^2$:

$$X^2 = \left( \sum_{k=1}^N n_k \right) \left( \sum_{j=1}^N n_j \right) = \sum_{k=1}^N n_k^2 + 2 \sum_{k=1}^N n_k \sum_{j=1}^{k-1} n_j$$

Rearranging this identity gives:

$$\sum_{k=1}^N n_k \sum_{j=1}^{k-1} n_j = \frac{1}{2} X^2 - \frac{1}{2} \sum_{k=1}^N n_k^2$$

Substituting this into the permanent impact term:

$$\sum_{k=1}^N n_k \left( \sum_{j=1}^{k-1} n_j + \frac{1}{2} n_k \right) = \left( \frac{1}{2} X^2 - \frac{1}{2} \sum_{k=1}^N n_k^2 \right) + \frac{1}{2} \sum_{k=1}^N n_k^2 = \frac{1}{2} X^2$$

The trade-size-dependent terms cancel out completely, proving that discrete permanent impact cost depends **only** on total position size $X$, not on individual trade sizes $n_k$.

#### C. Stochastic Term via Discrete Integration by Parts

We exchange the order of summation for the noise term:

$$\sum_{k=1}^N n_k \sum_{j=1}^{k-1} \xi_j = \sum_{j=1}^{N-1} \xi_j \left( \sum_{k=j+1}^N n_k \right)$$

Notice that $\sum_{k=j+1}^N n_k$ is precisely the unexecuted inventory $x_j$ remaining at time step $t_j$. Thus:

$$\sum_{k=1}^N n_k \sum_{j=1}^{k-1} \xi_j = \sum_{j=1}^{N-1} x_j \xi_j$$

---

#### 4. Assembling Realized Shortfall $E$

Substituting these simplified algebraic terms back into the revenue equation $R$:

$$R = X S_0 - \frac{1}{2} \gamma X^2 - \frac{\eta}{\tau} \sum_{k=1}^N n_k^2 + \sigma \tau^{1/2} \sum_{j=1}^{N-1} x_j \xi_j$$

The discrete Implementation Shortfall $E = X S_0 - R$ becomes:

$$E = \frac{1}{2} \gamma X^2 + \frac{\eta}{\tau} \sum_{k=1}^N n_k^2 - \sigma \tau^{1/2} \sum_{j=1}^{N-1} x_j \xi_j$$

---

#### 5. Discrete Expected Cost $\mathbb{E}[E]$ and Variance $\mathbb{V}[E]$

#### Expected Shortfall $\mathbb{E}[E]$:

Since $\xi_j \sim \mathcal{N}(0, 1)$, $\mathbb{E}[\xi_j] = 0$:

$$\mathbb{E}[E] = \frac{1}{2} \gamma X^2 + \frac{\eta}{\tau} \sum_{k=1}^N n_k^2$$

Defining $\tilde{\eta} = \frac{\eta}{\tau}$ (or $\tilde{\eta} = \frac{\eta}{\tau} - \frac{1}{2}\gamma$ under the alternative formulation where temporary impact includes the parameter shift):

$$\mathbb{E}[E] = \tilde{\eta} \sum_{k=1}^N n_k^2 + \frac{1}{2} \gamma X^2$$

#### Variance of Shortfall $\mathbb{V}[E]$:

Since $\xi_j$ are i.i.d. with $\mathbb{V}[\xi_j] = 1$ and $\text{Cov}(\xi_i, \xi_j) = 0$ for $i \neq j$:

$$\mathbb{V}[E] = \mathbb{V} \left[ -\sigma \tau^{1/2} \sum_{j=1}^{N-1} x_j \xi_j \right] = \sigma^2 \tau \sum_{j=1}^{N-1} x_j^2 \mathbb{V}[\xi_j] = \sigma^2 \tau \sum_{j=1}^{N-1} x_j^2$$

Since $x_N = 0$, we extend the index to $N$ without changing the sum:

$$\mathbb{V}[E] = \sigma^2 \tau \sum_{k=1}^N x_k^2$$

---

#### 6. Final Discrete Objective Functional

Combining expected cost and variance risk penalty under trader risk aversion $\lambda$:

$$U(x_1, \dots, x_{N-1}) = \mathbb{E}[E] + \lambda \mathbb{V}[E]$$

$$U(x_1, \dots, x_{N-1}) = \tilde{\eta} \sum_{k=1}^N n_k^2 + \frac{1}{2}\gamma X^2 + \lambda \sigma^2 \tau \sum_{k=1}^N x_k^2 \quad \blacksquare$$

|  |
| --- |
| $`U(x) = \underbrace{\tilde{\eta} \sum_{k=1}^N (x_{k-1} - x_k)^2}_{\text{Term 1: Temporary Impact}} + \underbrace{\frac{1}{2}\gamma X^2}_{\text{Term 2: Permanent Impact}} + \underbrace{\lambda \sigma^2 \tau \sum_{k=1}^N x_k^2}_{\text{Term 3: Risk Penalty}}`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 7: Deflated Sharpe Ratio (DSR) & Multiple Testing Correction

#### First-Principles Mathematical Derivation: Bailey & López de Prado's Deflated Sharpe Ratio (DSR)

To derive the Deflated Sharpe Ratio (DSR) from first principles, we must unify two quantitative finance frameworks: **higher-moment asymptotic variance of the Sharpe ratio** under non-normal returns (Lo, 2002; Opdyke, 2007) and **Extreme Value Theory (EVT)** for the maximum order statistics of correlated trials (Bailey & López de Prado, 2014).

The goal of DSR is to correct the Sharpe Ratio for **selection bias (backtest overfitting)** and **non-normality (skewness and kurtosis)**, answering the fundamental question: *What is the probability that a reported Sharpe ratio is truly greater than zero after accounting for the number of trials tried and the higher moments of the return distribution?*

#### 1. Asymptotic Variance of the Sharpe Ratio Under Non-Normality

Let daily or periodic excess returns of a strategy be denoted by $r_t$ for $t = 1, \dots, T$.

* Sample mean: $\hat{\mu} = \frac{1}{T} \sum_{t=1}^T r_t$
* Sample standard deviation: $\hat{\sigma} = \sqrt{\frac{1}{T-1} \sum_{t=1}^T (r_t - \hat{\mu})^2}$
* Sample Sharpe Ratio: $\widehat{SR} = \frac{\hat{\mu}}{\hat{\sigma}}$

In practice, financial asset returns exhibit significant **skewness** ($\gamma_3$) and **excess kurtosis** ($\gamma_4$). Opdyke (2007) showed that assuming normality ($\gamma_3 = 0, \gamma_4 = 3$) severely understates the variance of the estimated Sharpe ratio when fat tails are present.

Using the Delta Method ( second-order Taylor expansion around the population mean vector $(\mu, \sigma)$ ), the asymptotic variance of $\widehat{SR}$ for sample size $T$ is given by:

$$\text{Var}[\widehat{SR}] \approx \frac{1}{T} \left( 1 - \gamma_3 \widehat{SR} + \frac{\gamma_4 - 1}{4} \widehat{SR}^2 \right)$$

For finite-sample adjustments (replacing $T$ with degrees of freedom $T-1$), this scales to:

$$\text{Var}[\widehat{SR}] \approx \frac{1}{T-1} \left( 1 - \gamma_3 \widehat{SR} + \frac{\gamma_4 - 1}{4} \widehat{SR}^2 \right)$$

* $\gamma_3 = \frac{1}{T} \sum \left(\frac{r_t - \hat{\mu}}{\hat{\sigma}}\right)^3$ (Sample skewness)
* $\gamma_4 = \frac{1}{T} \sum \left(\frac{r_t - \hat{\mu}}{\hat{\sigma}}\right)^4$ (Sample kurtosis)

#### 2. Maximum Order Statistics & Extreme Value Theory (Gumbel Limit)

When a researcher tests $N$ independent strategy configurations (or backtest variations), they select the maximum observed Sharpe ratio:


$$\max_{N} \equiv \max(\widehat{SR}_1, \widehat{SR}_2, \dots, \widehat{SR}_N)$$

If all trials are truly null strategies ($\mathbb{E}[\widehat{SR}_i] = 0$), the expected maximum of $N$ independent standard normal random variables grows with $\sqrt{2 \ln N}$. Bailey and López de Prado (2014) generalized this using Extreme Value Theory (specifically the Fisher-Tippett-Gnedenko theorem and the Gumbel distribution limit) to account for non-zero variance and dependency among trials.

The expected maximum value of $N$ standard normal variables $Z_i \sim \mathcal{N}(0, 1)$ is approximated asymptotically as:

$$\mathbb{E}[\max_N] \approx (1 - \gamma) \Phi^{-1}\left(1 - \frac{1}{N}\right) + \gamma \Phi^{-1}\left(1 - \frac{1}{N \cdot e}\right)$$

where:

* $\Phi^{-1}(\cdot)$ is the inverse cumulative distribution function (quantile function) of the standard normal distribution.
* $e \approx 2.71828$ is Euler's number.
* $\gamma \approx 0.57721566$ is the **Euler-Mascheroni constant**.

To scale this expected maximum from standardized units back to the units of the strategy's specific return variance, we multiply by the standard error of the Sharpe ratio ($\sqrt{\text{Var}[\widehat{SR}]}$):

$$\mathbb{E}[\max_N]_{\text{scaled}} \approx \sqrt{\text{Var}[\widehat{SR}]} \cdot \left( (1 - \gamma)\Phi^{-1}\left(1 - \frac{1}{N}\right) + \gamma \Phi^{-1}\left(1 - \frac{1}{N \cdot e}\right) \right)$$

#### 3. Formulating the Deflated Sharpe Ratio ($Z$-Score & Probability)

Having established both the true variance of our observed Sharpe ratio ($\text{Var}[\widehat{SR}]$) and the expected maximum benchmark hurdle created by selection bias ($\mathbb{E}[\max_N]$), we construct a test statistic ($Z$-score).

We evaluate whether our observed $\widehat{SR}$ significantly exceeds the expected maximum of random noise trials:

$$Z = \frac{\widehat{SR} - \mathbb{E}[\max_N]}{\sqrt{\text{Var}[\widehat{SR}]}}$$

Finally, the **Deflated Sharpe Ratio (DSR)** is defined as the cumulative probability that a standard normal random variable falls below this $Z$-score (evaluating the p-value of backtest overfitting):

$$\text{DSR} = \Phi(Z) = \int_{-\infty}^{Z} \frac{1}{\sqrt{2\pi}} e^{-\frac{u^2}{2}} du \quad \blacksquare$$

|     |
| :-- |
| $`\text{DSR} = \Phi(Z) = \int_{-\infty}^{Z} \frac{1}{\sqrt{2\pi}} e^{-\frac{u^2}{2}} du`$ |

#### Economic & Practical Interpretation:

* If $\text{DSR} < 0.95$ (i.e., $Z < 1.645$), we fail to reject the null hypothesis that the strategy's Sharpe ratio is zero or negative *after* adjusting for multiple testing and non-normal fat tails. The backtest is deemed overfitted.
* If $\text{DSR} \ge 0.95$, the strategy exhibits statistical significance at the $5\%$ level, proving its performance cannot be explained purely by data mining across $N$ trials.

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 8: Order Flow Imbalance (OFI) & Micro-Price Formulation

#### First-Principles Mathematical Derivation: Order Flow Imbalance (OFI) & Micro-Price Formulation

#### 1. Discrete Level-1 Limit Order Book Mechanics

Let $t_n \in \{t_1, t_2, \dots, t_N\}$ denote discrete event timestamps (every time a limit order, cancellation, or market order arrives at the top-of-book).

At any event time $t$, the top-of-book state is given by the tuple $(P_b(t), V_b(t), P_a(t), V_a(t))$, where:

* $P_b(t)$ and $P_a(t)$ are the best bid and ask prices.
* $V_b(t)$ and $V_a(t)$ are the total available queue sizes at $P_b(t)$ and $P_a(t)$.
* Tick size is $\delta > 0$, such that $P_a(t) - P_b(t) = k \cdot \delta$ for $k \in \mathbb{N}_{\ge 1}$.

##### 1.1 Defining Bid-Side Queue Changes ( $\Delta W_b(t)$ )

Consider the physical accounting of liquidity change at the bid boundary over $\Delta t = t_n - t_{n-1}$. We project the aggregate flow onto the prevailing price grid:

###### Case I: $P_b(t) > P_b(t-1)$ (Bid Price Improvement)

A market participant posts a limit buy order inside the spread ($P_b(t) \ge P_b(t-1) + \delta$). The previous best bid $P_b(t-1)$ is no longer the top of book. The new queue size at the new best bid is composed entirely of net new liquidity introduced at $P_b(t)$:

$$
\Delta W_b(t) = +V_b(t)
$$

###### Case II: $P_b(t) = P_b(t-1)$ (Bid Price Unchanged)

The price level remains constant. The change in volume at $P_b(t)$ is the sum of limit order arrivals ($L_b$), cancellations ($C_b$), and market sell orders ($M_s$):

$$
\Delta W_b(t) = V_b(t) - V_b(t-1) = L_b(t) - C_b(t) - M_s(t)
$$

###### Case III: $P_b(t) < P_b(t-1)$ (Bid Price Depletion)

The previous bid queue $V_b(t-1)$ was completely consumed by market sell orders ($M_s$) or depleted by cancellations ($C_b$). The new top bid $P_b(t)$ falls back to a lower price level. From the perspective of the former best bid level $P_b(t-1)$, the total volume removed is $V_b(t-1)$:

$$
\Delta W_b(t) = -V_b(t-1)
$$

We write this compactly using the indicator function $\mathbb{I}(\cdot)$:

$$
\Delta W_b(t) = V_b(t) \cdot \mathbb{I}_{\{P_b(t) > P_b(t-1)\}} + \big(V_b(t) - V_b(t-1)\big) \cdot \mathbb{I}_{\{P_b(t) = P_b(t-1)\}} - V_b(t-1) \cdot \mathbb{I}_{\{P_b(t) < P_b(t-1)\}}
$$

##### 1.2 Defining Ask-Side Queue Changes ( $\Delta W_a(t)$ )

Similarly, for the ask side:

###### Case I: $P_a(t) < P_a(t-1)$ (Ask Price Improvement)

A limit sell order enters below the current ask. Lowering the ask price represents **increased selling pressure**. To maintain consistent signed orientation (positive = net buying pressure, negative = net selling pressure), the addition of ask volume at a better price contributes negatively to net demand:

$$
\Delta W_a(t) = -V_a(t)
$$

###### Case II: $P_a(t) = P_a(t-1)$ (Ask Price Unchanged)

Volume changes at the prevailing ask level via limit buys/cancellations/market orders:

$$
\Delta W_a(t) = V_a(t) - V_a(t-1) = L_a(t) - C_a(t) - M_b(t)
$$

###### Case III: $P_a(t) > P_a(t-1)$ (Ask Price Depletion)

The ask level is cleared by market buy orders or cancellations. Moving the ask level up reduces immediate supply, reflecting **positive demand absorption**. The removal of $V_a(t-1)$ at the old ask level yields:

$$
\Delta W_a(t) = +V_a(t-1)
$$

Compact indicator representation:

$$
\Delta W_a(t) = -V_a(t) \cdot \mathbb{I}_{\{P_a(t) < P_a(t-1)\}} + \big(V_a(t) - V_a(t-1)\big) \cdot \mathbb{I}_{\{P_a(t) = P_a(t-1)\}} + V_a(t-1) \cdot \mathbb{I}_{\{P_a(t) > P_a(t-1)\}}
$$

##### **1.3 Scalar Order Flow Imbalance ( $\text{OFI}_t$ )**

Net Order Flow Imbalance over the step $[t-1, t]$ is defined as the net shift in available liquidity oriented toward buy pressure:

$$
\text{OFI}_t = \Delta W_b(t) - \Delta W_a(t) \quad \blacksquare
$$

|     |
| :-- |
| $`\text{OFI}_t = \Delta W_b(t) - \Delta W_a(t)`$ |

Accumulating $\text{OFI}_t$ across an interval $[0, T]$ gives integrated order flow imbalance:

$$
I(T) = \sum_{k=1}^{N(T)} \text{OFI}_{t_k}
$$

#### 2. Micro-Price Linkage & Stochastic Formulation

To connect $\text{OFI}_t$ to price discovery mathematically, we formulate the relationship between imbalance and short-term price movements via the **Micro-Price** framework (Cont, Kukanov, & Stoikov, 2014).

##### 2.1 The Static Weighted Mid-Price

The standard mid-price $P_{\text{mid}}(t) = \frac{P_a(t) + P_b(t)}{2}$ ignores queue depth imbalance. Define the volume-weighted mid-price $P_{\text{imb}}(t)$:

$$
P_{\text{imb}}(t) = \frac{V_b(t)}{V_b(t) + V_a(t)} P_a(t) + \frac{V_a(t)}{V_b(t) + V_a(t)} P_b(t) = P_b(t) + I(t) \cdot S(t)
$$

where:

* $S(t) = P_a(t) - P_b(t)$ is the bid-ask spread.
* $I(t) = \frac{V_b(t)}{V_b(t) + V_a(t)} \in [0, 1]$ is the static order book imbalance ratio.

##### 2.2 First-Principles Derivation of Price Impact Equation

Consider the price update equation driven by continuous-time queue dynamics. Let $N_b(t)$ and $N_a(t)$ be point processes driving incoming bid/ask volume updates.

The continuous increment of the mid-price change $\mathrm{d}P_{\text{mid}}(t)$ over a time interval $\Delta t \to 0$ can be modeled as a linear response function to the net imbalance increment $\mathrm{d}I(t)$:

$$
\Delta P_{\text{mid}}(t) = \beta \cdot \text{OFI}_t + \varepsilon_t
$$

To derive parameter $\beta$ from first principles, consider a stationary LOB where the spread is fixed at $S(t) = \delta$.

1. **Volume to clear a tick:** Suppose the total depth at the best bid and ask averages $D = \frac{V_b + V_a}{2}$.
2. A price shift of $\delta$ occurs when a queue of size $V_b$ or $V_a$ is fully depleted (or added).
3. The expected price impact coefficient $\beta$ is therefore governed by the average total depth across the top levels:

$$
\beta = \frac{\delta}{V_b(t) + V_a(t)}
$$

This yields the normalized OFI formulation ($\text{NOFI}_t$):

$$
\text{NOFI}_t = \frac{\text{OFI}_t}{V_b(t-1) + V_a(t-1)}
$$

##### **2.3 Micro-Price as a Martingale Limit**

Let $P(t)$ denote the fundamental value process of the asset. Under the assumption that $P(t)$ is a martingale conditioned on the filtration $\mathcal{F}_t$ of current and historical book states:

$$
P^M(t) = \lim_{\tau \to \infty} \mathbb{E}\left[ P_{\text{mid}}(t + \tau) \;\middle\vert{}\; \mathcal{F}_t \right]
$$

Assuming top-of-book queue dynamics follow a 2-state Markov Chain for $\mathbf{x}_t = (V_b(t), V_a(t))$, the micro-price $P^M(t)$ can be explicitly solved as:

$$
P^M(t) = P_{\text{mid}}(t) + g(V_b(t), V_a(t), S(t))
$$

Taking the first-order Taylor expansion of $g(V_b, V_a, S)$ relative to small perturbations in volume $\Delta W_b, \Delta W_a$:

$$
\Delta P^M(t) \approx \frac{\partial g}{\partial V_b} \Delta W_b(t) + \frac{\partial g}{\partial V_a} \Delta W_a(t)
$$

By symmetry of the order book Markov chain under neutral flow, $\frac{\partial g}{\partial V_b} = -\frac{\partial g}{\partial V_a} = c > 0$. Substituting these partial derivatives yields:

$$
\Delta P^M(t) \approx c \cdot \big( \Delta W_b(t) - \Delta W_a(t) \big) = c \cdot \text{OFI}_t \quad \blacksquare
$$

|     |
| :-- |
| $`\Delta P^M(t) \approx c \cdot \big( \Delta W_b(t) - \Delta W_a(t) \big) = c \cdot \text{OFI}_t`$ |

This completes the dynamic first-principles linkage between local LOB event metrics ($\Delta W_b, \Delta W_a$), aggregate flow imbalance ($\text{OFI}$), and short-term price conditional expectations.

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 9: The Square-Root Law of Market Impact

#### First-Principles Derivation of the Square-Root Law of Market Impact

To derive the metaorder market impact relationship $\Delta P \propto \sigma \sqrt{\frac{Q}{V}}$ from first principles, we utilize the **Dimensional Analysis and Order Book Liquidity Clearing Model** pioneered by Barra, Tóth et al. (2011) and Farmer et al. (2013).

#### 1. Invariance Principles and Scale Invariance

Let:

* $Q$: Size of the metaorder in shares.
* $V$: Average daily volume (ADV) in shares during execution horizon $T$.
* $\sigma$: Daily volatility of return (dimensionless fraction or absolute currency units).
* $I$: Total price impact (relative return $\frac{\Delta P}{P}$ or absolute price shift $\Delta P$).

By Buckingham $\pi$-theorem, price impact $I$ must be expressed as a non-dimensional quantity function of non-dimensional ratios formed from market variables:

$$I = f\left(\frac{Q}{V}, \sigma, \frac{T}{T_0}\right)$$

Assuming return scale invariance over horizon $T$, market volatility scales with square-root of time under diffusion: $\sigma_T = \sigma \sqrt{\frac{T}{T_{\text{day}}}}$.

#### 2. Order Book Latent Liquidity & Linear Density

Assume a locally continuous latent order book where order density $\rho(p)$ represents shares per unit price away from the mid-price $P_0$.

$$Q = \int_{P_0}^{P_0 + \Delta P} \rho(p) \, dp$$

In a steady-state competitive market, market makers deploy liquidity dynamically. Diffusion of latent order prices yields a uniform average linear liquidity density $\rho_0 = \frac{dV}{dP}$ near the mid-price:

$$\rho_0 = \frac{V_0}{\sigma}$$

where $V_0$ is daily volume and $\sigma$ provides the characteristic price scale over which volume $V_0$ is distributed.

#### 3. Clearing the Latent Order Book

Integrating constant density $\rho_0$ over price shift $\Delta P$:

$$Q = \rho_0 \cdot \Delta P = \left(\frac{V}{\sigma}\right) \cdot \Delta P$$

However, metaorders are executed sequentially over time $T$. As the metaorder executes, market participants adjust quotes dynamically via linear market impact rate $g(v)$, where $v = \frac{Q}{T}$ is instantaneous execution rate.

The instantaneous impact at time $t \in [0, T]$ scales with accumulated volume $q(t) = v \cdot t$:

$$\Delta P(t) = Y \cdot \sigma \cdot \sqrt{\frac{v \cdot t}{V}}$$

Integrating total impact across execution duration $T$ with dimensionless constant $Y \approx 0.5 - 1.0$:

$$\Delta P = Y \cdot \sigma \cdot \sqrt{\frac{Q}{V}} \quad \blacksquare$$

|  |
| --- |
| $`\Delta P = Y \cdot \sigma \cdot \sqrt{\frac{Q}{V}}`$ |

#### Key Takeaway:

Market impact depends strictly on relative volume fraction $\frac{Q}{V}$ raised to the power of $0.5$, scaled by underlying asset volatility $\sigma$, independent of execution speed $T$.

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 10: TCA Statistical Significance & Hypothesis Testing (CLT / `t`-Test)

#### First-Principles Mathematical Derivation: TCA Significance Testing

To evaluate whether algorithmic execution performance (slippage over benchmark) represents true skill vs. random market noise, we construct a formal hypothesis test from the Central Limit Theorem.

#### 1. Sample Formulation

Let $s_i = P_i^{\text{exec}} - P_i^{\text{bench}}$ be the realized per-trade benchmark slippage for trade $i \in \{1, \dots, N\}$.

* Trades are assumed independent with population mean $\mu$ and finite variance $\sigma^2$.
* Sample mean slippage: $\bar{s} = \frac{1}{N} \sum_{i=1}^N s_i$.
* Unbiased sample variance: $s^2 = \frac{1}{N-1} \sum_{i=1}^N (s_i - \bar{s})^2$.

#### 2. Hypothesis Definition

* $\mathcal{H}_0: \mu \le 0$ (The strategy generates no statistically significant positive alpha / cost reduction).
* $\mathcal{H}_1: \mu > 0$ (The strategy generates statistically significant positive cost reduction).

#### 3. Central Limit Convergence and Test Statistic

By the Kolmogorov-Feller Central Limit Theorem, as sample size $N \to \infty$:

$$\frac{\bar{s} - \mu}{\sigma / \sqrt{N}} \xrightarrow{d} \mathcal{N}(0, 1)$$

Replacing unknown population variance $\sigma^2$ with sample variance $s^2$ yields the Student’s $t$-statistic:

$$t = \frac{\bar{s} - 0}{s / \sqrt{N}} = \frac{\bar{s} \sqrt{N}}{s}$$

For sample sizes $N \ge 30$, $t$ converges rapidly to the standard normal distribution $\mathcal{Z} \sim \mathcal{N}(0, 1)$.

#### 4. Required Minimum Sample Size Formula

To achieve statistical power $1 - \beta$ at significance level $\alpha$ for expected effect size (mean slippage) $\mu_0$:

$$N^* = \left( \frac{(z_{\alpha} + z_{\beta}) s}{\mu_0} \right)^2 \quad \blacksquare$$

|  |
| --- |
| $`t = \frac{\bar{s} \sqrt{N}}{s} \quad \implies \quad N^* = \left( \frac{(z_{\alpha} + z_{\beta}) s}{\mu_0} \right)^2`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 11: Dynamic Programming & Minimax Solution for the Two Eggs Problem

#### First-Principles Derivation: Optimal Egg-Dropping Strategy

Given $E = 2$ eggs and a building with $K$ floors, find the minimum number of drop trials $W$ required in the worst-case scenario to determine the highest safe floor $F \in \{0, 1, \dots, K\}$.

#### 1. Dynamic Programming Recurrence Formulation

Let $DP(e, k)$ be the minimum number of drops needed in the worst case with $e$ eggs and $k$ floors remaining.

If we drop an egg from floor $x \in \{1, 2, \dots, k\}$:

1. **Egg Breaks:** We now have $e - 1$ eggs left and must search the lower $x - 1$ floors. Drops required: $1 + DP(e - 1, x - 1)$.
2. **Egg Survives:** We still have $e$ eggs left and must search the upper $k - x$ floors. Drops required: $1 + DP(e, k - x)$.

Adopting the minimax principle (adversarial nature of worst-case optimization):

$$DP(e, k) = 1 + \min_{1 \le x \le k} \left( \max\left( DP(e - 1, x - 1), DP(e, k - x) \right) \right)$$

Base conditions:

* $DP(1, k) = k$ (With 1 egg, we must test linearly sequentially bottom-to-top).
* $DP(e, 0) = 0, \quad DP(e, 1) = 1$.

#### 2. First-Principles Closed-Form Derivation for $E = 2$

For $e = 2$, let $W$ be the target maximum number of drops allowed.

* On the 1st drop, test floor $x_1 = W$.
* If it breaks, 1 egg remains and $W - 1$ drops remain, which can linearly test floors $1$ to $W - 1$.
* If it survives, we have $W - 1$ drops left total.


* On the 2nd drop, step up by $W - 1$ floors to floor $x_2 = W + (W - 1)$.
* On the $k$-th drop, step up by $W - (k - 1)$ floors.

Total maximum floor coverage $K$ with $W$ drops is the sum of the arithmetic series:

$$K(W) = W + (W - 1) + (W - 2) + \dots + 1 = \sum_{j=1}^W j = \frac{W(W + 1)}{2}$$

#### 3. Solving for Minimal Drops $W$ given $K = 100$

Set $K = 100$:

$$\frac{W(W + 1)}{2} \ge 100 \implies W^2 + W - 200 \ge 0$$

Solving the quadratic equation $W^2 + W - 200 = 0$:

$$W = \frac{-1 + \sqrt{1 - 4(1)(-200)}}{2} = \frac{-1 + \sqrt{801}}{2} \approx \frac{-1 + 28.3019}{2} = 13.65$$

Ceiling to the nearest integer for discrete trials:

$$W = \lceil 13.65 \rceil = 14 \quad \blacksquare$$

|  |
| --- |
| $`\frac{W(W + 1)}{2} \ge K \quad \implies \quad W = \left\lceil \frac{-1 + \sqrt{1 + 8K}}{2} \right\rceil`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 12: Negative-Binomial Model for Partial Fill Dynamics

#### First-Principles Mathematical Derivation: Negative-Binomial Partial Fill Model

When limit orders sit at the top of book, liquidity consumption happens in discrete fills. We model order execution as a sequence of Bernoulli trials where market order volume arrivals attempt to clear queue depth.

#### 1. Compound Poisson to Negative-Binomial Transition

Let limit order queue clearing be defined as achieving $r$ successful execution allocations (each allocation representing a fixed lot size $v_0$).

Assume market orders arrive according to a Poisson process with intensity $\lambda$, but the size of arriving market orders follows a Gamma distribution $\Gamma(r, \beta)$ due to order size heterogeneity.

The probability density function of Gamma distributed intensity $\lambda \sim \text{Gamma}(r, \beta)$ is:

$$f(\lambda) = \frac{\beta^r}{\Gamma(r)} \lambda^{r-1} e^{-\beta \lambda}$$

The conditional distribution of fills $k$ given intensity $\lambda$ is Poisson:

$$P(K = k \mid \lambda) = \frac{\lambda^k e^{-\lambda}}{k!}$$

#### 2. Marginalizing over Intensity $\lambda$

To find marginal probability $P(K = k)$:

$$P(K = k) = \int_0^\infty P(K = k \mid \lambda) f(\lambda) \, d\lambda$$

$$P(K = k) = \int_0^\infty \left( \frac{\lambda^k e^{-\lambda}}{k!} \right) \left( \frac{\beta^r}{\Gamma(r)} \lambda^{r-1} e^{-\beta \lambda} \right) d\lambda$$

Combining terms:

$$P(K = k) = \frac{\beta^r}{k! \, \Gamma(r)} \int_0^\infty \lambda^{k+r-1} e^{-\lambda(\beta + 1)} \, d\lambda$$

Using the integral identity $\int_0^\infty x^{n-1} e^{-a x} \, dx = \frac{\Gamma(n)}{a^n}$:

$$P(K = k) = \frac{\beta^r}{k! \, \Gamma(r)} \cdot \frac{\Gamma(k + r)}{(\beta + 1)^{k + r}}$$

Using $\Gamma(r) = (r - 1)!$ and defining parameter $p = \frac{\beta}{\beta + 1} \implies 1 - p = \frac{1}{\beta + 1}$:

$$P(K = k) = \frac{(k + r - 1)!}{k! (r - 1)!} p^r (1 - p)^k = \binom{k + r - 1}{k} p^r (1 - p)^k \quad \blacksquare$$

|  |
| --- |
| $`P(K = k) = \binom{k + r - 1}{k} p^r (1 - p)^k`$ |

#### Expectation and Variance:

$$\mathbb{E}[K] = \frac{r(1-p)}{p}, \quad \mathbb{V}[K] = \frac{r(1-p)}{p^2}$$


Overdispersion ($\mathbb{V}[K] > \mathbb{E}[K]$) rigorously captures the clustering and variance in limit order fill times.

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 13: Bayesian Information Updating in the Monty Hall Problem

#### First-Principles Mathematical Derivation: Monty Hall Problem

To prove why switching doors doubles the probability of winning from $\frac{1}{3}$ to $\frac{2}{3}$, we apply Bayes' Theorem directly to the host's conditional action filtration.

#### 1. Defining Hypotheses and Observation Events

Let $C \in \{1, 2, 3\}$ be the door hiding the car.

* Prior distribution: $P(C = 1) = P(C = 2) = P(C = 3) = \frac{1}{3}$.

Without loss of generality, assume the contestant chooses **Door 1**.
Let $H \in \{1, 2, 3\}$ be the door opened by the host.

#### 2. Host Conditional Likelihoods $P(H = h \mid C = c)$

The host operates under strict constraints:

1. The host never opens the door chosen by the player ($H \neq 1$).
2. The host never opens the door hiding the car ($H \neq C$).
3. If the host has two choices (when $C = 1$), the host chooses uniformly at random.

Suppose the host opens **Door 3** ($H = 3$). We compute likelihoods $P(H = 3 \mid C = c)$:

* If $C = 1$: Host can open Door 2 or Door 3. $P(H = 3 \mid C = 1) = \frac{1}{2}$.
* If $C = 2$: Host must open Door 3 (cannot open 1 or 2). $P(H = 3 \mid C = 2) = 1$.
* If $C = 3$: Host cannot open Door 3. $P(H = 3 \mid C = 3) = 0$.

#### 3. Total Probability of Observation $P(H = 3)$

$$P(H = 3) = \sum_{c=1}^3 P(H = 3 \mid C = c) P(C = c)$$

$$P(H = 3) = \left(\frac{1}{2} \cdot \frac{1}{3}\right) + \left(1 \cdot \frac{1}{3}\right) + \left(0 \cdot \frac{1}{3}\right) = \frac{1}{6} + \frac{1}{3} = \frac{1}{2}$$

#### 4. Posterior Probabilities via Bayes' Theorem

Calculate posterior probability of car being behind Door 1 (Staying):

$$P(C = 1 \mid H = 3) = \frac{P(H = 3 \mid C = 1) P(C = 1)}{P(H = 3)} = \frac{\frac{1}{2} \cdot \frac{1}{3}}{\frac{1}{2}} = \frac{1}{3}$$

Calculate posterior probability of car being behind Door 2 (Switching):

$$P(C = 2 \mid H = 3) = \frac{P(H = 3 \mid C = 2) P(C = 2)}{P(H = 3)} = \frac{1 \cdot \frac{1}{3}}{\frac{1}{2}} = \frac{2}{3} \quad \blacksquare$$

|  |
| --- |
| $`P(\text{Win} \mid \text{Switch}) = \frac{2}{3}, \quad P(\text{Win} \mid \text{Stay}) = \frac{1}{3}`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 14: First-Step Analysis for Gambler's Ruin & Absorption Probabilities

#### First-Principles Mathematical Derivation: Gambler's Ruin

Consider a trader starting with capital $i$ units, seeking to reach target capital $N$ before hitting ruin ($0$). Each trade wins $\$1$ with probability $p$ and loses $\$1$ with probability $q = 1 - p$.

#### 1. Setup of the Difference Equation

Let $P_i$ be the probability of reaching $N$ starting from $i$.
Applying **First-Step Analysis** (law of total probability conditioning on the first step):

$$P_i = p P_{i+1} + q P_{i-1} \quad \text{for } 1 \le i \le N-1$$

Boundary conditions:


$$P_0 = 0, \quad P_N = 1$$

#### 2. Solving the Homogeneous Linear Difference Equation

Since $p + q = 1$, rewrite as:

$$(p + q) P_i = p P_{i+1} + q P_{i-1}$$

$$p (P_{i+1} - P_i) = q (P_i - P_{i-1}) \implies P_{i+1} - P_i = \frac{q}{p} (P_i - P_{i-1})$$

Let $\gamma = \frac{q}{p}$. Define step differences $D_i = P_i - P_{i-1}$:

$$D_{i+1} = \gamma D_i \implies D_i = \gamma^{i-1} D_1$$

By telescoping sum:

$$P_k = P_k - P_0 = \sum_{i=1}^k D_i = D_1 \sum_{i=1}^k \gamma^{i-1}$$

#### 3. Summing Geometric Series and Applying Boundary Conditions

Case I: $p \neq q \implies \gamma \neq 1$:

$$P_k = D_1 \left( \frac{1 - \gamma^k}{1 - \gamma} \right)$$

Using $P_N = 1$:

$$P_N = D_1 \left( \frac{1 - \gamma^N}{1 - \gamma} \right) = 1 \implies D_1 = \frac{1 - \gamma}{1 - \gamma^N}$$

Substituting $D_1$ back into $P_k$:

$$P_k = \frac{1 - \left(\frac{q}{p}\right)^k}{1 - \left(\frac{q}{p}\right)^N} \quad \blacksquare$$

|  |
| --- |
| $`P_k = \frac{1 - (q/p)^k}{1 - (q/p)^N} \quad (p \neq q), \qquad P_k = \frac{k}{N} \quad (p = q = 0.5)`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 15: Central Limit Theorem for Cumulative VWAP Slippage Variance

#### First-Principles Derivation: VWAP Slippage Dispersion

Volume-Weighted Average Price (VWAP) over execution horizon $T$ is:

$$P_{\text{VWAP}} = \frac{\sum_{k=1}^N v_k P_k}{\sum_{k=1}^N v_k} = \sum_{k=1}^N w_k P_k$$

where volume weights $w_k = \frac{v_k}{V_{\text{tot}}}$ satisfy $\sum_{k=1}^N w_k = 1$.

#### 1. Execution Slippage Formulation

Let execution price $P_k^e = P_k + \epsilon_k$, where $\epsilon_k$ is execution noise with $\mathbb{E}[\epsilon_k] = 0$, $\mathbb{V}[\epsilon_k] = \sigma_\epsilon^2$, and asset return process $P_k = P_0 + \sum_{j=1}^k \Delta P_j$ with $\mathbb{V}[\Delta P_j] = \sigma_0^2 \tau$.

Slippage $\Delta_{\text{VWAP}} = P_{\text{exec}} - P_{\text{VWAP}}$:

$$\Delta_{\text{VWAP}} = \sum_{k=1}^N w_k (P_k + \epsilon_k) - \sum_{k=1}^N w_k P_k = \sum_{k=1}^N w_k \epsilon_k$$

#### 2. Variance Calculation with Uncorrelated Execution Noise

$$\mathbb{V}[\Delta_{\text{VWAP}}] = \mathbb{V}\left( \sum_{k=1}^N w_k \epsilon_k \right) = \sum_{k=1}^N w_k^2 \mathbb{V}[\epsilon_k] = \sigma_\epsilon^2 \sum_{k=1}^N w_k^2$$

#### 3. Bounds via Cauchy-Schwarz Inequality

To find minimum variance distribution of weights $w_k$:

$$\left( \sum_{k=1}^N w_k \cdot 1 \right)^2 \le \left( \sum_{k=1}^N w_k^2 \right) \left( \sum_{k=1}^N 1^2 \right)$$

$$1 \le N \sum_{k=1}^N w_k^2 \implies \sum_{k=1}^N w_k^2 \ge \frac{1}{N}$$

For uniform profile $w_k = \frac{1}{N}$:

$$\mathbb{V}[\Delta_{\text{VWAP}}] = \frac{\sigma_\epsilon^2}{N} \quad \blacksquare$$

|  |
| --- |
| $`\mathbb{V}[\Delta_{\text{VWAP}}] = \sigma_\epsilon^2 \sum_{k=1}^N w_k^2 \ge \frac{\sigma_\epsilon^2}{N}`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 16: Covariance & Variance Decomposition of Correlated Bernoulli Trials

#### First-Principles Mathematical Derivation: Correlated Binary Signals

Consider $N$ binary trading signals $X_1, X_2, \dots, X_N \in \{0, 1\}$ representing strategy successes, each with marginal success probability $P(X_i = 1) = p$.

#### 1. Pairwise Covariance Definition

Assume constant pairwise correlation $\rho = \text{Corr}(X_i, X_j)$ for $i \neq j$.

Marginal variance of individual Bernoulli trial:

$$\sigma_X^2 = \mathbb{V}[X_i] = p(1 - p)$$

By definition of correlation:

$$\rho = \frac{\text{Cov}(X_i, X_j)}{\sqrt{\mathbb{V}[X_i]\mathbb{V}[X_j]}} = \frac{\text{Cov}(X_i, X_j)}{p(1 - p)}$$

$$\text{Cov}(X_i, X_j) = \rho p(1 - p)$$

#### 2. Variance of Aggregate Sum $S_N = \sum_{i=1}^N X_i$

$$\mathbb{V}[S_N] = \mathbb{V}\left( \sum_{i=1}^N X_i \right) = \sum_{i=1}^N \mathbb{V}[X_i] + \sum_{i=1}^N \sum_{j \neq i}^N \text{Cov}(X_i, X_j)$$

Counting diagonal elements ($N$) and off-diagonal elements ($N(N - 1)$):

$$\mathbb{V}[S_N] = N p(1 - p) + N(N - 1) \rho p(1 - p)$$

Factoring out $N p (1 - p)$:

$$\mathbb{V}[S_N] = N p(1 - p) \Big[ 1 + (N - 1)\rho \Big] \quad \blacksquare$$

|  |
| --- |
| $`\mathbb{V}[S_N] = N p(1 - p) \Big[ 1 + (N - 1)\rho \Big]`$ |

#### Key Quant Implication:

If correlation $\rho > 0$, portfolio variance scales with $\mathcal{O}(N^2)$ rather than $\mathcal{O}(N)$, eliminating the diversification benefits of adding more trading strategies.

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 17: Fermi Estimation Framework for CME Futures Daily Volume

#### First-Principles Mathematical Derivation: Fermi CME Volume Estimation

To derive an order-of-magnitude estimate for total daily contract volume across CME Group futures markets, we decompose the total volume $V_{\text{CME}}$ into fundamental market participant drivers.

#### 1. Structural Volumetric Equation

$$V_{\text{CME}} = \sum_{m \in \text{Asset Classes}} N_m \times A_m \times F_m$$

where:

* $N_m$: Number of active institutional/retail trading entities in asset class $m$.
* $A_m$: Average position size (contracts per order).
* $F_m$: Daily turn/turnover frequency (orders per day per entity).

#### 2. Class-by-Class Operational Breakdown

We aggregate over 4 core asset class pillars:

1. **Equity Index (E-mini S&P 500, Nasdaq 100):** $V_{\text{eq}} \approx 3.5 \times 10^6$ contracts/day.
2. **Rates (SOFR, 10-Yr Treasury Futures):** $V_{\text{rates}} \approx 12.0 \times 10^6$ contracts/day.
3. **Energy & Commodities (WTI Crude, Gold, Corn):** $V_{\text{comm}} \approx 3.0 \times 10^6$ contracts/day.
4. **FX Futures (EUR/USD, JPY/USD):** $V_{\text{fx}} \approx 1.5 \times 10^6$ contracts/day.

#### 3. Summation & Order of Magnitude

$$V_{\text{CME}} = (3.5 + 12.0 + 3.0 + 1.5) \times 10^6 = 20.0 \times 10^6 \text{ contracts/day}$$

Taking the logarithm base 10 to establish the Fermi scale:

$$\log_{10}(V_{\text{CME}}) \approx \log_{10}(2.0 \times 10^7) = 7.301 \implies \mathcal{O}(10^7) \quad \blacksquare$$

|  |
| --- |
| $`V_{\text{CME}} \approx 2.0 \times 10^7 \text{ contracts/day} \quad \implies \quad \mathcal{O}(10^7)`$ |

[🔝 Back to Top](#-table-of-contents)

---
---

### Derivation 18: Multi-Period Kelly Criterion & Growth Rate Maximization

#### First-Principles Mathematical Derivation: Kelly Criterion

To maximize long-term logarithmic capital growth rate without incurring ruin, we formulate John Kelly's (1956) information-theoretic optimal bet sizing model.

#### 1. Wealth Process Formulation

Let initial capital be $W_0$. At each step $t$, leverage fraction $f \in [0, 1]$ of current wealth $W_{t-1}$ is allocated to a trade.

* Win probability: $p$, yielding return $+b \cdot f$ (where $b$ is net odds payout).
* Loss probability: $q = 1 - p$, yielding return $-f$.

After $N$ independent trials with $W$ wins and $L = N - W$ losses:

$$W_N = W_0 (1 + b f)^W (1 - f)^{N - W}$$

#### 2. Asymptotic Expected Logarithmic Growth Rate

Define geometric growth rate per trade $g(f)$:

$$g(f) = \lim_{N \to \infty} \frac{1}{N} \ln\left( \frac{W_N}{W_0} \right) = \lim_{N \to \infty} \left[ \frac{W}{N} \ln(1 + b f) + \frac{N - W}{N} \ln(1 - f) \right]$$

By the Strong Law of Large Numbers, $\frac{W}{N} \xrightarrow{a.s.} p$:

$$g(f) = p \ln(1 + b f) + q \ln(1 - f)$$

#### 3. First-Order Optimization for Optimal Fraction $f^{\*}$

To maximize growth rate $g(f)$, take derivative with respect to $f$ and set to zero:

$$\frac{d g}{d f} = \frac{p \cdot b}{1 + b f} - \frac{q}{1 - f} = 0$$

$$p b (1 - f) = q (1 + b f)$$

$$p b - p b f = q + q b f$$

$$p b - q = b f (p + q)$$

Since $p + q = 1$:

$$f^{\*} = \frac{p b - q}{b} \quad \blacksquare$$

|  |
| --- |
| $`f^{\*} = \frac{p b - q}{b} = \frac{p(b + 1) - 1}{b}`$ |

#### Extension to Continuous Gaussian Returns:

For continuous return process $r \sim \mathcal{N}(\mu, \sigma^2)$, Taylor expanding $\mathbb{E}[\ln(1 + f r)]$ yields the continuous Kelly leverage formula:

$$f^{\*} = \frac{\mu}{\sigma^2}$$

[🔝 Back to Top](#-table-of-contents)

---
---
