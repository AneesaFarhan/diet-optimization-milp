# Minervan Diet Optimizer (MDO)

**A generalized Mixed-Integer Linear Program that finds the cost-minimal, nutritionally-compliant, physically-transportable weekly grocery basket — adaptable to any city's local prices**

[![Course](https://img.shields.io/badge/Course-CS164%20Optimization%20Methods-blue)](https://aneesafarhan.github.io/diet-optimization-milp/)

---

## Overview

Minerva University's rotation model means relocating to a new city — with a completely different cost of living and currency — every four months. Manually re-balancing food costs against macronutrient targets in each new city is tedious and error-prone. The classical **Stigler Diet Problem** offers a theoretical starting point, but its standard Linear Programming formulation allows unrealistic fractional purchases (0.37 jars of peanut butter) and ignores real-world grocery logistics entirely.

**MDO** reformulates the diet problem as a **Mixed-Integer Linear Program (MILP)**: given a city's local grocery price data, it outputs a cost-optimal, integer-quantity, weight-bounded weekly grocery basket that satisfies WHO nutritional standards — adaptable to any location simply by swapping in new local price data.

Built as the final project for **CS164: Optimization Methods**.

---

## Problem Formulation

| Element | Definition |
|---|---|
| Decision variables | `x_j ∈ Z≥0` — integer quantity of item *j* purchased; `y_j ∈ {0,1}` — binary indicator for whether item *j* is purchased at all |
| Objective | Minimize total basket cost `Z = Σ c_j · x_j` |
| Nutrition constraints | Weekly protein ≥ 378g and energy ≥ 15,750 kcal, based on averaged WHO/UK government reference standards for an anonymous student population |
| Logistics constraint | A "backpack constraint" bounding total basket weight, preventing the solver from selecting large quantities of low-density, low-cost bulk items that would be physically unrealistic to carry home |
| Category constraints | Binary indicators enforce dietary category requirements (e.g., minimum representation across food groups), with an optional vegan mode disabling dairy requirements entirely |

The result is a genuinely **discrete, non-convex** optimization problem — a meaningful step up from the continuous relaxation of the classical Stigler formulation.

---

## Case Studies

Three scenarios are solved and compared using real local grocery price data:

1. **Buenos Aires** — standard nutritional constraints, ARS pricing
2. **San Francisco** — standard nutritional constraints, USD pricing
3. **Vegan sensitivity test** — Buenos Aires price data with dairy requirements disabled, isolating the cost impact of the vegan constraint

A key finding from the comparison: dietary cost is driven less by raw caloric needs (efficiently met by cheap carbohydrate staples — rice alone supplies a large share of weekly calories at a small fraction of total cost) and more by the **non-caloric nutritional and category requirements**, which pull the solver toward more expensive items regardless of location.

---

## Repository Structure

```
diet-optimization-milp/
├── diet_optimizer.ipynb        # Full MILP implementation (cvxpy), outputs stripped
├── CS164_Final_Report.pdf      # Full written report: formulation, justification, analysis
├── prices_sf.csv               # San Francisco grocery price + nutrition data
├── prices_ba.csv               # Buenos Aires grocery price + nutrition data
├── figures/
│   ├── figure_1.png
│   ├── figure_2.png
│   ├── prices_sf.png
│   └── prices_ba.png
├── README.md
└── LICENSE
```

---

## Tech Stack

| Library | Role |
|---|---|
| `cvxpy` | Mixed-Integer Linear Program modeling and solving |
| `pandas` | Price/nutrition data loading and basket aggregation |
| `numpy` | Numerical operations |
| `matplotlib` / `seaborn` | Category cost breakdowns, comparative visualizations |

---

## Key Design Decisions

**Why MILP instead of the classical LP diet problem?**
Continuous LP solutions allow fractional item quantities that have no real-world meaning (0.37 jars of peanut butter). Integer decision variables force the model to output an actually purchasable basket.

**Why a "backpack constraint"?**
Standard household diet-optimization models assume grocery transport by car. Minerva students carry groceries on foot between rotations, so the model penalizes selecting excessive quantities of low-cost, low-density bulk items that would be impractical to physically transport.

**Why WHO/UK government reference standards instead of individualized targets?**
The tool is designed to generalize across an anonymous, diverse student body rather than being tuned to any one person, so nutrition targets are averaged across standard male/female reference values (0.83g/kg protein; 2,000–2,500 kcal/day energy).

**Why compare across cities instead of optimizing for one?**
The core motivation is Minerva's rotation model — the tool's value is in being trivially re-runnable against any new city's price data, which the Buenos Aires vs. San Francisco comparison is designed to demonstrate.

---

## Running Locally

```bash
pip install cvxpy pandas numpy matplotlib seaborn
jupyter notebook diet_optimizer.ipynb
```

`prices_sf.csv` and `prices_ba.csv` are loaded directly from the repository root. To adapt the model to a new city, replace either CSV with local price/nutrition data in the same column format (`Item, Price, Protein (g), Calories (kcal), Category`).

---

## License

MIT. See `LICENSE`.
