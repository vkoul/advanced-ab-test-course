# Formula Reference

A complete reference of the key formulas used throughout this course.

---

## Week 1: Statistical Foundations

### Welch's t-statistic (two-sample)

$$
t = \frac{\bar{X}_1 - \bar{X}_2}{\sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}}}
$$

### Welch-Satterthwaite degrees of freedom

$$
\nu = \frac{\left(\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}\right)^2}{\frac{S_1^4}{n_1^2(n_1-1)} + \frac{S_2^4}{n_2^2(n_2-1)}}
$$

### Confidence interval for a proportion

$$
\hat{p} \pm z_{\alpha/2} \cdot \sqrt{\frac{\hat{p}(1-\hat{p})}{n}}
$$

---

## Week 3: Sample Size & MDE

### Sample size per group (two-sample, continuous metric)

$$
n = \frac{(z_{\alpha/2} + z_\beta)^2 \cdot 2\sigma^2}{\delta^2}
$$

where $\delta = \mu \cdot (\text{effect} - 1)$ is the absolute difference in means.

### Sample size per group (two-sample, binary metric)

$$
n = \frac{(z_{\alpha/2} + z_\beta)^2 \cdot [p_1(1-p_1) + p_2(1-p_2)]}{(p_2 - p_1)^2}
$$

### Minimum Detectable Effect (given fixed n)

$$
\text{MDE} = (z_{\alpha/2} + z_\beta) \cdot \sqrt{\frac{2\sigma^2}{n}}
$$

### Test duration

$$
\text{days} = \left\lceil \frac{n \times \text{num\_variants}}{\text{daily\_traffic}} \right\rceil
$$

---

## Week 4: Stratification

### Variance of stratified estimator

$$
\text{Var}(\bar{X}_{\text{strat}}) = \sum_{k=1}^K w_k^2 \cdot \frac{\sigma_k^2}{n_k}
$$

where $w_k$ is the weight (proportion) of stratum $k$, $\sigma_k^2$ is the within-stratum variance.

### Variance reduction from stratification

$$
\text{Var}(\bar{X}_{\text{SRS}}) - \text{Var}(\bar{X}_{\text{strat}}) = \sum_{k=1}^K w_k (\mu_k - \mu)^2
$$

The reduction equals the **between-stratum variance** — stratification removes this component entirely.

---

## Week 5: CUPED

### CUPED-adjusted metric

$$
Y_{\text{cuped}} = Y - \theta \cdot X
$$

### Optimal theta (minimizes variance)

$$
\theta^* = \frac{\text{Cov}(Y, X)}{\text{Var}(X)}
$$

### Variance of CUPED metric

$$
\text{Var}(Y_{\text{cuped}}) = \text{Var}(Y) \cdot (1 - \rho_{XY}^2)
$$

where $\rho_{XY}$ is the Pearson correlation between the covariate $X$ and the target $Y$.

### Variance reduction factor

$$
\text{Reduction} = 1 - (1 - \rho^2) = \rho^2
$$

A correlation of 0.7 gives 49% variance reduction. A correlation of 0.9 gives 81%.

---

## Week 6: Ratio Metrics & Linearization

### Ratio metric

$$
R = \frac{\sum_i x_i}{\sum_i y_i}
$$

where $x_i$ is the numerator (e.g., revenue) and $y_i$ is the denominator (e.g., sessions) for user $i$.

### Linearized metric

$$
L_i = x_i - \kappa \cdot y_i
$$

where $\kappa = \frac{\sum_i x_i}{\sum_i y_i}$ is the global ratio estimate.

### Delta method variance for ratio

$$
\text{Var}(R) \approx \frac{1}{\bar{Y}^2} \left[ \text{Var}(\bar{X}) - 2R \cdot \text{Cov}(\bar{X}, \bar{Y}) + R^2 \cdot \text{Var}(\bar{Y}) \right]
$$

---

## Week 7: Multiple Testing

### Family-Wise Error Rate (FWER)

$$
\text{FWER} = P(\text{at least one false rejection}) = 1 - (1-\alpha)^m \approx m \cdot \alpha
$$

### Bonferroni correction

$$
\alpha_{\text{adj}} = \frac{\alpha}{m}
$$

Reject $H_i$ if $p_i < \alpha/m$.

### Holm's step-down procedure

1. Order p-values: $p_{(1)} \leq p_{(2)} \leq \ldots \leq p_{(m)}$
2. For $k = 1, 2, \ldots$: reject $H_{(k)}$ if $p_{(k)} < \frac{\alpha}{m - k + 1}$
3. Stop at first non-rejection

### Benjamini-Hochberg (FDR)

1. Order p-values: $p_{(1)} \leq p_{(2)} \leq \ldots \leq p_{(m)}$
2. Find largest $k$ such that $p_{(k)} \leq \frac{k}{m} \cdot \alpha$
3. Reject all $H_{(1)}, \ldots, H_{(k)}$

Controls: $\text{FDR} = E\left[\frac{\text{false discoveries}}{\text{total discoveries}}\right] \leq \alpha$

---

## Week 8: Sequential Testing (SPRT)

### Log-likelihood ratio

$$
\Lambda_n = \sum_{i=1}^n \log \frac{f_1(x_i)}{f_0(x_i)}
$$

### Decision boundaries

$$
A = \log \frac{\beta}{1 - \alpha} \quad \text{(lower, reject } H_1\text{)}
$$

$$
B = \log \frac{1 - \beta}{\alpha} \quad \text{(upper, reject } H_0\text{)}
$$

### Decision rule

$$
\begin{cases}
\Lambda_n \geq B & \Rightarrow \text{Reject } H_0 \text{ (effect exists)} \\
\Lambda_n \leq A & \Rightarrow \text{Reject } H_1 \text{ (no effect)} \\
A < \Lambda_n < B & \Rightarrow \text{Continue sampling}
\end{cases}
$$

### For normal data with known variance

If $H_0: \mu = \mu_0$ vs $H_1: \mu = \mu_1$ with known $\sigma$:

$$
\log \frac{f_1(x)}{f_0(x)} = \frac{(\mu_1 - \mu_0)}{\sigma^2} \left(x - \frac{\mu_0 + \mu_1}{2}\right)
$$

---

## Useful Constants

| Symbol | Value | Meaning |
|--------|-------|---------|
| $z_{0.025}$ | 1.960 | Two-sided alpha = 0.05 |
| $z_{0.005}$ | 2.576 | Two-sided alpha = 0.01 |
| $z_{0.20}$ | 0.842 | Power = 0.80 |
| $z_{0.10}$ | 1.282 | Power = 0.90 |

## Combining Techniques

### Linearization + CUPED

1. Compute linearized metric: $L_i = x_i - \kappa \cdot y_i$
2. Compute pre-period linearized metric: $L_i^{\text{pre}}$
3. Apply CUPED: $L_i^{\text{cuped}} = L_i - \theta \cdot L_i^{\text{pre}}$

### Stratification + CUPED

1. Apply CUPED within each stratum (or globally)
2. Run stratified test on CUPED-adjusted values
3. Both variance reductions stack (approximately multiplicatively)
