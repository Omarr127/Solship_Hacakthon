# ⚡ Energy AI Hackathon 2026 — Solution

## 🎯 Problem Overview

We are given a residential site in Italy with:
- Solar PV system (9 kWp)
- Lithium battery (16 kWh)
- Connection to the electricity grid

At every 15-minute interval, the site must fully satisfy its electrical load using:
- PV generation
- Battery storage
- Grid electricity

Our goal is to **minimize the total electricity bill** using:
- Machine Learning (load forecasting)
- Model Predictive Control (battery optimization)

---

## ⚡ System Objective

At each timestep:
- Import electricity from grid (buy price)
- Export electricity to grid (sell price)
- Charge / discharge battery

We aim to reduce total cost under real constraints.

---

## 🧠 Solution Approach

We build a two-stage system:

---

### 1. Load Forecasting (ML Model)

We predict future electricity consumption using historical data (2024).

**Model used:**
- LightGBM (primary)
- XGBoost (backup)

**Features used:**
- Lag features (past load values)
- Rolling statistics (mean, std)
- Time features (hour, weekday, month)
- Weather features (temperature, humidity if available)

**Output:**
- 15-minute load forecast for 2025

---

### 2. Battery Optimization (MPC Controller)

We use forecasted load + PV + prices to control the battery.

At each timestep (rolling horizon H):
- Decide battery charging/discharging
- Decide grid import/export
- Ensure cost is minimized

---

## ⚙️ Constraints

We strictly follow physical constraints:
- Battery power: ±8 kW
- Grid power: ±6 kW
- State of Charge (SoC): 0 to 1
- Battery efficiency: 90% round-trip
- Initial SoC: 50%

---

## 🔁 Rolling Horizon (MPC)

The controller:
1. Looks ahead H timesteps (forecast window)
2. Solves optimization problem
3. Executes only first action
4. Moves forward and repeats

This ensures **realistic real-time control**.

---

## ⚡ Power Sign Convention

- Battery < 0 → charging  
- Battery > 0 → discharging  
- Grid > 0 → importing from grid  
- Grid < 0 → exporting to grid  

---

## 📊 Baselines

We compare against:

### Baseline A (Historical System)
- Real recorded battery operation from dataset

### Baseline B (Zero Intelligence)
- No battery usage
- PV serves load first
- Grid used for remaining demand

---

## 📈 Evaluation Metrics

### Forecasting Metrics:
- MAE
- RMSE
- NRMSE (main ranking metric)

### System Metrics:
- Total electricity bill (€)
- Savings vs Baseline A (%)
- Savings vs Baseline B (%)
- Oracle gap (perfect forecast comparison)

---

## ⚙️ System Pipeline
Raw Data (2024)
↓
Data Exploration (patterns, visualization)
↓
Feature Engineering (lag + rolling + time + weather)
↓
Load Forecasting Model (LightGBM / XGBoost)
↓
Forecasted Load (2025, 15-min resolution)
↓
Rolling Horizon MPC Optimizer
↓
Battery Dispatch Decisions (charge / discharge / grid import-export)
↓
Full 2025 Simulation
↓
Final Electricity Bill Calculation
↓
Comparison with Baselines + Oracle Gap Analysis

---

## 🧠 Key Idea

We combine:
- Machine Learning → predict future electricity demand
- Optimization (MPC) → decide optimal battery strategy

This reduces electricity cost while respecting all physical constraints of the system.

---

## 👨‍💻 Tech Stack

- Python
- LightGBM / XGBoost
- CVXPY (optimization)
- Pandas, NumPy
- Matplotlib
