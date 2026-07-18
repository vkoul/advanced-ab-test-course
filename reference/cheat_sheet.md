# Advanced A/B Testing Cheat Sheet

## When to Use What

| Situation | Technique | Week |
|-----------|-----------|------|
| Basic comparison of two groups | t-test or Mann-Whitney | 1 |
| Metric has heavy-tailed outliers | Mann-Whitney, or cap outliers + t-test | 1, 3 |
| Need to know how long to run | Sample size calculation | 3 |
| Groups might be imbalanced on key covariates | Stratification | 4 |
| Have pre-experiment data correlated with metric | CUPED | 5 |
| Metric is a ratio (revenue/sessions) | Linearization | 6 |
| Testing many metrics simultaneously | FDR correction (Benjamini-Hochberg) | 7 |
| Want to stop early when evidence is clear | Sequential testing (SPRT) | 8 |
| All of the above | Capstone platform | Final |

## Quick Reference by Technique

### t-test
```python
from scipy.stats import ttest_ind
_, pvalue = ttest_ind(control, treatment)
significant = pvalue < 0.05
```

### Mann-Whitney (outlier-robust)
```python
from scipy.stats import mannwhitneyu
_, pvalue = mannwhitneyu(control, treatment, alternative='two-sided')
```

### Sample Size
```python
from scipy.stats import norm
z_alpha = norm.ppf(1 - alpha/2)  # 1.96 for alpha=0.05
z_beta = norm.ppf(1 - beta)      # 0.84 for beta=0.20
n = (z_alpha + z_beta)**2 * 2 * std**2 / (mu * effect - mu)**2
```

### Stratification
```python
# For each stratum, sample proportionally into pilot/control
for stratum in data.groupby(strat_columns):
    n_stratum = int(group_size * weight[stratum])
    # randomly assign n_stratum to pilot, n_stratum to control
```

### CUPED
```python
theta = np.cov(y_pilot, y_prepilot)[0, 1] / np.var(y_prepilot)
y_cuped = y_pilot - theta * y_prepilot
# Variance(y_cuped) < Variance(y_pilot)
```

### Linearization (for ratio metrics)
```python
kappa = total_numerator / total_denominator  # global ratio
linearized = numerator_per_user - kappa * denominator_per_user
# Now apply t-test on linearized values
```

### Multiple Testing Corrections
```python
from statsmodels.stats.multitest import multipletests

# Bonferroni (most conservative)
reject, pvals_adj, _, _ = multipletests(pvalues, method='bonferroni')

# Benjamini-Hochberg (FDR control — recommended for exploration)
reject, pvals_adj, _, _ = multipletests(pvalues, method='fdr_bh')
```

### Sequential Testing (SPRT)
```python
upper_bound = np.log((1 - beta) / alpha)
lower_bound = np.log(beta / (1 - alpha))
# Accumulate: sum += log(pdf_H1(x) / pdf_H0(x))
# Stop when sum > upper (reject H0) or sum < lower (reject H1)
```

## Common Pitfalls

| Pitfall | Solution |
|---------|----------|
| Peeking at results daily | Use sequential testing or pre-commit to a fixed duration |
| Outliers dominating t-test | Cap at 99th percentile, or use Mann-Whitney |
| Testing 20 metrics, finding 1 "significant" | Apply FDR correction |
| Ratio metric with unequal denominators | Linearize before testing |
| High variance requiring huge samples | Apply CUPED + stratification |
| Groups imbalanced on key segments | Use stratified assignment |
| Pre/post correlation ignored | Use CUPED to exploit it |

## Variance Reduction Stack (Cumulative)

```
Raw metric variance: σ²
  → Outlier capping:     ~0.7σ² (30% reduction typical)
  → Stratification:      ~0.85× remaining (15% further reduction)
  → CUPED:               ~(1-ρ²)× remaining (depends on correlation)
  → CUPED + ML:          ~(1-R²)× remaining (R² from model)

Example: if ρ=0.7 between pre/post metrics:
  Raw: 100% variance
  After CUPED: 1 - 0.7² = 51% of original variance
  → You need ~half the sample size!
```

## Decision Framework

```
Is the target metric statistically significant AND positive?
├── YES → Are any guardrail metrics significantly NEGATIVE?
│         ├── YES → DO NOT LAUNCH (causing harm)
│         └── NO  → LAUNCH ✓
└── NO  → DO NOT LAUNCH (no proven benefit)
```
