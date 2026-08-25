# Black-Litterman-Portfolio-Optimization-Framework

A quantitative portfolio construction framework for the **NIFTY 50** that combines traditional mean-variance optimization, CAPM-style market risk estimation, Black-Litterman reverse optimization, and explicit investor views to produce and compare portfolio allocations.

## Project Overview

Traditional Markowitz mean-variance optimization can be highly sensitive to estimation errors in expected returns. This project implements a Black-Litterman workflow for the NIFTY 50 to address that issue by:

1. Estimating historical asset returns and the covariance matrix.
2. Constructing a market-capitalization-weighted NIFTY 50 portfolio.
3. Building a traditional mean-variance efficient frontier.
4. Estimating NIFTY 50 market return and variance.
5. Computing each stock's covariance with the market and its CAPM beta.
6. Performing Black-Litterman reverse optimization to infer market-equilibrium returns.
7. Creating relative and absolute investor views using the `P` and `Q` matrices.
8. Combining equilibrium returns with investor views to obtain adjusted posterior returns.
9. Re-optimizing the portfolio using the adjusted Black-Litterman returns.
10. Comparing portfolio weights and efficient frontiers before and after incorporating views.

## Methodology

### Historical Data

The notebook retrieves approximately 10 years of daily price history using Yahoo Finance and uses the most recent **2,000 trading observations** for estimation.

Daily simple returns are calculated as:

```text
r_t = P_t / P_(t-1) - 1
```

Annualized expected returns:

```text
R_i = (1 + mean(daily_returns_i))^252 - 1
```

Annualized covariance:

```text
C = cov(daily_returns) × 252
```

### Markowitz Optimization

Portfolio return:

```text
μ_p = RᵀW
```

Portfolio variance:

```text
σ²_p = WᵀCW
```

The optimization is long-only with:

```text
0 ≤ W_i ≤ 1
ΣW_i = 1
```

The tangent portfolio maximizes:

```text
Sharpe = (μ_p - r_f) / σ_p
```

with the notebook using:

```python
rf = 0.065
```

### Black-Litterman Reverse Optimization

The market portfolio is constructed from normalized market capitalizations. The implied risk-aversion coefficient is:

```text
λ = (μ_m - r_f) / σ²_m
```

The equilibrium excess returns are:

```text
Π = λCW
```

These market-implied returns form the prior for Black-Litterman estimation.

### Investor Views

The notebook combines relative and absolute views.

Relative examples include:

```text
RELIANCE > ONGC        : 2.0%
HDFCBANK > SBIN        : 1.5%
TCS > WIPRO            : 1.0%
ICICIBANK > KOTAKBANK  : 1.0%
BHARTIARTL > ITC       : 1.5%
```

Absolute examples include:

```text
RELIANCE     : 18%
HDFCBANK     : 16%
ICICIBANK    : 17%
INFY         : 15%
TCS          : 14%
BHARTIARTL   : 18%
LT           : 17%
SUNPHARMA    : 16%
TITAN        : 18%
BAJFINANCE   : 17%
```

These are encoded through the Black-Litterman `P` and `Q` matrices.

### Posterior Returns

The notebook uses:

```python
tau = 0.025
```

and constructs the view uncertainty matrix as:

```text
Ω = τPCPᵀ
```

while retaining only its diagonal elements for the final uncertainty matrix.

The posterior equilibrium return vector is then computed from the prior and views and passed into the same portfolio optimization framework.

## Outputs

The notebook generates:

* Annualized expected-return and market-cap-weight tables.
* Covariance and correlation matrices.
* Covariance heatmaps.
* Historical efficient frontiers.
* Market-implied equilibrium return plots.
* Historical vs. equilibrium frontier comparisons.
* Markowitz portfolio weights.
* Black-Litterman portfolio weights.
* Black-Litterman weights after investor views.
* Before/after weight bar charts.
* Before/after portfolio pie charts.

After the adjusted Black-Litterman optimization, the notebook applies an additional post-processing step to allocations exceeding **10%**, redistributing the calculated excess weight across the assets.
