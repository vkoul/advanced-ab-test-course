# Capstone Project: A/B Testing Platform

## Overview

Build an end-to-end A/B testing evaluation service that combines the techniques from all 8 weeks into a production-grade system.

## The Challenge

You are building the **statistical evaluation engine** for QuickCart's experimentation platform. The service receives:

- **User data**: user_id, gender, age
- **Sales data**: user_id, day, sales amount
- **Test groups**: lists of user IDs for control (group_a_one, group_a_two) and treatment (group_b)

The service must return whether a **statistically significant positive effect** exists in the treatment group.

## Architecture

```
┌─────────────┐     ┌──────────────────────────┐     ┌────────────┐
│  Test Setup │────▶│  Evaluation Engine (You)  │────▶│  Decision  │
│  (groups)   │     │  Flask API + Statistics   │     │  yes / no  │
└─────────────┘     └──────────────────────────┘     └────────────┘
```

## Requirements

Your service must:

1. **Accept** a POST request with user group assignments
2. **Compute** the target metric (total sales per user during the pilot period)
3. **Apply** at least two variance reduction techniques:
   - CUPED (using pre-pilot sales as covariate)
   - Stratification (by gender and/or age bucket)
4. **Validate** with an AA test (groups A1 vs A2 should NOT be significant)
5. **Return** a JSON response: `{"result": 0}` (no effect) or `{"result": 1}` (significant positive effect)

## Scoring

Your solution is tested against multiple synthetic experiments:

| Scenario | What's tested |
|----------|--------------|
| True positive | Real effect exists — your system should detect it |
| True negative | No effect — your system should NOT claim significance |
| Small effect | Effect exists but is small — tests sensitivity |
| AA validation | Both "control" groups are identical — must return 0 |

## Suggested Approach

### Phase 1: Baseline (t-test only)
```python
# Simple approach: compute mean sales per user, run t-test
from scipy.stats import ttest_ind

control_metric = sales_per_user[control_users]
treatment_metric = sales_per_user[treatment_users]
_, pvalue = ttest_ind(control_metric, treatment_metric)
result = int(pvalue < 0.05)
```

### Phase 2: Add CUPED
```python
# Use pre-pilot sales as covariate
theta = cov(pilot_sales, prepilot_sales) / var(prepilot_sales)
cuped_metric = pilot_sales - theta * prepilot_sales
# Now test on cuped_metric instead
```

### Phase 3: Add Stratification
```python
# Stratify by gender x age_bucket
# Run stratified t-test (weighted combination of within-stratum tests)
```

### Phase 4: ML-Enhanced CUPED (Advanced)
```python
# Train LightGBM on pre-pilot features to predict pilot sales
# Use predictions as CUPED covariate (higher correlation = more variance reduction)
```

## Flask API Template

```python
from flask import Flask, request, jsonify
import numpy as np
import pandas as pd
from scipy.stats import ttest_ind

app = Flask(__name__)

# Load user and sales data at startup
users = pd.read_csv('users.csv')
sales = pd.read_csv('sales.csv')

@app.route('/test', methods=['POST'])
def evaluate_test():
    data = request.json
    group_a_one = data['group_a_one']  # control group 1
    group_a_two = data['group_a_two']  # control group 2
    group_b = data['group_b']          # treatment group
    
    # YOUR IMPLEMENTATION HERE
    # 1. Compute metrics for each group
    # 2. Apply CUPED
    # 3. Apply stratification
    # 4. AA validation: test group_a_one vs group_a_two
    # 5. AB test: test group_a_one vs group_b
    
    result = 0  # default: no effect
    return jsonify({"result": result})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## Dockerfile

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["python", "app.py"]
```

## Data Description

### users.csv
| Column | Type | Description |
|--------|------|-------------|
| user_id | int | Unique user identifier |
| gender | str | 'M' or 'F' |
| age | int | User age (18-65) |

### sales.csv
| Column | Type | Description |
|--------|------|-------------|
| user_id | int | User who made the purchase |
| day | str | Date of purchase (YYYY-MM-DD) |
| sales | float | Purchase amount |

### Periods
- **Pre-pilot**: 2 weeks before the experiment
- **Pilot**: 2 weeks of the experiment

## Tips

1. **Start simple, iterate**: Get the baseline working first, then add CUPED, then stratification
2. **AA test is crucial**: If your AA test rejects (A1 vs A2 shows significance), something is wrong with your methodology — return 0
3. **Outlier handling**: Cap sales at the 99th percentile before computing metrics
4. **Age bucketing**: For stratification, bucket ages into groups (18-25, 26-35, 36-45, 46-55, 56+)
5. **Pre-compute what you can**: CUPED theta and model predictions can be computed once at startup

## Deliverables

1. `app.py` — Your Flask application
2. `Dockerfile` — Container definition
3. `requirements.txt` — Python dependencies
4. A brief write-up explaining your approach and expected variance reduction

## Going Further

If you want an extra challenge:
- Add sequential testing (stop early if evidence is overwhelming)
- Implement multiple metric testing with FDR correction
- Build a dashboard showing experiment progress over time
- Compare your variance reduction against the simple baseline — by what factor did you reduce the required sample size?
