# Set 6 — Statistics & Quantitative Reasoning
**Total time budget: ~15 minutes** (heaviest math-derivation set alongside Set 3 — expect mathcha for formulas).

---
---

[↩️ Back to README.md](./README.md)

---
---

## 📑 Table of Contents

- [§1 · **Q1. How would you statistically test whether one execution algorithm produces lower slippage than another?**](#q1-how-would-you-statistically-test-whether-one-execution-algorithm-produces-lower-slippage-than-another)
- [§2 · **Q2. Explain heteroskedasticity in trading-cost data and how you'd correct standard errors (e.g., Newey-West).**](#q2-explain-heteroskedasticity-in-trading-cost-data-and-how-youd-correct-standard-errors-eg-newey-west)
- [§3 · **Q3. How would you fit a market-impact model relating order size to price impact, and what functional form would you use?**](#q3-how-would-you-fit-a-market-impact-model-relating-order-size-to-price-impact-and-what-functional-form-would-you-use)
- [§4 · **Q4. Explain multicollinearity in a cost-attribution regression with correlated explanatory variables (size, volatility, spread).**](#q4-explain-multicollinearity-in-a-cost-attribution-regression-with-correlated-explanatory-variables-size-volatility-spread)
- [§5 · **Q5. How do you separate temporary vs permanent market impact empirically?**](#q5-how-do-you-separate-temporary-vs-permanent-market-impact-empirically)
- [§6 · **Q6. What statistical approach would you use to detect a regime shift in trading costs (e.g., pre/post a liquidity event)?**](#q6-what-statistical-approach-would-you-use-to-detect-a-regime-shift-in-trading-costs-eg-prepost-a-liquidity-event)
- [§7 · **Q7. How would you determine the appropriate sample size/lookback window for a TCA benchmark to be statistically meaningful?**](#q7-how-would-you-determine-the-appropriate-sample-sizelookback-window-for-a-tca-benchmark-to-be-statistically-meaningful)
- [§8 · **Q8. Explain the difference between correlation and causation in the context of "high volatility causes high slippage."**](#q8-explain-the-difference-between-correlation-and-causation-in-the-context-of-high-volatility-causes-high-slippage)
- [§9 · **Q9. How would you communicate a cost-model result with wide confidence intervals to a non-technical PM?**](#q9-how-would-you-communicate-a-cost-model-result-with-wide-confidence-intervals-to-a-non-technical-pm)
- [§10 · **Q10. Describe how you'd validate a trading-cost model out-of-sample before rolling it out firm-wide.**](#q10-describe-how-youd-validate-a-trading-cost-model-out-of-sample-before-rolling-it-out-firm-wide)

[🔝 Back to Top](#-table-of-contents)

---

## Q1. How would you statistically test whether one execution algorithm produces lower slippage than another?

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Are the two algos' trades naturally paired (same orders split A/B) or observational (different orders happened to use different algos)?" (Materially changes the right test.)

**C) Detailed answer — write on mathcha:**
> "If assignment is **randomized/paired** (e.g., an A/B test alternating algo choice for similar order characteristics), a **paired t-test** on the slippage difference is appropriate:
> 
> $$t = \frac{\bar{d}}{s_d/\sqrt{n}}, \quad d_i = \text{slippage}_{A,i} - \text{slippage}_{B,i}$$
> 
> where $\bar d$ is the mean paired difference and $s_d$ its sample standard deviation.
>
> If assignment is **observational** (algos used non-randomly, e.g., algo choice correlated with order size/urgency), a raw two-sample t-test is confounded — instead I'd run a regression controlling for order characteristics:
> $$\text{slippage}_i = \beta_0 + \beta_1 \cdot \mathbb{1}[\text{Algo A}]_i + \beta_2 \, \text{size}_i + \beta_3 \, \text{urgency}_i + \varepsilon_i$$
> and test $H_0: \beta_1 = 0$ using **Newey-West standard errors** (Q2) since trading-cost residuals are typically heteroskedastic and autocorrelated. I'd also check the effect size, not just the p-value — statistical significance with a trivial economic magnitude (e.g., 0.1bps) isn't actionable even if $p<0.05$ given large sample sizes typical of execution data."

**D) Feynman summary:** If you can literally flip a coin to decide which algo trades each order, a simple paired comparison is enough — the coin flip already removed the confounders. If the algo choice wasn't randomized, you have to statistically remove the confounders yourself via regression before the comparison means anything.

**E) Follow-ups:**
- *"Why prefer regression over a simple two-sample comparison in the observational case?"* → A raw two-sample comparison silently assumes the two algo's order populations are otherwise identical — regression explicitly controls for the ways they differ (size, urgency) so the coefficient isolates the algo effect.

[🔝 Back to Top](#-table-of-contents)

---

## Q2. Explain heteroskedasticity in trading-cost data and how you'd correct standard errors (e.g., Newey-West).

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** none needed — direct technical ask, answer immediately.

**C) Detailed answer — write on mathcha:**
> "**Heteroskedasticity**: the variance of the regression error $\varepsilon_i$ is not constant across observations — in trading-cost data this is almost guaranteed, since slippage variance scales with order size and volatility (larger/more volatile orders have both higher *and* more variable cost). Formally, $\text{Var}(\varepsilon_i) = \sigma_i^2$ depends on $i$ rather than being a constant $\sigma^2$.
>
> Ordinary OLS standard errors assume $\text{Var}(\varepsilon_i) = \sigma^2$ (homoskedastic) — under heteroskedasticity, OLS coefficients remain unbiased but the standard errors are wrong (typically understated), inflating false confidence (Type I error).
>
> Trading-cost data is *also* typically **autocorrelated** — consecutive orders during a persistent liquidity regime have correlated costs — so a simple heteroskedasticity-robust (White) correction isn't enough; **Newey-West** corrects for both simultaneously:
>
> $$\hat{V}_{NW} = \hat{V}_{OLS} + \sum_{l=1}^{L} w_l \left( \hat{\Gamma}_l + \hat{\Gamma}_l' \right)$$
>
> where $\hat\Gamma_l$ is the lag-`l` autocovariance of the score/residual terms, $w_l = 1 - \frac{l}{L+1}$ (Bartlett kernel weights), and $L$ is the chosen maximum lag (rule of thumb: $L \approx 4(n/100)^{2/9}$, or set by the expected autocorrelation horizon in the data, e.g., the roll-window length)."

**D) Feynman summary:** Regular OLS standard errors assume every observation's noise is equally sized and independent of its neighbors — trading-cost data violates both: big orders are noisier than small ones, and a bad liquidity day makes today's cost *and* tomorrow's cost both elevated together. Newey-West is a correction that widens your error bars appropriately for both violations so you don't fool yourself into false confidence.

**E) Follow-ups:**
- **"How do you choose the lag length $L$ in practice?"** → Start with the rule-of-thumb formula, but sanity-check against the data's actual autocorrelation function (e.g., ACF of residuals) — if cost autocorrelation persists further out (e.g., multi-day liquidity regimes), extend $L$ accordingly.

[🔝 Back to Top](#-table-of-contents)

---

## Q3. How would you fit a market-impact model relating order size to price impact, and what functional form would you use?

**A) Time budget:** 2 minutes (overlaps **[Set 3 → Q5. Describe how you would fit a market impact model using historical futures execution data.](./ANSWER3.md#q5-describe-how-you-would-fit-a-market-impact-model-using-historical-futures-execution-data)** — keep this one focused on functional-form choice/statistical fitting, defer full pipeline detail to **Set 3 Q5** if repeated).

**B) Follow-ups:** "This overlaps a question from the TCA set — would you like the full data-pipeline answer again, or just the functional-form/statistical reasoning here?"

**C) Detailed answer:**
> "As in **Set 3 Q5**, I'd use the empirically well-supported **square-root law**: $\text{Impact} = \eta\,\sigma\,(Q/ADV)^{\beta}$ with $\beta \approx 0.5$, fit via log-linear OLS with Newey-West errors (Q2). The key statistical reasoning for *why* square-root rather than linear: a linear model $(\beta=1)$ implies impact cost scales proportionally with size with no diminishing effect, which is empirically rejected — large orders' *marginal* impact per unit size actually shrinks (though total impact still rises), consistent with $\beta<1$. I'd let $\beta$ be a **free parameter estimated from the data** rather than hard-coding 0.5, and test $H_0: \beta = 0.5$ against the fitted estimate to see whether the classic square-root law holds for this specific futures universe or needs adjustment — different asset classes/liquidity regimes have shown estimated $\beta$ anywhere roughly in the 0.4–0.6 range in various empirical impact studies."

**D) Feynman summary:** Don't just assume the textbook exponent is correct for your data — treat it as a hypothesis to test, fit the exponent freely, and let the data tell you if this market's impact curve is flatter or steeper than the textbook 0.5.

**E) Follow-ups:**
- **"What if the fitted $\beta$ is close to 1 (linear)?"** → Investigate whether the sample is dominated by a regime with unusually thin liquidity (impact doesn't saturate/diminish as quickly) or whether there's a data issue (e.g., large orders systematically executed during adverse conditions, biasing the fit) before accepting a linear-like result.

[🔝 Back to Top](#-table-of-contents)

---

## Q4. Explain multicollinearity in a cost-attribution regression with correlated explanatory variables (size, volatility, spread).

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer — write on mathcha:**
> "Multicollinearity: when explanatory variables (order size, volatility, bid-ask spread) are themselves highly correlated with each other, individual regression coefficients become unstable and hard to interpret — the regression can't cleanly attribute the *shared* variance to one variable versus another. Formally, if $X$ is the design matrix, near-collinearity means $X'X$ is close to singular, so $(X'X)^{-1}$ has very large entries, inflating coefficient variance:
> $$\text{Var}(\hat\beta_j) = \frac{\sigma^2}{(1-R_j^2)\sum_i(x_{ij}-\bar x_j)^2}$$
> where $R_j^2$ is the R-squared from regressing $x_j$ on all other regressors — as $R_j^2 \to 1$ (near-perfect collinearity), $\text{Var}(\hat\beta_j) \to \infty$. This is quantified via the **Variance Inflation Factor**: $VIF_j = \frac{1}{1-R_j^2}$; $VIF_j > 10$ (or a more conservative $>5$) is a common rule-of-thumb concern threshold.
>
> **Practical handling in a cost-attribution context**: (1) check VIFs before trusting individual coefficients; (2) if size and volatility are highly correlated (larger orders often placed in higher-vol regimes), consider an interaction term or orthogonalize (e.g., regress size on volatility, use the residual 'size-adjusted-for-vol' as the regressor) rather than naively including both; (3) shift focus from individual coefficient interpretation to overall model predictive power (out-of-sample $R^2$) if the goal is prediction rather than causal attribution; (4) use regularization (Ridge) if the goal is a stable predictive model despite correlated regressors, accepting biased-but-stable coefficients over unbiased-but-wild ones."

**D) Feynman summary:** If two of your explanatory variables move almost in lockstep, the regression can't tell which one is "really" responsible for the outcome — it's like trying to figure out whether height or shoe size predicts basketball skill when nearly everyone tall also has big feet; you need either more varied data or a different modeling approach to pull them apart.

**E) Follow-ups:**
- *"When would you choose Ridge over dropping a correlated variable?"* → When both variables are theoretically meaningful and you care about prediction more than individual-coefficient interpretability — Ridge shrinks and stabilizes without arbitrarily discarding a variable that carries some genuine signal.

[🔝 Back to Top](#-table-of-contents)

---

## Q5. How do you separate temporary vs permanent market impact empirically?

**A) Time budget:** 2.5 minutes.

**B) Follow-ups:** "Should I frame this via the Almgren-Chriss decomposition from **[Set 3 → Q9. Explain Almgren-Chriss market impact modeling and its assumptions relative to futures liquidity.](./ANSWER3.md#q9-explain-almgren-chriss-market-impact-modeling-and-its-assumptions-relative-to-futures-liquidity)**, or purely as an empirical/event-study measurement approach?"

**C) Detailed answer — write on mathcha:**
> "Empirically, I'd use a **post-trade price-reversion event study**: measure the price at execution time $P_{exec}$, then track price at increasing horizons after the trade, $P_{t+\Delta}$ for $\Delta = 1\text{min}, 5\text{min}, 30\text{min}, \ldots$, relative to the pre-trade reference price $P_{pre}$ (e.g., arrival price):
> $$\text{Impact}(\Delta) = P_{t+\Delta} - P_{pre}$$
> As $\Delta \to \infty$ (or to some 'settled' horizon), $\text{Impact}(\Delta)$ converges to the **permanent** component $\gamma$; the **temporary** component is the difference between the immediate execution impact and that settled level:
> $$\text{Temporary Impact} = \text{Impact}(\Delta=0) - \text{Impact}(\Delta \to \infty)$$
> Practically, I'd average this reversion curve across many trades (grouped by size/side) to get a stable estimate, since any single trade's post-trade price path is dominated by noise — the *average* reversion curve across hundreds/thousands of similar trades is what reveals the temporary-vs-permanent split cleanly. This connects directly to the Almgren-Chriss model structure (**Set 3 Q9**): the settled/asymptotic impact level is the model's permanent impact parameter $\gamma \cdot v$, and the immediate-minus-settled gap is the temporary impact $\eta \cdot v$."

**D) Feynman summary:** After you push the market by trading, some of that push is permanent (the market genuinely re-prices based on the information your trade revealed) and some fades away once you stop pushing (temporary, mechanical impact from consuming liquidity). You measure the split by watching the price in the minutes/hours after the trade and seeing how much of the initial move survives versus reverts.

**E) Follow-ups:**
- *"How long a horizon is 'settled'?"* → Depends on the instrument's liquidity — for liquid front-month futures, often tens of minutes; less liquid contracts may need hours; I'd choose it empirically by finding where the average reversion curve visibly plateaus.

[🔝 Back to Top](#-table-of-contents)

---

## Q6. What statistical approach would you use to detect a regime shift in trading costs (e.g., pre/post a liquidity event)?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Are you thinking of a known, dated event (e.g., a specific liquidity shock) or an unknown changepoint we need to discover in the data?"

**C) Detailed answer:**
> "For a **known** event date, a straightforward **structural break test** (Chow test) comparing regression fits before vs. after the date, or simply a difference-in-difference-style comparison of mean slippage pre/post with Newey-West-adjusted confidence intervals (Q2), is sufficient and interpretable.
>
> For an **unknown** changepoint, I'd use a **changepoint detection algorithm** — e.g., CUSUM (cumulative sum control chart) on the slippage series, or a Bayesian/PELT (Pruned Exact Linear Time) changepoint detection method — to statistically identify where the underlying mean/variance of trading costs shifted, without needing to pre-specify the date. I'd validate any detected changepoint isn't just noise by checking its significance against a bootstrap null distribution (resampling under the no-changepoint assumption) before treating it as a real regime shift worth alerting a PM about."

**D) Feynman summary:** If you already know when the liquidity event happened, just cleanly compare the "before" and "after" periods statistically. If you don't know when things changed, use a changepoint algorithm that scans the whole series looking for the most likely place the underlying behavior shifted — and then check that the shift it found isn't something that could plausibly have happened by chance alone.

