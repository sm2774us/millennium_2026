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