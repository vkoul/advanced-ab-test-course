# Advanced A/B Testing

Welcome to **Advanced A/B Testing** — a hands-on course that takes you beyond the basics of experimentation into the techniques used by top tech companies to run faster, more sensitive, and more reliable experiments.

## Prerequisites

This course assumes you already understand:

- Basic A/B testing (hypothesis testing, p-values, confidence intervals)
- Python (pandas, numpy, scipy)
- Introductory statistics (distributions, CLT, standard errors)

If you need a refresher, check out [A/B Testing in Practice](https://vkoul.github.io/ab-test-practice/) first.

## The Setting

You are a **data scientist on the experimentation platform team** at **QuickCart** — a fast-growing online electronics retailer. Your team runs hundreds of A/B tests per quarter across checkout flows, recommendation engines, pricing, and search.

The challenge: with so many experiments, you need techniques that reduce noise (detect smaller effects with fewer users), handle complex metrics (ratios, revenue per user), avoid false discoveries (when testing many metrics), and make decisions faster (without waiting weeks for significance).

Each week introduces a technique that solves one of these problems.

## Course Structure

| Week | Topic | Problem It Solves |
|------|-------|-------------------|
| 1 | Statistical Foundations | Building reliable tests from scratch |
| 2 | Real-World A/B Analysis | Handling messy real data, synthetic metrics |
| 3 | Sample Size & MDE | How long to run, how small an effect to detect |
| 4 | Stratification | Reducing variance via population structure |
| 5 | CUPED | Reducing variance via pre-experiment data |
| 6 | Ratio Metrics & Linearization | Testing metrics like revenue/session correctly |
| 7 | Multiple Testing & Infrastructure | Running many tests without false discoveries |
| 8 | Sequential Testing | Making decisions before the test "ends" |

**Capstone**: Build an end-to-end A/B testing service that combines CUPED, stratification, and ML-based variance reduction.

## How to Use This Course

For each week:

1. **Read** the Study Notes — theory with intuition, formulas, and code demonstrations
2. **Attempt** the Assignment — scaffolded problems to implement the techniques yourself
3. **Verify** your solution — test cases are provided in the assignments for self-checking

## Run the Notebooks

Click the Colab badge at the top of any notebook to run it interactively.

Or clone and run locally:
```bash
git clone https://github.com/vkoul/advanced-ab-testing.git
cd advanced-ab-testing
pip install -r requirements.txt
jupyter lab
```
