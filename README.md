# Causal Toolkit MKhan

A Python package for causal inference methods including randomized controlled trial (RCT) analysis, propensity score methods, and meta-learners for treatment effect estimation.

## Features

This package provides implementations of key causal inference methods:

### 📊 RCT Analysis (`rct` module)
- **`calculate_ate_ci()`**: Calculate Average Treatment Effect (ATE) with confidence intervals
- **`calculate_ate_pvalue()`**: Calculate ATE with t-statistic and p-value

### 🎯 Propensity Score Methods (`propensity` module)
- **`ipw()`**: Inverse Propensity Weighting for ATE estimation
- **`doubly_robust()`**: Doubly Robust estimator combining propensity scores and outcome regression

### 🤖 Meta-Learners (`meta_learners` module)
- **`s_learner_discrete()`**: S-Learner for discrete treatment (single model approach)
- **`t_learner_discrete()`**: T-Learner for discrete treatment (two model approach)
- **`x_learner_discrete()`**: X-Learner for discrete treatment (cross-fitted approach)
- **`double_ml_cate()`**: Double Machine Learning for Conditional Average Treatment Effects (CATE) with continuous treatment

## Installation

### From GitHub

```bash
# Clone the repository
git clone https://github.com/mokhan95/causal-toolkit-mkhan.git
cd causal-toolkit-mkhan

# Install in editable mode with uv
uv pip install -e .

# Or install with pip
pip install -e .
```

## Quick Start

### RCT Analysis

```python
import pandas as pd
from causal_toolkit_mkhan import calculate_ate_ci, calculate_ate_pvalue

# Your data should have columns 'T' (treatment) and 'Y' (outcome)
data = pd.DataFrame({
    'T': [1, 0, 1, 0, 1, 0],
    'Y': [5.2, 3.1, 6.0, 2.8, 5.5, 3.3]
})

# Calculate ATE with 95% confidence interval
ate, ci_lower, ci_upper = calculate_ate_ci(data, alpha=0.05)
print(f"ATE: {ate:.3f}, 95% CI: [{ci_lower:.3f}, {ci_upper:.3f}]")

# Calculate ATE with p-value
ate, t_stat, p_value = calculate_ate_pvalue(data)
print(f"ATE: {ate:.3f}, t-stat: {t_stat:.3f}, p-value: {p_value:.4f}")
```

### Propensity Score Methods

```python
from causal_toolkit_mkhan import ipw, doubly_robust

# IPW estimation
ate_ipw = ipw(df, ps_formula="X1 + X2", T="treatment", Y="outcome")
print(f"ATE (IPW): {ate_ipw:.3f}")

# Doubly Robust estimation
ate_dr = doubly_robust(df, formula="X1 + X2", T="treatment", Y="outcome")
print(f"ATE (Doubly Robust): {ate_dr:.3f}")
```

### Meta-Learners

```python
from causal_toolkit_mkhan import s_learner_discrete, t_learner_discrete, x_learner_discrete

# Prepare your data
train_df = pd.DataFrame(...)  # Training data
test_df = pd.DataFrame(...)   # Test data
X_cols = ['age', 'income', 'education']  # Covariate columns

# S-Learner
result_s = s_learner_discrete(train_df, test_df, X=X_cols, T='treatment', y='outcome')
print(f"Average CATE (S-Learner): {result_s['cate'].mean():.3f}")

# T-Learner
result_t = t_learner_discrete(train_df, test_df, X=X_cols, T='treatment', y='outcome')
print(f"Average CATE (T-Learner): {result_t['cate'].mean():.3f}")

# X-Learner
result_x = x_learner_discrete(train_df, test_df, X=X_cols, T='treatment', y='outcome')
print(f"Average CATE (X-Learner): {result_x['cate'].mean():.3f}")
```

### Double ML (Continuous Treatment)

```python
from causal_toolkit_mkhan import double_ml_cate

# For continuous treatment variables
result_dml = double_ml_cate(train_df, test_df, X=X_cols, T='dosage', y='outcome')
print(f"Average CATE (Double ML): {result_dml['cate'].mean():.3f}")
```

## API Reference

### `calculate_ate_ci(data, alpha=0.05)`
Calculate ATE with confidence interval using z-based CI.

**Parameters:**
- `data` (pd.DataFrame): DataFrame with columns 'T' (treatment) and 'Y' (outcome)
- `alpha` (float): Significance level (default: 0.05)

**Returns:** `(ate, ci_lower, ci_upper)` tuple

### `calculate_ate_pvalue(data)`
Calculate ATE with t-statistic and two-sided p-value.

**Parameters:**
- `data` (pd.DataFrame): DataFrame with columns 'T' (treatment) and 'Y' (outcome)

**Returns:** `(ate, t_stat, p_value)` tuple

### `ipw(df, ps_formula, T, Y)`
Inverse Propensity Weighting estimator.

**Parameters:**
- `df` (pd.DataFrame): Input data
- `ps_formula` (str): Patsy formula for propensity score model
- `T` (str): Treatment column name
- `Y` (str): Outcome column name

**Returns:** `float` - ATE estimate

### `doubly_robust(df, formula, T, Y)`
Doubly Robust estimator.

**Parameters:**
- `df` (pd.DataFrame): Input data
- `formula` (str): Patsy formula for both propensity and outcome models
- `T` (str): Treatment column name
- `Y` (str): Outcome column name

**Returns:** `float` - ATE estimate

### Meta-Learner Functions

All meta-learner functions share the same signature:

**Parameters:**
- `train` (pd.DataFrame): Training data
- `test` (pd.DataFrame): Test data
- `X` (List[str]): List of covariate column names
- `T` (str): Treatment column name
- `y` (str): Outcome column name

**Returns:** `pd.DataFrame` - Test data with added 'cate' column containing CATE estimates

## Testing

Run the test suite to verify installation:

```bash
# Run all tests
uv run pytest tests/ -v

# Run with coverage report
uv run pytest tests/ -v --cov=causal_toolkit_mkhan --cov-report=html
```

## Dependencies

- pandas >= 1.3.0
- numpy >= 1.21.0
- scipy >= 1.7.0
- scikit-learn >= 1.0.0
- lightgbm >= 3.3.0
- patsy >= 0.5.0

## Development

To contribute or modify the package:

```bash
# Install with development dependencies
uv pip install -e ".[dev]"

# Run tests
uv run pytest

# Format code with black
black causal_toolkit_mkhan/ tests/

# Type checking with mypy
mypy causal_toolkit_mkhan/
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

**mkhan**

## Acknowledgments

This package was developed as part of the Causal Models course at the University of Chicago, consolidating implementations from Weeks 2-5 assignments.