**E) Follow-ups:**
- *"Would you alert a PM automatically when CUSUM flags something?"* → No — auto-flag internally for investigation first (avoid false-positive noise reaching PMs), and only escalate after confirming statistical and economic significance, consistent with communication discipline specific to the question → **[Set 3 → Q8. How would you present a TCA finding that shows a PM's execution cost is worse than peers, without being adversarial?](./ANSWER3.md#q8-how-would-you-present-a-tca-finding-that-shows-a-pms-execution-cost-is-worse-than-peers-without-being-adversarial)**.

[🔝 Back to Top](#-table-of-contents)

---

## Q7. How would you determine the appropriate sample size/lookback window for a TCA benchmark to be statistically meaningful?

**A) Time budget:** 2 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer — write on mathcha:**
> "I'd do a **power calculation** rather than picking an arbitrary window. Given the observed (or estimated) standard deviation of per-trade slippage $\sigma$, and a minimum economically-meaningful effect size $\delta$ (e.g., 'we care about detecting a 2bps difference'), the required sample size for a two-sample comparison at significance level $\alpha$ and power $1-\beta$ is approximately:
> $$n \approx \frac{2\,\sigma^2\,(z_{\alpha/2} + z_{\beta})^2}{\delta^2}$$
> I'd compute this using the *actual* observed dispersion of slippage for the relevant instrument/strategy (since futures slippage variance differs hugely by liquidity), then translate that trade-count requirement into a calendar lookback window given typical trade frequency — e.g., if $n=500$ trades are needed and the desk executes ~20 relevant trades/day, that implies roughly a 25-trading-day window as a floor, not an arbitrary 'last month' choice."

**D) Feynman summary:** Don't just pick '30 days' because it sounds reasonable — work backwards from how big a cost difference actually matters to the business and how noisy individual trades are, and let that arithmetic tell you the minimum number of trades (and hence minimum time window) needed to trust a conclusion.

