# Project 2 — Causal Inference in Healthcare: Matching, OVB, and IV

## Overview
This project evaluates whether **pacemaker device type** creates a downstream **“diagnostic penalty”** that harms cognitive outcomes. The key idea: patients with **legacy (non–MRI-conditional)** pacemakers may face more friction accessing MRI, delaying neurological diagnosis and treatment, which can worsen cognition over time.

We compare causal methods across three escalating realism levels:
1. **Selection bias** (observed confounding)
2. **Omitted Variable Bias (OVB)** (unobserved confounding revealed by a “client update”)
3. **Instrumental Variables (IV)** to recover causal effects when confounding can’t be fully measured

## Client + Research Question
**Client:** National Academies of Sciences, Engineering, and Medicine  
**Question:**  
> Among patients who need a pacemaker, does choosing a **Legacy System (D=1)** cause worse cognitive outcomes over time compared to an **MRI-Conditional System (D=0)**?

## Data (Simulated)
We simulate a cohort (**N = 5000**) to reflect realistic selection and downstream outcomes.

### Key Variables
- **Treatment (D):** `legacy_device`  
  - `1` = Legacy / non–MRI-conditional pacemaker  
  - `0` = MRI-conditional pacemaker
- **Outcome (Y):** `moca_score` (MoCA, 0–30)
- **Observed confounder (X):** `household_income` (log-normal to mimic U.S. right-skew)
- **Unobserved confounder (U) revealed later:** `health_literacy` (correlated with income, affects both D and Y)

### Simulation logic
- **Income influences treatment assignment** (lower income -> higher probability of legacy device)
- **Legacy device imposes a true causal penalty** to MoCA (negative effect)
- **Health literacy** creates **OVB** when omitted from models

Outputs saved during the workflow include:
- `Project2_OVB_Data.csv` (data including health literacy for demonstrations)

## Methods Used
### 1) Regression Adjustment (RA)
- Vanilla/naive regression vs adjusted regression controlling for income

### 2) Matching + Regression (RA + Matching)
- Matching on income (full matching framework)
- Weighted regression on matched sample
- Post-matching treatment effect computed with robust SE logic (subclass clustering)

### 3) Omitted Variable Bias (OVB) Demonstration
- Introduce health literacy as a missing confounder
- Show how omission biases the estimated treatment effect
- Sensitivity analysis using `sensemakr`

### 4) Instrumental Variables (IV)
To address unobserved confounding, we introduce an instrument:

**Instrument (Z): Regional legacy device supply**
- **Relevance:** regions with more legacy inventory implant more legacy devices
- **Exclusion:** regional supply affects cognition only through pacemaker type
- **Independence:** supply decisions are administrative/procurement-driven (not patient literacy)

We estimate effects using **2SLS** via `AER::ivreg` and report IV diagnostics (first stage/reduced form + tests).

## Key Deliverables
- A rendered report (HTML) containing:
  - DAGs (observed confounding + latent confounder + IV setup)
  - EDA showing imbalance and mechanism plausibility
  - Side-by-side comparison table of estimates (OLS vs controlled vs IV vs truth)

