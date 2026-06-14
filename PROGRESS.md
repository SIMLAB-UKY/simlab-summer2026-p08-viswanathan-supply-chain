# Project 08: Critical Minerals Supply Chain (Mine-to-Market Logistics)
**Researcher:** Soumya Gupta
**Branch:** `Soumya_Gupta_P08`  

---

## Executive Summary of Progress
Over Weeks 2 and 3, our objective was to bridge the theoretical gap between traditional, isolated mine-site extraction and the broader, volatile downstream supply chain. Moving away from reactive, deterministic fleet management, We built a foundation for a proactive, intelligent Fleet Management System (FMS) by first mathematically modeling logistical chaos (Week 2), and then engineering Machine Learning models to predict it (Week 3).

---

## Week 2: Stochastic Logistics Simulation (Modeling Chaos)

### 1. Conceptualization & Literature Integration
* **Understanding the Task:** Before writing any code, We analyzed the core vulnerability of mining supply chains: deterministic planning fails when confronted with real-world uncertainty. 
* **Inheritance from Foundation Papers:** We grounded our approach in *Advanced Analytics for Surface Extraction* (Moradi-Afrapoli & Askari-Nasab) and *Maximizing Mining Operations* (Hazrathosseini & Moradi Afrapoli). These papers emphasize that traditional FMSs fail due to high-dimensionality and stochasticity. They advocate for a "value-chain" perspective rather than just an "in-pit" perspective.
* **Pre-Implementation Thought Process:** We realized that to prove the necessity of an intelligent FMS (Mining 4.0), We first had to prove how and why a traditional system fails. Therefore, our goal for Week 2 was not just to write a simulation, but to stress-test the logistics network by intentionally injecting compounded uncertainties (demand volatility + port capacity crashes).

### 2. Our Unique Approach
Standard simulations often evaluate uncertainties in isolation. Our unique approach was to construct a **coupled stochastic environment**. We simultaneously modeled demand volatility (continuous uncertainty via a Normal distribution with $CV=17\%$) and port disruptions (discrete "black swan" events via a 5% probability of a 50% capacity drop). This dual-layer stochasticity accurately mimics the complex, non-linear failures observed in real-world mineral logistics.

### 3. Step-by-Step Execution & Implementation
1. **Mathematical Framework Design:** We established a mass-balance inventory equation as our logical core: $S_{s,t} = \min(D_{s,t}, I_{s,t-1} + P_t, C_{s,t})$. This ensured that all simulated shipments were strictly bounded by market demand, physical stockpile, and port constraints.
2. **Monte Carlo Engine Initialization:** Utilizing `numpy`, We generated 200 distinct 60-month scenarios, establishing a robust statistical sample size to capture extreme tail-end risks.
3. **Disruption Logic Implementation:** We programmed a rolling probabilistic loop to simulate sudden port capacity losses, observing how these sudden bottlenecks backed up inventory into the mine.
4. **Data Aggregation & KPI Tracking:** We exported the granular simulation results into `sim_kpis.csv` and visualized the widening gap between expected demand and actual logistical shortfalls using `matplotlib`.

### Week 2 Key Findings:
Standard deterministic planning is deeply flawed. The simulation proved that even when average mine production perfectly matches average market demand (300 kt), the system experiences massive, unfulfillable shortfalls (up to 310+ kt) when a demand spike coincides with a port disruption. **Conclusion:** We cannot rely on averages; we must build predictive intelligence.

---

## Week 3: Predictive Analytics & Machine Learning (Anticipating Chaos)

### 1. Conceptualization & Literature Integration
* **Understanding the Task:** Having proved the supply chain's fragility in Week 2, Week 3 required building the "brain" to anticipate these shocks. We needed to shift from descriptive analytics (what happened) to predictive analytics (what will happen).
* **Inheritance from Foundation Papers:** *Maximizing Mining Operations* highlights the transition to AI-enabled algorithms to manage complex systems. Conventional methods (like static LP models) require predefined limits that fail under stochastic conditions. We opted to implement Machine Learning (Random Forest) because it inherently handles the non-linear relationships characteristic of dynamic market data.
* **Pre-Implementation Thought Process:** We recognized that predicting demand purely based on time (like a statistical SARIMA model) would be insufficient. A true Mining 4.0 FMS needs contextual awareness. How does current pricing affect future demand? How do our current stockpiles impact market behavior? 

### 2. Our Unique Approach
Instead of just feeding raw demand into an algorithm, We focused heavily on **Feature Engineering**. We constructed a multi-dimensional feature vector ($X_t = [D_{t-1}, D_{t-2}, D_{t-3}, Price_t, Inv_t, Month]$). By feeding the Random Forest model lagged demand, current inventory levels, and mineral price data, We forced the model to learn the hidden macroeconomic relationships of the value chain, rather than just memorizing a seasonal curve. Furthermore, We uniquely trained a *secondary* model to predict Price Volatility, giving the FMS dual-intelligence.

### 3. Step-by-Step Execution & Implementation
1. **Exploratory Data Analysis (EDA):** We initiated the workflow with `statsmodels.tsa.seasonal_decompose` to mathematically strip the 60-month historical data into its trend, seasonal, and residual components to understand the baseline pulse of the market.
2. **Feature Engineering Pipeline:** We utilized `pandas` `shift()` functions to create historical lag features and extracted numerical month identifiers to capture cyclicality. 
3. **Baseline Modeling:** We trained a SARIMA (1,1,1)(1,1,1,12) model to serve as our traditional, statistical benchmark.
4. **Machine Learning Implementation:** We trained two distinct `RandomForestRegressor` models—one targeting demand, the other targeting price. 
5. **Rigorous Evaluation:** We evaluated the models using MAE, RMSE, and $R^2$ on a 20% hold-out test set to ensure the models could generalize to unseen future market conditions, saving the best models via `pickle`.

### Week 3 Key Findings & KPI Results:
* **SARIMA Baseline:** MAE = 67.24 kt. Proved incapable of handling non-linear market shifts.
* **Random Forest (Demand):** MAE = 57.06 kt. Successfully outperformed the statistical baseline by incorporating engineered features. However, a negative $R^2$ mathematically highlighted that short-term demand volume remains highly chaotic and noisy.
* **Random Forest (Price Volatility):** MAE = $1.51 / t | $R^2$ = 0.99. **Critical Discovery:** While demand volume is noisy, mineral pricing is highly predictable. 

**Conclusion for Upcoming Weeks:** The FMS now has the intelligence to predict price crashes with near-perfect accuracy. Moving into the optimization phases (Weeks 4+), this allows the mathematical models to prescribe *proactive stockpiling* during anticipated price dips, directly protecting the mine's profit margins and achieving the core goal of an intelligent value-chain operation.