**E) Follow-ups:**
- *"What if the instrument trades too infrequently to hit the required n in a reasonable window?"* → Be explicit about the resulting low statistical power — either widen the window and accept a longer feedback loop, pool across similar instruments/strategies if defensible, or report the finding with appropriately wide confidence intervals rather than false precision.

[🔝 Back to Top](#-table-of-contents)

---

## Q8. Explain the difference between correlation and causation in the context of "high volatility causes high slippage."

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> "Observing that periods of high volatility co-occur with high slippage is a **correlational** finding — it doesn't by itself prove volatility is the *cause*. There's a very plausible causal mechanism here ( wider spreads and thinner order books during high-vol periods mechanically raise execution costs — consistent with the impact model's explicit $\sigma$ term in **[Q3](#q3-how-would-you-fit-a-market-impact-model-relating-order-size-to-price-impact-and-what-functional-form-would-you-use)** / **[Set 3 → Q5. Describe how you would fit a market impact model using historical futures execution data.](./ANSWER3.md#q5-describe-how-you-would-fit-a-market-impact-model-using-historical-futures-execution-data)** ), so causation is *believable*, but I'd still be careful about confounders: high volatility often coincides with other things that independently raise cost — e.g., PMs trading more urgently during volatile periods ( urgency itself raises impact, **[Set 3 → Q6. How would you attribute slippage across a portfolio manager group to identify a systemic cost driver?](./ANSWER3.md#q6-how-would-you-attribute-slippage-across-a-portfolio-manager-group-to-identify-a-systemic-cost-driver)** ), or wider bid-ask spreads acting as a separate, correlated channel. A cleaner causal read comes from controlling for urgency/order-size in a regression ( **[Q1](#q1-how-would-you-statistically-test-whether-one-execution-algorithm-produces-lower-slippage-than-another)**, **[Q4](#q4-explain-multicollinearity-in-a-cost-attribution-regression-with-correlated-explanatory-variables-size-volatility-spread)** ) and checking that the volatility coefficient remains significant *after* controlling for those confounders — that's stronger evidence than the raw correlation alone, though even then it's not a randomized experiment, so I'd describe the conclusion as 'consistent with a causal mechanism' rather than definitively proven."

**D) Feynman summary:** Volatility and slippage rising together doesn't tell you volatility is pulling the strings — it might be, but urgency and spread-widening could be pulling on both at once; you get more confidence in the causal story once you've statistically removed the other plausible common causes, but a regression is never quite as convincing as a true randomized experiment.

