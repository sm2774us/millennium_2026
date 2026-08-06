## 🧮 Comprehensive Quantitative Finance Equation Engine

### 1. Market Structure, Pricing & Futures Mechanics

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **TAS Contract Pricing**<br> | $`P_{\text{TAS}} = S_{\text{settle}} + \delta`$<br> | • $`P_{\text{TAS}}`$: Execution fill price<br><br>• $`S_{\text{settle}}`$: Official closing settlement price<br><br>• $`\delta`$: Agreed basis differential | Locks execution price relative to the exchange settlement, eliminating intraday price volatility while introducing benchmark settlement risk. |
| **Variation Margin (VM)**<br> | $`\text{VM}_t = (F_t - F_{t-1}) \times M \times N`$<br> | • $`\text{VM}_t`$: Daily MTM cash flow<br><br>• $`F_t, F_{t-1}`$: Futures settlement prices<br><br>• $`M`$: Contract multiplier<br><br>• $`N`$: Contract quantity | Resets contract present value to zero daily, creating intraday cash funding demands distinct from OTC forward contracts. |
| **Annualized Futures Roll Yield**<br> | $`\text{Roll Yield} \approx -\frac{F(t,T_{\text{next}}) - F(t,T_{\text{front}})}{F(t,T_{\text{front}})} \times \frac{365}{T_{\text{next}}-T_{\text{front}}}`$<br> | • $`F(t,T)`$: Futures price at time $`t`$ for maturity $`T`$<br><br>• $`T_{\text{next}}, T_{\text{front}}`$: Expiry dates | Measures contango drag or backwardation yield when systematically rolling expiring futures positions via calendar spreads. |

---

### 2. Transaction Cost Analysis (TCA) & Implementation Shortfall (IS)

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Implementation Shortfall (Perold 1988)**<br> | $`\text{IS} = d \cdot \left[ (P_{\text{fill,avg}} - P_{\text{arrival}}) Q_{\text{filled}} + (P_{\text{final}} - P_{\text{arrival}}) Q_{\text{unfilled}} \right]`$<br> | • $`d \in \{+1, -1\}`$: Buy/Sell sign<br><br>• $`P_{\text{arrival}}`$: Benchmark mid-price<br><br>• $P_{\text{fill,avg}}$: VWAP of executed fills<br><br>• $Q_{\text{filled}}, Q_{\text{unfilled}}$: Executed vs unfilled size | Core benchmark quantifying execution friction against paper portfolio returns, penalizing market impact and opportunity cost. |
| **Four-Way IS Cost Decomposition**<br> | $`\text{IS} = \text{Delay Cost} + \text{Explicit Fees} + \text{Realized Impact} + \text{Opportunity Cost}`$<br> | • $`\text{Delay Cost} = d \cdot (P_{\text{release}} - P_{\text{decision}}) Q_{\text{parent}}`$<br><br>• $`\text{Explicit Fees}`$: Commissions, clearing, exchange fees| Isolates operational latency, broker commissions, active execution impact, and residual price drift. |
| **Slippage Attribution Waterfall**<br> | $`\text{Slippage} = d \cdot \left[ (P_{\text{release}} - P_{\text{decision}}) + (P_{\text{fill,avg}} - P_{\text{release}}) + (P_{\text{close}} - P_{\text{fill,avg}}) \right]`$<br> | • $P_{\text{decision}}$: PM decision mark<br><br>• $`P_{\text{release}}`$: Order release timestamp<br><br>• $`P_{\text{close}}`$: Session close mark | Decomposes pre-trade routing delay, execution trading friction, and post-trade adverse price selection. |
| **Normalized IS Components (bps)**<br> | $`\text{IS}_{\text{bps}} = \frac{Q_{\text{filled}} \cdot \text{ExecCost}_{\text{bps}} + Q_{\text{unfilled}} \cdot \text{OppCost}_{\text{bps}}}{Q_{\text{parent}}}`$<br><br>$`\text{ExecCost}_{\text{bps}} = d \cdot 10^4 \cdot \frac{P_{\text{fill,avg}} - P_{\text{arrival}}}{P_{\text{arrival}}}`$<br> | • $`\text{IS}_{\text{bps}}`$: Total shortfall in basis points<br><br>• $`Q_{\text{parent}}`$: Total parent order quantity | Standardizes relative execution cost metrics across multi-asset portfolios and varying contract scales. |

---

