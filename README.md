# Advanced A/B Testing: Variance Reduction & Experimentation at Scale

An 8-week self-study course covering advanced A/B testing techniques — from statistical foundations through CUPED, linearization, multiple testing corrections, and sequential testing. Built with MyST Jupyter Book and deployable to GitHub Pages.

## Course Structure

| Week | Topic | Key Techniques |
|------|-------|----------------|
| 1 | Statistical Foundations | Welch's t-test, bootstrap, Type I/II errors, AAB testing |
| 2 | Real-World A/B Analysis | Metric computation, monitoring dashboards, anomaly detection |
| 3 | Sample Size & MDE | Power analysis, minimum detectable effect, experiment planning |
| 4 | Stratification | Pre/post-stratification, variance reduction via balanced allocation |
| 5 | CUPED | Controlled-experiment Using Pre-Experiment Data, ML covariates |
| 6 | Ratio Metrics & Linearization | Delta method, linearized metrics, combined pipelines |
| 7 | Multiple Testing & Infrastructure | Bonferroni, Holm, BH-FDR, hash-based user splitting |
| 8 | Sequential Testing | Wald's SPRT, early stopping with error control |

Each week includes:
- **Study Notes** — teaching notebook with theory, code demonstrations, and visualizations
- **Assignment** — scaffolded problems with hints (no solutions provided)

Plus: Capstone project (Flask A/B evaluation API), formula reference, decision flowchart, and cheat sheet.

## Getting Started

```bash
pip install -r requirements.txt
```

Open any notebook in Jupyter or click the "Open in Colab" badge at the top of each notebook.

## Building the Book

```bash
pip install jupyter-book
jupyter-book build --html .
```

## Disclaimer

This course was created for **educational and self-study purposes only**. The content is adapted and restructured from publicly available materials, translated into English, and rewritten with original narrative and code examples.

- The statistical methods presented are standard textbook techniques. Implementations are simplified for pedagogical clarity and may not be production-ready.
- The "QuickCart" company and all datasets are entirely fictional. Any resemblance to real companies or data is coincidental.
- This material was generated with the assistance of AI (Claude by Anthropic). While care has been taken to ensure correctness, users should verify critical statistical claims independently.
- No guarantee is made regarding the accuracy, completeness, or fitness for any particular purpose.

## License

CC-BY-4.0