**E) Follow-ups:**
- *"How would you get closer to a true causal test?"* → A natural experiment or quasi-random variation (e.g., comparing execution cost around scheduled, pre-known volatility events like economic releases, where the timing is exogenous to any individual PM's decision) gets closer to causal identification than pure observational correlation.

[🔝 Back to Top](#-table-of-contents)

---

## Q9. How would you communicate a cost-model result with wide confidence intervals to a non-technical PM?

**A) Time budget:** 1.5 minutes.

**B) Follow-ups:** none needed.

**C) Detailed answer:**
> "I'd avoid jargon like 'confidence interval' and translate it into decision-relevant language: 'based on the data we have, we estimate your execution cost is roughly X bps, but given how much cost varies trade-to-trade, the true number could reasonably be anywhere between Y and Z — that range is wide enough that I wouldn't recommend acting on the point estimate alone yet.' I'd pair this with a concrete next step rather than leaving it as an ambiguous shrug: either 'here's what more data/time would narrow this down' or 'here's a lower-uncertainty finding we *can* act on today' ( e.g., the fee/explicit-cost breakdown from **[Set 3 → Q10. How do give-up fees, exchange fees, and clearing costs factor into total cost of trading?](./ANSWER3.md#q10-how-do-give-up-fees-exchange-fees-and-clearing-costs-factor-into-total-cost-of-trading)**, which has no statistical uncertainty at all ). The goal is honesty about uncertainty without leaving the PM without any actionable takeaway."