### 3. Market Impact & Optimal Execution Trajectories

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Kyle’s Lambda Microstructure Model** | $`\Delta P_t = \lambda \cdot y_t + \epsilon_t`$<br>
<br><br>$`\lambda = \frac{\text{Cov}(\Delta P, y)}{\text{Var}(y)} = \frac{\sigma_v}{2 \sigma_u}`$ | • $`\Delta P_t`$: Price change over interval $`t`$<br><br>• $`y_t`$: Net order flow (informed + noise)<br><br>• $`\sigma_v`$: Variance of asset fundamental value<br><br>• $\sigma_u$: Variance of noise trader order flow | Measures market illiquidity and adverse selection cost. Essential for sizing small orders, passive market-making, and HFT liquidity routing algorithms. |
| **Empirical Square-Root Market Impact Law**<br> | $`\Delta P = \sigma \cdot Y \cdot \sqrt{\frac{Q}{V}}`$<br> | • $`\Delta P`$: Percentage price impact<br><br>• $`\sigma`$: Daily volatility<br><br>• $`Y \in [0.5, 0.7]`$: Empirical constant<br><br>• $`Q`$: Parent volume; $`V`$: ADV | Estimates price displacement for parent order execution and pre-trade strategy participation rate scaling. |
| **Almgren-Chriss Total Cost Objective**<br> | $`\text{Total Cost} = \int_0^T \eta \dot{x}(t)^2 dt + \frac{1}{2}\gamma X^2 + \lambda \int_0^T \sigma^2 x(t)^2 dt`$<br> | • $`x(t)`$: Inventory trajectory<br><br>• $`\dot{x}(t)`$: Trading velocity<br><br>• $`\eta, \gamma`$: Temporary & permanent impact parameters<br><br>• $`\lambda`$: Risk aversion coefficient | Balances temporary trading impact costs against inventory volatility exposure over an execution schedule. |
| **Almgren-Chriss Optimal Trajectory (Single Asset)**<br> | $`x(t) = X \cdot \frac{\sinh\left(\kappa(T-t)\right)}{\sinh(\kappa T)}`$<br><br>$`\kappa = \sqrt{\frac{\lambda \sigma^2}{\eta}}`$<br> | • $`X`$: Initial inventory $`x(0)`$<br><br>• $`T`$: Execution horizon<br><br>• $`\kappa`$: Urgency parameter | Analytical solution for continuous inventory liquidation. Higher urgency $`\kappa`$ accelerates liquidation to avoid volatility risk. |
| **Almgren-Chriss Trajectory (Multi-Asset Matrix System)**<br> | $`\mathbf{x}(t) = \sinh\left(\boldsymbol{\Gamma}(T-t)\right) \left[\sinh(\boldsymbol{\Gamma} T)\right]^{-1} \mathbf{x}(0)`$<br><br>$`\boldsymbol{\Gamma} = \sqrt{\lambda \boldsymbol{\eta}^{-1} \boldsymbol{\Sigma}}`$<br> | • $`\mathbf{x}(t)`$: Inventory vector<br><br>• $`\boldsymbol{\Sigma}`$: Covariance matrix<br><br>• $`\boldsymbol{\eta}`$: Temporary impact matrix | Generalizes optimal execution to multi-asset portfolios, incorporating asset correlations and cross-asset market impact dynamics. |

---

### 4. Microstructure Metrics, Aggregations & Risk Signals

| Domain / Context | Final Form Equation | Validated Parameters & Definitions | Buy-Side Desk Relevance |
| --- | --- | --- | --- |
| **Order Book Micro-Price**<br> | $`P_{\text{micro}} = \frac{P_{\text{bid}} \cdot S_{\text{ask}} + P_{\text{ask}} \cdot S_{\text{bid}}}{S_{\text{bid}} + S_{\text{ask}}}`$<br> | • $`P_{\text{bid}}, P_{\text{ask}}`$: Bid & Ask prices<br><br>• $`S_{\text{bid}}, S_{\text{ask}}`$: Displayed sizes | Size-weighted fair value indicator predicting short-term order book directional movement. |
| **Volume-Weighted Average Price (VWAP)**<br> | $`P_{\text{VWAP}} = \frac{\sum_{i=1}^N P_i \cdot Q_i}{\sum_{i=1}^N Q_i}`$<br> | • $`P_i`$: Trade fill price<br><br>• $`Q_i`$: Trade fill volume | Standard benchmark evaluating execution performance against volume-weighted market activity. |
| **Time-Weighted Average Price (TWAP)**<br> | $`P_{\text{TWAP}} = \frac{\sum_{i=1}^N P_i \cdot \Delta t_i}{\sum_{i=1}^N \Delta t_i}`$<br> | • $`P_i`$: Prevailing price<br><br>• $`\Delta t_i`$: Duration interval | Measures time-uniform execution quality across designated market windows. |
| **Peak-to-Trough Running Drawdown**<br> | $`\text{Drawdown}_t = \frac{P_t - \max_{\tau \le t} P_\tau}{\max_{\tau \le t} P_\tau} \times 100\%`$<br> | • $P_t$: Equity / asset value at time $t$<br><br>• $`\max_{\tau \le t} P_\tau`$: Historical peak value | Real-time risk management metric tracking peak-to-trough capital erosion. |