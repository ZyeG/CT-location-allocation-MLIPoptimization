## Overview
This project formulates and solves a Mixed-Integer Linear Programming (MILP) facility location model to optimize the capital allocation of new CT machines across an 8-hospital network. The goal is to minimize total capital expenditures and operational costs while maximizing the percentage of the population (across 19 census tracts) within a 45-minute travel radius.

This was completed as an operations optimization assignment for MIE1623 (Introduction to Healthcare Engineering) at the University of Toronto.

## Tech Stack
* **Modeling & Optimization:** Microsoft Excel, Solver Add-in (Simplex LP)
* **Mathematics:** Mixed-Integer Linear Programming (MILP), Operations Research

## Mathematical Formulation

### 1. Decision Variables
* **Continuous Variables ($x_{ij}$):** The number of patients routed from census tract $i$ to hospital $j$ (152 variables).
* **Integer Variables ($y_j$):** The number of new CT machines installed at hospital $j$ (8 variables).

### 2. Objective Function
Minimize Total Cost = Capital/Operating Costs + Variable Exam Costs + Routing Penalty.
$$\text{Minimize } Z = \sum_{j \in J} (1,250,000 + 1,700,000) \cdot y_j + \sum_{i \in I} \sum_{j \in J} 62.26 \cdot x_{ij} + \left( \sum_{i \in I} \sum_{j \in J} t_{ij} \cdot x_{ij} \right) \cdot 0.001$$
*(Note: A fractional penalty weight of 0.001 is applied to the total travel time $t_{ij}$ to force the solver to route patients to their closest available CT scanner without overriding actual financial costs).*

### 3. Constraints
1. **Demand Satisfaction:** Every census tract's demand must be completely met.
   $$\sum_{j \in J} x_{ij} = D_i \quad \forall i \in \{1...19\}$$
2. **Machine Capacity:** A hospital cannot process more exams than its installed machines allow (Max 19,743 exams per machine).
   $$\sum_{i \in I} x_{ij} \le 19,743 \cdot y_j \quad \forall j \in \{A...H\}$$
3. **Integrality & Non-Negativity:** $x_{ij} \ge 0$ and $y_j \in \mathbb{Z}^+$

## Handling Mathematical Infeasibility
The initial stakeholder requirement dictated that **90% of the population must be within 45 minutes** of a CT location. 

However, translating this into a hard mathematical constraint results in an **infeasible model**. Due to regional geography, census tracts 10, 14, and 16 possess minimum travel times of 56, 48, and 48 minutes respectively to their absolute closest hospitals. Because their combined unmet demand represents over 11% of the total population, the maximum theoretical coverage is capped at **88.90%**. 

To resolve this, the 90% constraint was relaxed and replaced with the objective function's distance penalty, allowing the Simplex LP solver to successfully compute the absolute best-case scenario feasible within the geographic limits.

## Results
* **Total System Cost:** $32,678,815.46
* **Maximum Coverage Achieved:** 88.90% (129,628 patients)
* **Capital Allocation:** Exactly 8 new CT machines are required to meet volume demands. To minimize travel times, the optimal distribution is exactly 1 machine built at each of the 8 hospitals.
* **90th-Percentile Travel Time:** 48 minutes.

## How to Run
1. Open `CT_location.xlsx`.
2. Navigate to **Data > Solver** (ensure the Solver add-in is enabled).
3. The objective function, variable cells, integer constraints, and Simplex LP engine are pre-configured.
4. Click **Solve** to generate the optimal routing grid and capital allocation row.