**D) Feynman summary:** Translate 'wide confidence interval' into plain English as 'we're genuinely not sure yet, and here's the range of plausible truth' — and always follow it with either a path to more certainty or a different, more certain finding they can act on instead, so uncertainty doesn't read as unhelpfulness.

**E) Follow-ups:**
- *"What if the PM pushes for a single number anyway?"* → Give the point estimate, but explicitly caveat that treating it as precise could lead to a wrong decision — resist the pressure to manufacture false precision.

[🔝 Back to Top](#-table-of-contents)

---

## Q10. Describe how you'd validate a trading-cost model out-of-sample before rolling it out firm-wide.

**A) Time budget:** 2 minutes.

**B) Follow-ups:** "Should this cover just statistical validation, or also the operational rollout process (phased deployment, monitoring)?"

**C) Detailed answer:**
> "Statistical validation: (1) **time-based train/test split** — never a random split for time-series trading-cost data ( leaks future information into training, **[Set 3 → Q5. Describe how you would fit a market impact model using historical futures execution data.](./ANSWER3.md#q5-describe-how-you-would-fit-a-market-impact-model-using-historical-futures-execution-data)** ); hold out the most recent period(s) as a genuine forward test; (2) check **prediction error metrics** (RMSE, MAE of predicted vs. realized cost) on the held-out set, not just in-sample $R^2$; (3) test **stability across sub-periods/regimes** — does the model's error stay reasonable across different volatility regimes (**[Q6](#q6-what-statistical-approach-would-you-use-to-detect-a-regime-shift-in-trading-costs-eg-prepost-a-liquidity-event)**), or does it break down exactly when it matters most (stressed markets)?; (4) check for **calibration**, not just point accuracy — if the model produces a predicted cost distribution/interval, verify the realized outcomes fall within stated intervals at roughly the stated rate.
> Operationally: (5) **phased rollout** — deploy to one PM group / one asset class first, monitor live prediction error against realized cost for a defined burn-in period, before extending firm-wide; (6) maintain a **fallback/kill-switch** — if live monitored error exceeds a pre-agreed threshold, revert to the prior model/methodology rather than silently degrading every PM's cost reporting."

**D) Feynman summary:** Out-of-sample validation for a cost model means testing it the way it will actually be used — predicting the future from the past, not shuffled data — and then rolling it out cautiously in stages with a way to notice quickly (and revert) if it behaves worse in the live world than it did in backtesting.

**E) Follow-ups:**
- *"What would trigger you to pull the model back after rollout?"* → A sustained, statistically significant divergence between predicted and realized cost beyond the pre-agreed threshold (Q7's power-calculation logic applied to *monitoring*, not just initial validation) — not a single bad day, which could just be normal variance.

[🔝 Back to Top](#-table-of-contents)

---
