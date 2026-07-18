# Decision Flowchart

Use this guide to pick the right technique for your experiment.

---

## Which Statistical Test Should I Use?

```
Is your metric a ratio (e.g., revenue per session)?
├── YES → Linearize the metric first (Week 6), then test linearized values
└── NO  → Continue below

Does your metric have heavy-tailed outliers?
├── YES → Cap at 99th percentile, OR use Mann-Whitney U test
└── NO  → Standard t-test is fine

Do you have more than one metric or variant?
├── YES → Apply multiple testing correction (Week 7)
│         ├── Exploratory (many metrics)? → Benjamini-Hochberg (FDR)
│         └── Confirmatory (few, critical)? → Holm (FWER control)
└── NO  → Single t-test at α = 0.05
```

---

## Do I Need Variance Reduction?

```
Is your sample size limited or your MDE requirement tight?
├── NO  → Probably fine without it. Run the standard test.
└── YES → Apply one or more techniques:

    Do you have pre-experiment data for the same metric?
    ├── YES → Use CUPED (Week 5)
    │         ├── Correlation ρ > 0.5? → Big win! (>25% variance reduction)
    │         └── Correlation ρ < 0.3? → Modest benefit. Still worth doing.
    └── NO  → Skip CUPED

    Can you segment users into meaningful strata (device, country, cohort)?
    ├── YES → Use Stratification (Week 4)
    │         └── Strata explain >10% of variance? → Worth it
    └── NO  → Skip stratification

    Do you have many user features available?
    ├── YES → Consider ML-CUPED (train model on pre-data, use predictions as covariate)
    └── NO  → Stick with simple CUPED
```

---

## How Long Should I Run the Experiment?

```
Do you know your target metric's variance (from historical data)?
├── NO  → Run an AA test for 1-2 weeks first to estimate variance
└── YES → Continue

Choose your parameters:
  • α = significance level (typically 0.05)
  • β = acceptable Type II error (typically 0.20, giving 80% power)
  • MDE = minimum effect you want to detect (business decision)

Calculate: n = (z_α/2 + z_β)² × 2σ² / MDE²
Duration = ceil(n × num_variants / daily_traffic)

Is the duration acceptable?
├── YES → Run the experiment for that duration
└── NO  → Options:
          ├── Lower your power requirement (β = 0.30 → 70% power)
          ├── Increase your MDE (detect only larger effects)
          ├── Apply variance reduction (CUPED, stratification)
          └── Use sequential testing to potentially stop early (Week 8)
```

---

## Can I Stop Early?

```
Did you pre-commit to a fixed sample size?
├── YES → NO, you cannot peek. Wait for the full sample.
│         (Peeking inflates Type I error far beyond α)
└── NO  → Did you set up sequential testing (SPRT) before starting?
          ├── YES → Check your cumulative log-likelihood ratio:
          │         ├── Λ ≥ B → Stop: reject H₀ (effect exists)
          │         ├── Λ ≤ A → Stop: reject H₁ (no effect)
          │         └── A < Λ < B → Continue sampling
          └── NO  → You cannot stop early. Commit to the full run.
```

---

## Should I Launch This Feature?

```
Step 1: AA Validation
  Is the AA test (control-1 vs control-2) non-significant?
  ├── NO → STOP. Something is wrong with your methodology.
  │        (Check: randomization, metric computation, data pipeline)
  └── YES → Proceed to AB test

Step 2: Primary Metric
  Is the primary metric statistically significant?
  ├── NO → DO NOT LAUNCH (no proven benefit)
  └── YES → Is the effect POSITIVE (in the desired direction)?
            ├── NO → DO NOT LAUNCH (significant negative effect!)
            └── YES → Continue to guardrails

Step 3: Guardrail Metrics
  Are any guardrail metrics significantly NEGATIVE?
  ├── YES → DO NOT LAUNCH (causing harm elsewhere)
  │         Investigate the trade-off. Can the harm be mitigated?
  └── NO  → LAUNCH ✓

Step 4: Practical Significance
  Is the effect size large enough to justify the engineering cost?
  ├── NO → DO NOT LAUNCH (real but too small to matter)
  └── YES → LAUNCH ✓ (and document the expected impact)
```

---

## Quick Reference: Technique Selection Matrix

| Your situation | Primary technique | Supporting technique |
|---|---|---|
| Standard metric, large traffic | t-test | None needed |
| Standard metric, limited traffic | t-test | CUPED + Stratification |
| Ratio metric (revenue/session) | Linearization + t-test | CUPED on linearized metric |
| Heavy-tailed metric | Cap outliers + t-test | Mann-Whitney as sanity check |
| Many metrics being tested | t-test + BH correction | Group into primary/secondary/guardrail |
| Impatient stakeholders | Sequential testing (SPRT) | CUPED to speed convergence |
| First time testing in this area | AA test first | Compute historical variance |
| Pre/post data highly correlated | CUPED | Stratification for further gains |
| New users (no pre-data) | Stratification | Consider surrogate pre-metrics |

---

## The Full Stack (Combining Everything)

For maximum sensitivity with production-grade rigor:

```
1. Pre-experiment
   ├── Compute historical metric variance
   ├── Run sample size calculation
   ├── Set up hash-based user splitting (Week 7)
   └── Define primary, secondary, and guardrail metrics

2. During experiment
   ├── Monitor with sequential testing (optional early stop)
   ├── Check randomization with AA validation daily
   └── Do NOT peek at AB results without sequential framework

3. Analysis
   ├── Cap outliers (99th percentile)
   ├── Linearize ratio metrics (if applicable)
   ├── Apply CUPED (using pre-period same-metric)
   ├── Apply stratification (by key segments)
   ├── Run test on adjusted values
   ├── Apply multiple testing correction (if >1 metric)
   └── Check practical significance

4. Decision
   ├── AA passes → methodology OK
   ├── Primary metric significant + positive → candidate for launch
   ├── Guardrails safe → launch approved
   └── Document and communicate
```
