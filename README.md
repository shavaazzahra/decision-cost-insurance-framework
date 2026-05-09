# decision-cost-insurance-framework
Insurance premium pricing framework with asymmetric cost-sensitive decision making, balancing False Positive (customer churn) vs False Negative (underwriting loss) risk through Monte Carlo simulation and tail risk analysis.
# asymmetric-cost-premium-decision

Premium pricing decisions are rarely just a statistics problem. This project treats them as a cost problem.

---

## The Problem

When an insurance company decides whether to raise premiums, two things can go wrong:

- **Raise when you shouldn't** → customers leave, you lose market share (False Positive)
- **Don't raise when you should** → you absorb the losses, reserves run thin, solvency risk creeps in (False Negative)

The catch: these two mistakes don't cost the same. In this dataset, the cost of a False Negative is nearly **4x higher** than a False Positive. That asymmetry changes everything about how the decision should be made.

A p-value alone won't tell you which mistake is cheaper to make. That's what this project is for.

---

## What This Does

Starting from 5,500 observations of projected insurance losses (simulated data), the analysis runs through:

1. **EDA**  the loss data is right-skewed and heavy-tailed, so normal distribution assumptions are out from the start
2. **Distribution fitting**  Gamma, Weibull, and Lognormal are tested via KS-test; Lognormal wins
3. **Hypothesis testing**  one-sample right-tailed t-test to check whether mean losses have significantly exceeded the historical benchmark
4. **Cost matrix**  CFP and CFN are defined based on customer count and per-unit financial impact
5. **Monte Carlo simulation**  10,000 scenarios generated from the fitted Lognormal distribution, each run through the same hypothesis test and cost classification
6. **VaR & TVaR at 95%**  tail risk measured separately for "raise premium" vs "keep premium" decisions
7. **Final recommendation**  if TVaR under inaction exceeds TVaR under action, raising the premium is the safer call

---

## Cost Structure

| Error | Scenario | Financial Impact |
|---|---|---|
| False Positive | Raised premium unnecessarily | $1,200 × 5,500 customers = **$6.6M** |
| False Negative | Failed to raise when losses increased | $5,000 × 5,500 customers = **$27.5M** |

The asymmetry here is intentional. It reflects a real tradeoff: short-term reputational risk (FP) vs long-term solvency risk (FN).

---

## Project Structure

```
├── 5500 obs simulated data.csv  # dataset
├── README.md
└── notebook.ipynb #main notebook
```

---

## Stack

- Python 3.10
- `scipy` — distribution fitting, KS-test, t-test
- `numpy` — Monte Carlo simulation engine
- `matplotlib` — all visualizations
- `pandas` — data handling

---

## Results

The notebook produces:

- KS-test comparison across three distributions
- Error type breakdown across 10,000 simulated scenarios
- VaR and TVaR for both decision paths
- A final recommendation based on tail risk comparison

Actual values depend on the data — run the notebook to see them.

## Context

This is part of a broader interest in decision frameworks that go beyond statistical significance — where the question isn't just *"is this effect real?"* but *"what does it cost to be wrong in each direction?"*

The actuarial angle (VaR, TVaR, loss distribution fitting) is intentional. Premium pricing decisions live at the intersection of statistics and financial risk, and treating them as purely one or the other tends to miss the point.
