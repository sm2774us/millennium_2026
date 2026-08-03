<div align="center">

# 🏦 Millennium Management — QR Systematic Macro · Rapid Revision Guide

> **How to use:** One pass per section the night before. Each question = one card. Memorise the **formula**, the **mechanism**, and the **key insight**. Everything else is colour.

</div>

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📋 Table of Contents

| § | Topic | Questions |
|---|-------|-----------|
| [A](#a--probability--statistics) | 🎲 Probability & Statistics | Q1–Q7 |
| [B](#b--stochastic-calculus) | 📈 Stochastic Calculus | Q8–Q12 |
| [C](#c--linear-algebra) | 🔢 Linear Algebra | Q13–Q15 |
| [D](#d--machine-learning) | 🤖 Machine Learning | Q16–Q18 |
| [E](#e--artificial-intelligence) | 🧠 AI / NLP | Q19–Q20 |
| [F](#f--coding) | 💻 Coding | Q21–Q23 |
| [G](#g--reasoning) | 🔍 Reasoning | Q24–Q25 |
| [H](#h--brain-teasers) | 🧩 Brain Teasers | Q26–Q28 |
| [I](#i--mathematical-induction) | ∑ Induction | Q29–Q30 |
| [⚡](#-master-cheatsheet) | Master Cheatsheet | All formulas |

[🔝 Back to Top](#-table-of-contents)

---

## A · 🎲 Probability & Statistics

> **Section summary:** Bayes updates, order statistics, ruin theory, MLE, cointegration vs correlation, GARCH persistence, probability simulation. Know the derivation *and* the finance analogy for each.

[🔝 Back to Top](#-table-of-contents)

---

### Q1 · Biased Coin Jar (Bayesian Update)

**Setup:** 999 fair + 1 double-headed coin. Pick one, observe 10H. P(double-headed)?

$$P(D \mid H_{10}) = \frac{1 \cdot \tfrac{1}{1000}}{1 \cdot \tfrac{1}{1000} + \tfrac{1}{1024}\cdot\tfrac{999}{1000}} = \frac{1024}{2023} \approx \mathbf{50.6\%}$$

```
Prior: 1-in-1000 → After 10H: only 50.6%
Lesson: rare priors resist updating — need many observations
```

**Finance link:** Regime detection — "Is the market in a high-vol regime, or just a bad week?" Hidden Markov Model / structural break detection.

**Key insight:** Beyond 10 flips the posterior flips to favour double-headed. 10 flips is the *transition point*.

[🔝 Back to Top](#-table-of-contents)

---

### Q2 · Expected Maximum of N Uniform RVs

$$F_{M_n}(x) = x^n \Rightarrow f_{M_n}(x) = nx^{n-1}$$

$$\mathbb{E}[\max(U_1,\ldots,U_n)] = \int_0^1 x\cdot nx^{n-1}\,dx = \boxed{\dfrac{n}{n+1}}$$

**Check:** $n=1 \Rightarrow \tfrac{1}{2}$ ✓; $n\to\infty \Rightarrow 1$ ✓

**Finance link:** Best-of-N strategies; expected maximum drawdown across N paths.

[🔝 Back to Top](#-table-of-contents)

---

### Q3 · Gambler's Ruin

**Reach $N$ starting from $k$, win prob $p$, lose prob $q=1-p$, $r=q/p$:**

$$P(\text{reach }N \mid k) = \begin{cases} \dfrac{1-r^k}{1-r^N} & p\ne q \\[6pt] \dfrac{k}{N} & p=q \end{cases}$$

```
  0 ←── k ──────────────► N
 (ruin)      p each step   (target)
```

**Finance link:** Drawdown limits on a strategy. Link to Kelly Criterion — risk of ruin if over-betting.

[🔝 Back to Top](#-table-of-contents)

---

### Q4 · MLE for Exponential Distribution

**Log-likelihood:** $\ell(\lambda) = n\log\lambda - \lambda\sum x_i$

$$\frac{d\ell}{d\lambda} = \frac{n}{\lambda} - \sum x_i = 0 \Rightarrow \boxed{\hat\lambda_{\text{MLE}} = \frac{1}{\bar x}}$$

**Mechanism:** Rate = reciprocal of sample mean. Second derivative $=-n/\lambda^2 < 0$ confirms maximum.

**Finance link:** Modelling inter-trade arrival times; default intensity in credit models.

[🔝 Back to Top](#-table-of-contents)

---

### Q5 · Correlation vs. Cointegration

| Property | Correlation | Cointegration |
|---|---|---|
| Applies to | Returns (stationary) | Prices (I(1)) |
| Time-varying | Yes | Structural |
| Pairs trading | Insufficient | Foundation |

**Engle–Granger 2-step:**
1. Regress $P^A_t = \alpha + \beta P^B_t + \varepsilon_t$
2. ADF test on residuals $\varepsilon_t$:

$$\Delta\varepsilon_t = \gamma\varepsilon_{t-1} + \sum c_j\Delta\varepsilon_{t-j} + u_t$$

Reject $H_0:\gamma=0$ → cointegrated.

**Key insight:** High correlation ≠ cointegration (spurious regression risk). Cointegration is the *correct* foundation for stat-arb.

[🔝 Back to Top](#-table-of-contents)

---

### Q6 · GARCH(1,1) Persistence

$$\sigma_t^2 = \omega + \alpha\varepsilon_{t-1}^2 + \beta\sigma_{t-1}^2$$

**Persistence** $= \alpha+\beta$. Long-run variance: $\bar\sigma^2 = \omega/(1-\alpha-\beta)$

```
α+β < 1  → mean-reverting (stationary)
α+β = 1  → IGARCH: shocks permanent → VaR understated
Typical equity: α+β ≈ 0.97–0.99
```

**Finance link:** 10-day VaR window. Code with `arch` library. Interview asks to interpret $\alpha+\beta$ live.

[🔝 Back to Top](#-table-of-contents)

---

### Q7 · Fair Coin → Arbitrary Probability $p$

**Binary expansion method:** Write $p = 0.b_1 b_2 b_3\ldots$ in binary.

```
Flip coin → result c_i, compare to b_i:
  c_i < b_i → WIN immediately
  c_i > b_i → LOSE immediately
  c_i = b_i → continue
```

**Finite $p=m/n$:** Generate uniform integer in $\{0,\ldots,n-1\}$ via repeated flips; WIN if result $< m$.

**Finance link:** Simulation of path-dependent payoffs with arbitrary probabilities; Monte Carlo importance sampling.

[🔝 Back to Top](#-table-of-contents)

---

## B · 📈 Stochastic Calculus

> **Section summary:** Itô's lemma and the $-\sigma^2/2$ correction, Black-Scholes PDE derivation via delta-hedging, local vs stochastic vol, martingale/FTAP, Vasicek bond pricing. The $\sigma^2/2$ term and the no-arbitrage argument are the two most commonly failed exam points.

[🔝 Back to Top](#-table-of-contents)

---

### Q8 · Itô's Lemma → GBM Log Return

**GBM:** $dS_t = \mu S_t\,dt + \sigma S_t\,dW_t$. Apply $f=\ln S$:

$$f_S = \frac{1}{S},\quad f_{SS} = -\frac{1}{S^2},\quad (dS)^2 = \sigma^2 S^2\,dt$$

$$\boxed{d\ln S_t = \left(\mu - \frac{\sigma^2}{2}\right)dt + \sigma\,dW_t}$$

```
The -σ²/2 is the ITÔ CORRECTION
  → comes from quadratic variation dW²=dt
  → candidates who forget this fail immediately
```

**Implication:** $\ln S_T \sim \mathcal{N}\!\left(\ln S_0 + (\mu-\tfrac{\sigma^2}{2})T,\;\sigma^2 T\right)$ — foundation of Black-Scholes.

[🔝 Back to Top](#-table-of-contents)

---

### Q9 · Black-Scholes PDE

**Delta-hedged portfolio** $\Pi = V - \tfrac{\partial V}{\partial S}S$ eliminates stochastic term:

$$\boxed{\frac{\partial V}{\partial t} + \frac{1}{2}\sigma^2 S^2\frac{\partial^2 V}{\partial S^2} + rS\frac{\partial V}{\partial S} - rV = 0}$$

```
Term            | Meaning
────────────────┼────────────────────
∂V/∂t           │ Time decay (Theta)
½σ²S²∂²V/∂S²   │ Convexity (Gamma)
rS·∂V/∂S        │ Risk-neutral drift
-rV             │ Discounting
```

**Mechanism:** No-arbitrage forces $d\Pi = r\Pi\,dt$. The stochastic $dW$ term cancels with the $\Delta$ hedge.

[🔝 Back to Top](#-table-of-contents)

---

### Q10 · Local Vol vs Heston

**Local Vol (Dupire):** $\sigma=\sigma(S,t)$ fits today's smile exactly but **future smile dynamics wrong** (predicts flattening, market shows sticky-strike/delta).

**Heston:**
$$dv_t = \kappa(\theta-v_t)\,dt + \xi\sqrt{v_t}\,dW^v_t,\quad \langle dW^S,dW^v\rangle=\rho\,dt$$

| Param | Meaning |
|-------|---------|
| $\kappa$ | Mean-reversion speed |
| $\theta$ | Long-run variance |
| $\xi$ | Vol of vol |
| $\rho$ | Leverage effect (typically $<0$) |

**Key:** Heston has semi-closed-form characteristic function → FFT pricing. Better for exotics (barriers, cliquets).

[🔝 Back to Top](#-table-of-contents)

---

### Q11 · Martingale & FTAP

**Martingale:** $\mathbb{E}^\mathbb{P}[M_t\mid\mathcal{F}_s]=M_s$

**FTAP:**
- No-arbitrage ↔ ∃ equivalent martingale measure $\mathbb{Q}$
- Completeness ↔ $\mathbb{Q}$ is unique

$$V_0 = e^{-rT}\cdot\mathbb{E}^\mathbb{Q}[\text{Payoff}(S_T)]$$

**Girsanov:** Changes drift $\mu\to r$ via Radon-Nikodym $\frac{d\mathbb{Q}}{d\mathbb{P}}$. Discounted prices are $\mathbb{Q}$-martingales.

[🔝 Back to Top](#-table-of-contents)

---

### Q12 · Vasicek Model & Bond Price

$$dr_t = \kappa(\theta - r_t)\,dt + \sigma\,dW_t \quad\text{(Ornstein-Uhlenbeck)}$$

**Affine bond price:** $P(t,T) = e^{A(\tau)-B(\tau)r_t}$, $\tau=T-t$

$$B(\tau) = \frac{1-e^{-\kappa\tau}}{\kappa}, \qquad A(\tau) = \left(\theta-\frac{\sigma^2}{2\kappa^2}\right)(B(\tau)-\tau) - \frac{\sigma^2 B(\tau)^2}{4\kappa}$$

**Limitation:** Allows negative rates. **Use case:** Term structure modelling, closed-form option prices on bonds.

[🔝 Back to Top](#-table-of-contents)

---

## C · 🔢 Linear Algebra

> **Section summary:** PCA for factor construction (scree plot, variance explained), SVD for denoising covariance matrices (Marchenko-Pastur), condition number and numerical stability. Know when to use each decomposition.

[🔝 Back to Top](#-table-of-contents)

---

### Q13 · PCA for Statistical Risk Factors

**Pipeline:** Returns $R\in\mathbb{R}^{T\times n}$ → standardise → covariance $\Sigma$ → eigen-decompose:

$$\Sigma = V\Lambda V^\top,\quad \text{EVR}(k) = \frac{\sum_{i=1}^k\lambda_i}{\sum_{i=1}^n\lambda_i}$$

```
Scree Plot:
 λ │●
   │  ●
   │    ●
   │      ● ● ● ● ●
   └──────────────► PC index
         ↑ elbow = retain these
```

**Limitations:** PCs uninterpretable economically; non-stationary; sensitive to correlation vs covariance choice; outlier sensitive.

**Millennium context:** PCA is starting point → refine with sector/style factors for interpretable alpha.

[🔝 Back to Top](#-table-of-contents)

---

### Q14 · SVD & Covariance Denoising (Marchenko-Pastur)

$$A = U\Sigma V^\top$$

**Noise eigenvalue band (Marchenko-Pastur):**

$$\lambda^\pm = \sigma^2\!\left(1\pm\sqrt{\frac{n}{T}}\right)^2$$

**Recipe:**
1. Compute empirical $\Sigma$
2. Eigenvalues $\lambda_i\in[\lambda^-,\lambda^+]$ → **noise** → replace with average
3. Eigenvalues $\lambda_i > \lambda^+$ → **signal** → keep
4. Reconstruct $\hat\Sigma = V\hat\Lambda V^\top$

**Finance link:** Denoised $\Sigma$ → stable portfolio weights → better OOS Sharpe.

[🔝 Back to Top](#-table-of-contents)

---

### Q15 · Condition Number & Numerical Stability

$$\kappa(A) = \frac{\sigma_{\max}}{\sigma_{\min}}$$

If $\kappa(A)=10^k$ → lose up to $k$ digits of precision.

**Preferred solvers (best → good):**

```
SVD (most stable) > QR (Householder) > LU (partial pivot) > direct inversion ✗
```

**Pseudoinverse for ill-conditioned least-squares:**
$$\hat x = V\Sigma^+ U^\top b \quad (\text{zero out } 1/\sigma_i \text{ for small } \sigma_i)$$

[🔝 Back to Top](#-table-of-contents)

---

## D · 🤖 Machine Learning

> **Section summary:** Bias-variance decomposition, L1 vs L2 geometry, walk-forward CV with embargo, tree models vs linear for alpha. Know the *finance-specific* failure modes: lookahead, non-stationarity, crowding, transaction costs.

[🔝 Back to Top](#-table-of-contents)

---

### Q16 · Bias-Variance Tradeoff & Regularisation

$$\mathbb{E}[(y-\hat f)^2] = \underbrace{\text{Bias}^2}_{\text{underfitting}} + \underbrace{\text{Var}}_{\text{overfitting}} + \underbrace{\sigma^2_\varepsilon}_{\text{irreducible}}$$

```
Error │  Bias²
      │╲
      │ ╲    Variance
      │  ╲  ╱
      │   ╲╱
      └────────────► Model Complexity
             ↑ sweet spot
```

| | L2 (Ridge) | L1 (Lasso) |
|---|---|---|
| Penalty | $\lambda\|\theta\|_2^2$ | $\lambda\|\theta\|_1$ |
| Effect | Shrinks all weights | **Exact zeros** (sparse) |
| Closed form? | Yes: $(X^\top X+\lambda I)^{-1}X^\top y$ | No (subgradient) |
| Use when | Many small effects | Feature selection needed |

**Alpha context:** L1 preferred for selecting relevant alpha factors from a large candidate set.

[🔝 Back to Top](#-table-of-contents)

---

### Q17 · Walk-Forward CV & Lookahead Bias

**Standard k-fold is invalid** for time series — leaks future data.

```
Walk-Forward:
Fold 1:  [──TRAIN──][TEST]
Fold 2:  [────TRAIN────][TEST]
Fold 3:  [──────TRAIN──────][TEST]
         + EMBARGO GAP (e.g. 21 days) between train end and test start
```

**Lookahead red flags:**
1. Backtest Sharpe $\gg$ paper trade Sharpe
2. Sharp PnL discontinuity at live launch
3. Feature at time $t$ uses data $> t$
4. Forward return label $[t, t+h]$ accidentally feeds feature at $t$

**Key:** PnL should *degrade gracefully* post-live, not cliff-edge.

[🔝 Back to Top](#-table-of-contents)

---

### Q18 · XGBoost vs. Linear Factor Model for Alpha

```
Alpha Task → Non-linearities? ──Yes──► XGBoost/LightGBM
                               │         + High expressivity
                               │         - Overfit, opaque, turnover↑
                               No──► Linear Factor Model
                                      + Interpretable, regime-stable
                                      - Misses non-linear interactions
```

**Use trees when:** Feature interactions matter, large feature set, classification (up/down).

**Use linear when:** Interpretable factor exposures needed, low data regime ($N<1000$, $T<5$yr).

**Key ML-alpha risks:** Non-stationarity, crowding (signal self-destructs), high-turnover signals unprofitable net of costs.

[🔝 Back to Top](#-table-of-contents)

---

## E · 🧠 Artificial Intelligence

> **Section summary:** NLP for earnings call sentiment (FinBERT, IC evaluation, lookahead from timestamp), Transformer modifications for financial TS. Know SOTA but also *scepticism about naïve application*.

[🔝 Back to Top](#-table-of-contents)

---

### Q19 · NLP Alpha from Earnings Calls

```
Transcripts → Preprocess → FinBERT/LLM Embeddings
                         → Sentiment Score per sentence
                         → Δ Sentiment vs prior quarter
                         → IC/Rank IC on forward 1-month returns
                         → Keep if IC > 0.02 & t-stat > 2
```

**Key signals:** Hedging language ("uncertain", "challenging"), management tone shift QoQ, analyst aggressiveness vs management defensiveness.

**Pitfalls:**
- **Lookahead:** Must use exact transcript release timestamp, not earnings date
- **Selection bias:** Companies holding calls ≠ universe
- **Model decay:** IR teams now "AI-optimise" language

[🔝 Back to Top](#-table-of-contents)

---

### Q20 · Transformers for Financial Time-Series

$$\text{Attention}(Q,K,V) = \text{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V$$

| NLP | Financial TS Modification |
|---|---|
| Discrete tokens | Patch/continuous embeddings |
| Causal mask | Point-in-time availability constraint |
| Internet-scale data | 5–20 years daily (tiny) |
| Transfer learning works | Severe domain shift |

**2026 SOTA:** PatchTST, Chronos (Amazon) — patch-based TS-native Transformers.

**Millennium stance:** Tree ensembles + linear models remain workhorses. Interviewers reward *awareness + scepticism*.

[🔝 Back to Top](#-table-of-contents)

---

## F · 💻 Coding

> **Section summary:** Pandas rolling windows (annualisation = $\times 252$ mean, $\times\sqrt{252}$ std), DP with state machines for sequential decisions, SQL LEFT JOIN + COALESCE pattern.

[🔝 Back to Top](#-table-of-contents)

---

### Q21 · Rolling Sharpe Ratio (Python)

```python
def rolling_sharpe(returns: pd.Series, window: int = 252) -> pd.Series:
    mu  = returns.rolling(window).mean()
    std = returns.rolling(window).std(ddof=1)
    return (mu * 252) / (std * np.sqrt(252))
```

**Annualisation:** mean $\times 252$, std $\times\sqrt{252}$. First $w-1$ values → `NaN` by design. $O(n)$ via C-level sliding window.

**Follow-up:** Missing returns → `fillna(0)` (assume no trade) or `dropna()` depending on strategy.

[🔝 Back to Top](#-table-of-contents)

---

### Q22 · Max Profit with Cooldown (LeetCode DP)

**States:** `hold`, `sold`, `rest`

$$\text{hold}[i] = \max(\text{hold}[i-1],\;\text{rest}[i-1]-p[i])$$
$$\text{sold}[i] = \text{hold}[i-1]+p[i]$$
$$\text{rest}[i] = \max(\text{rest}[i-1],\;\text{sold}[i-1])$$

```python
def max_profit_cooldown(prices):
    hold, sold, rest = -prices[0], 0, 0
    for p in prices[1:]:
        hold, sold, rest = max(hold, rest-p), hold+p, max(rest, sold)
    return max(sold, rest)
# [1,2,3,0,2] → 3
```

**Time:** $O(n)$ | **Space:** $O(1)$

[🔝 Back to Top](#-table-of-contents)

---

### Q23 · SQL LEFT JOIN with Zero-Fill

```sql
SELECT p.ProductId, p.Name, COALESCE(o.Quantity, 0) AS Quantity
FROM Product p
LEFT JOIN Orders o ON p.ProductId = o.ProductId
ORDER BY p.ProductId;
```

**Key pattern:** `LEFT JOIN` retains all products; `COALESCE(NULL, 0)` fills missing. HackerRank also tests the Pandas equivalent: `pd.merge(..., how='left').fillna(0)`.

[🔝 Back to Top](#-table-of-contents)

---

## G · 🔍 Reasoning

> **Section summary:** Kelly maximises log-wealth (not expected wealth); 100% betting → ruin with prob 1. Irrational numbers require randomisation for exact fairness.

[🔝 Back to Top](#-table-of-contents)

---

### Q24 · Kelly Criterion

**Setup:** Win $\$b=2$ with $p=0.6$, lose $\$1$ with $q=0.4$.

$$f^* = \frac{pb - q}{b} = \frac{0.6\times2 - 0.4}{2} = \frac{0.8}{2} = \mathbf{0.4}$$

**Bet 40% of bankroll.**

**Why not 100%?** $\mathbb{E}[\log W]$ at $f=1$: $(0.6)\ln(3)+(0.4)\ln(0)=-\infty$ → ruin probability = 1 (Breiman's theorem).

**Practice:** Funds use **half-Kelly** for robustness against model mis-specification.

[🔝 Back to Top](#-table-of-contents)

---

### Q25 · Pay $\pi$ at a Restaurant

$\pi\approx3.14159\ldots$ — no finite decimal is exact.

**Probabilistic rounding (unbiased):**
- Pay $\$3.14$ with prob $1-0.159\approx0.841$
- Pay $\$3.15$ with prob $0.159$
- Expected payment $= \pi$ exactly ✓

**Why this matters:** Tests understanding that irrational numbers require **randomisation** for exact expected-value fairness — linked to Von Neumann's binary expansion method (Q7).

[🔝 Back to Top](#-table-of-contents)

---

## H · 🧩 Brain Teasers

> **Section summary:** Set up state equations for HH (gets E=6), counter strategy for prisoners (O(n log n) visits), harmonic series diverges for ant problem. All three test comfort with divergent/recursive processes.

[🔝 Back to Top](#-table-of-contents)

---

### Q26 · Expected Flips to HH

**State equations:**

$$E = 1 + \tfrac{1}{2}E_H + \tfrac{1}{2}E, \qquad E_H = 1 + \tfrac{1}{2}(0) + \tfrac{1}{2}E$$

Solving: $E_H = 1 + E/2 \Rightarrow E = 1+\tfrac{1}{2}(1+E/2)+\tfrac{E}{2}$

$$\frac{E}{4} = \frac{3}{2} \implies \boxed{E = 6}$$

**Generalisation:** Expected flips for $k$ consecutive heads $= 2^{k+1}-2$.

[🔝 Back to Top](#-table-of-contents)

---

### Q27 · 100 Prisoners & Light Bulb

**Counter strategy:**
1. Designate 1 prisoner as **Counter C**
2. Every other prisoner: first time they see bulb **OFF** → turn it **ON** (once only, ever)
3. Counter C: every time C sees bulb **ON** → turn it **OFF**, increment count
4. When count = **99** → declare all visited

**Why it works:** Each non-counter contributes exactly 1 "ON" signal; C counts 99 distinct events.

**Complexity:** $O(n\log n)$ expected visits; terminates with probability 1.

[🔝 Back to Top](#-table-of-contents)

---

### Q28 · Ant on a Rubber Band

**Fraction of band covered after $n$ seconds:**

$$f_n = \sum_{k=1}^n \frac{0.01}{k} = 0.01\cdot H_n$$

Harmonic series **diverges** → $f_n\ge 1$ when $H_n\ge 100$:

$$\boxed{n \approx e^{100} \approx 2.7\times10^{43}\text{ seconds — but yes, the ant reaches the end}}$$

**Key insight:** Harmonic divergence even when individual terms vanish. Appears in random walk theory and barrier option problems.

[🔝 Back to Top](#-table-of-contents)

---

## I · ∑ Mathematical Induction

> **Section summary:** Clean base case → IH → inductive step with algebraic manipulation. Know the Fibonacci closed form's $\phi^2=\phi+1$ trick — it makes the inductive step trivial.

[🔝 Back to Top](#-table-of-contents)

---

### Q29 · Sum of First $n$ Integers

**Claim:** $\sum_{k=1}^n k = n(n+1)/2$

**Base:** $n=1$: $1=1\cdot2/2$ ✓

**Step ($n=m\to m+1$):**

$$\sum_{k=1}^{m+1}k = \frac{m(m+1)}{2} + (m+1) = (m+1)\cdot\frac{m+2}{2} = \frac{(m+1)(m+2)}{2}\;\checkmark\;\blacksquare$$

**Finance link:** Binomial tree path counting; order-book queue position analysis.

[🔝 Back to Top](#-table-of-contents)

---

### Q30 · Binet's Formula (Fibonacci Closed Form)

$$\boxed{F_n = \frac{\phi^n - \psi^n}{\sqrt{5}}}, \quad \phi=\frac{1+\sqrt5}{2},\;\psi=\frac{1-\sqrt5}{2}$$

**Key algebraic trick:** $\phi^2=\phi+1$ and $\psi^2=\psi+1$ (both roots of $x^2=x+1$) → $\phi^{m+1}=\phi^m+\phi^{m-1}$.

**Inductive step:**

$$G_{m+1} = \frac{\phi^{m+1}-\psi^{m+1}}{\sqrt5} = G_m + G_{m-1} = F_m+F_{m-1} = F_{m+1}\;\checkmark\;\blacksquare$$

**Finance link:** Fibonacci ratios in Elliott Wave; golden ratio in optimal stopping problems.

[🔝 Back to Top](#-table-of-contents)

---

## ⚡ Master Cheatsheet

> Read this the morning of the interview. One-line recall only.

| Concept | Formula / Result |
|---------|-----------------|
| **Bayes (coin jar)** | $P(D\mid H_{10})=1024/2023\approx50.6\%$ |
| **Max of $n$ Uniforms** | $\mathbb{E}[\max]=n/(n+1)$ |
| **Gambler's Ruin** | $P=(1-r^k)/(1-r^N)$; fair: $k/N$ |
| **Exp MLE** | $\hat\lambda=1/\bar x$ |
| **Cointegration test** | ADF on residuals of $P^A=\alpha+\beta P^B+\varepsilon$ |
| **GARCH persistence** | $\alpha+\beta$; long-run $\bar\sigma^2=\omega/(1-\alpha-\beta)$ |
| **Coin → prob $p$** | Binary expansion; compare flip to bit |
| **Itô correction** | $d\ln S=(\mu-\sigma^2/2)\,dt+\sigma\,dW$ |
| **BS PDE** | $V_t+\tfrac{1}{2}\sigma^2S^2V_{SS}+rSV_S-rV=0$ |
| **Heston** | $dv=\kappa(\theta-v)\,dt+\xi\sqrt v\,dW^v$; corr $\rho$ |
| **FTAP** | No-arb ↔ $\exists\mathbb{Q}$; complete ↔ unique $\mathbb{Q}$ |
| **Vasicek bond** | $P=e^{A(\tau)-B(\tau)r}$; $B=(1-e^{-\kappa\tau})/\kappa$ |
| **PCA variance** | $\text{EVR}(k)=\sum_{i=1}^k\lambda_i/\sum\lambda_i$ |
| **M-P noise band** | $\lambda^\pm=\sigma^2(1\pm\sqrt{n/T})^2$ |
| **Condition number** | $\kappa=\sigma_{\max}/\sigma_{\min}$; lose $\log_{10}\kappa$ digits |
| **Bias-Variance** | $\text{MSE}=\text{Bias}^2+\text{Var}+\sigma^2_\varepsilon$ |
| **L1 vs L2** | L1→sparsity; L2→ridge closed form $(X^\top X+\lambda I)^{-1}X^\top y$ |
| **Rolling Sharpe** | $(\mu\times252)/(\sigma\times\sqrt{252})$ |
| **Cooldown DP** | States: hold/sold/rest; $O(n)$ time, $O(1)$ space |
| **Kelly** | $f^*=(pb-q)/b$; 100% → ruin (Breiman) |
| **Probabilistic $\pi$** | Pay \$3.15 w.p. 0.159; \$3.14 w.p. 0.841; $\mathbb{E}=\pi$ |
| **E[HH flips]** | $E=6$; gen: $2^{k+1}-2$ |
| **Prisoners** | Counter strategy; $O(n\log n)$ visits |
| **Ant on band** | $f_n=0.01 H_n$; harmonic diverges; reaches end at $e^{100}$s |
| **Sum induction** | $\sum k=n(n+1)/2$; step: factor $(m+1)$ |
| **Binet** | $F_n=(\phi^n-\psi^n)/\sqrt5$; use $\phi^2=\phi+1$ |

[🔝 Back to Top](#-table-of-contents)

---

## 🎯 Core Pitch for Millennium Systematic Macro Role

> *"I deliver alpha research across systematic macro — commodities, FX, equity and bond futures — combining rigorous statistical analysis (Bayesian updating, GARCH, cointegration) with ML techniques (walk-forward CV, tree ensembles, NLP signals) and clean Python implementation. I understand that alpha decay, lookahead bias, and transaction costs destroy most signals that look good in backtest — so I build research frameworks designed to survive live execution friction, not just in-sample."*

[🔝 Back to Top](#-table-of-contents)

---
