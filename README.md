# Monte Carlo Portfolio Simulation and Risk Analysis

## Project Overview

This project uses Monte Carlo simulation to explore possible long-term investment outcomes and quantify portfolio risk using historical financial data.

Historical daily price data for four ETFs (SPY, QQQ, BND, and GLD) is used to construct a diversified portfolio and simulate 10,000 possible 10-year investment paths. The project compares different simulation models, evaluates optimistic and pessimistic scenarios, and examines how portfolio allocation affects potential returns and downside risk.

The objective is not to predict exact future prices, but to understand the range of possible outcomes under different assumptions.

## Project Objectives

- Collect and analyze historical ETF price data.
- Calculate historical returns and portfolio risk metrics.
- Construct a diversified portfolio using fixed asset weights.
- Simulate possible future portfolio paths using three models.
- Estimate ending portfolio values and the probability of loss.
- Measure downside risk using Value at Risk (VaR) and Expected Shortfall (ES).
- Compare optimistic, base, and pessimistic scenarios.
- Evaluate conservative, balanced, and growth-oriented allocations.
- Document model assumptions, limitations, and potential improvements.

## Research Questions

This project investigates:

1. What range of portfolio values could occur over a 10-year investment horizon?
2. What is the probability of the portfolio ending below its initial investment?
3. What is the probability of reaching the selected investment target of \$200,000?
4. How severe could portfolio drawdowns become over the investment horizon?
5. How large could losses be over different horizons (1 day, 10 days, 1 year, and 10 years), measured using Value at Risk (VaR)?
6. How does Expected Shortfall (ES) complement VaR in assessing potential losses?
7. How do simulated outcomes change when the average return is treated as uncertain, and when real historical returns are re-used (block bootstrap) instead of a normal distribution?
8. How do optimistic and pessimistic assumptions about returns and volatility affect portfolio outcomes?
9. How do conservative, balanced, and growth-oriented portfolio allocations compare in terms of risk and return?

**Working hypothesis:** treating the historical average return as exactly known understates downside risk, because 11 years of data cannot pin down the true expected return.

## Tools and Technologies

- **Python**
- **Pandas**: data manipulation and analysis
- **NumPy**: numerical computation and simulation
- **Matplotlib**: visualization
- **yfinance**: downloading historical prices from Yahoo Finance
- **Jupyter Notebook**: analysis and documentation
- **Monte Carlo Simulation**: portfolio outcome modelling
- **Statistical Analysis**: returns, volatility, standard error, and downside risk

## Data

- **Source:** Yahoo Finance, downloaded with `yfinance`.
- **Prices:** adjusted for dividends and stock splits (`auto_adjust=True`), so returns reflect total return.
- **Period:** 2 January 2015 to 31 December 2025 (2,766 trading days, giving 2,765 daily returns).
- **Quality check:** no missing values before or after cleaning.

## Assets and Portfolio Allocation

The portfolio contains four exchange-traded funds representing different asset exposures.

| ETF | Asset exposure | Portfolio weight |
|---|---|---:|
| SPY | US large-cap equities | 40% |
| QQQ | Nasdaq-100 equities | 25% |
| BND | US investment-grade bonds | 25% |
| GLD | Gold | 10% |
| **Total** | | **100%** |

The analysis assumes an initial investment of \$100,000.

### Historical statistics (2015 to 2025)

| Asset | Arithmetic return | CAGR | Annual volatility |
|---|---:|---:|---:|
| SPY | 14.22% | 13.46% | 17.79% |
| QQQ | 19.46% | 18.57% | 21.98% |
| BND | 2.01% | 1.88% | 5.36% |
| GLD | 12.44% | 12.02% | 14.75% |
| **Portfolio (40/25/25/10)** | **12.30%** | **12.16%** | **12.83%** |

SPY and QQQ are highly correlated (0.93), while BND and GLD have very low correlation with the equity funds (0.04 to 0.11), which is where most of the diversification comes from.

The standard error of the portfolio's average annual return is **3.87%**. With about 11 years of data, the true expected return is uncertain by several percentage points, and this uncertainty is built into the base model.

## Methodology

### 1. Data Preparation and Exploratory Analysis

Historical prices were organized by date and used to calculate daily returns. The analysis examined historical annualized returns (arithmetic), compound annual growth rate (CAGR), annualized volatility, and correlations.

### 2. Portfolio Construction

A weighted portfolio was created using the four ETFs. The daily portfolio return series was calculated from the asset returns and portfolio weights.

The base portfolio assumes daily rebalancing to maintain the specified allocation.

### 3. Monte Carlo Simulation

The project simulated 10,000 possible portfolio paths over a 10-year horizon (2,520 trading days, using 252 trading days per year), with a fixed random seed (42) so results are repeatable.

Three simulation approaches were compared:

- **Normal Model with Fixed Mean:** uses a constant estimated mean return and normally distributed daily shocks.
- **Normal Model with Parameter Uncertainty:** each simulated path draws its own mean return from a normal distribution centred on the historical mean, with the standard error as its spread.
- **Block Bootstrap with Parameter Uncertainty:** resamples random 21-day blocks of historical portfolio returns (with the historical mean removed) to retain short-term dependence such as volatility clustering, and uses the same uncertain mean as the model above.

The block bootstrap model with parameter uncertainty was used as the base model for the main results.

### 4. Portfolio Outcome Analysis

The simulated ending portfolio values were summarized using percentiles to understand the range of possible outcomes.

The analysis also estimated:

- Probability of ending below the initial investment.
- Probability of reaching a target portfolio value of \$200,000.
- Median maximum drawdown.
- Probability of experiencing a drawdown of at least 20%.

### 5. Downside Risk Analysis

Value at Risk (VaR) and Expected Shortfall (ES) were calculated at the following horizons, each measured from the start of the simulation:

- 1 day
- 10 days
- 1 year
- 10 years

At the 95% confidence level, VaR is the loss exceeded in only 5% of simulated outcomes. Expected Shortfall is the average loss in that worst 5%.

The risk measures are reported without clipping negative values. A negative VaR indicates that the 5th-percentile return is positive under the simulation.

### 6. Scenario Analysis

Three scenarios were tested by changing the assumed annual return and a volatility multiplier, and re-running the base model.

| Scenario | Change in annual return | Volatility multiplier | Resulting average annual return |
|---|---|---|---|
| Optimistic | +2 percentage points | 0.90 | about 14.3% |
| Base | none | 1.00 | 12.3% |
| Pessimistic | -3 percentage points | 1.25 | about 9.3% |

This analysis examines how sensitive the simulated outcomes are to different assumptions. The pessimistic scenario is fairly mild, because it still assumes a positive annual return of about 9.3%.

### 7. Portfolio Allocation Comparison

Three allocations were compared:

| Portfolio | SPY | QQQ | BND | GLD |
|---|---:|---:|---:|---:|
| Conservative | 20% | 10% | 60% | 10% |
| Balanced | 40% | 25% | 25% | 10% |
| Growth | 50% | 35% | 5% | 10% |

All three were simulated with the base model, the same settings, the same seed, and 10,000 paths. They were evaluated using historical annualized return, annualized volatility, median simulated ending value, 5th-percentile ending value, probability of loss, and median maximum drawdown.

## Key Results

### Model Comparison

| Metric | Normal, fixed mean | Normal + parameter uncertainty | Bootstrap + parameter uncertainty (base) |
|---|---:|---:|---:|
| 5th-percentile ending value | \$160,464 | \$125,159 | \$130,816 |
| Median ending value | \$315,510 | \$317,888 | \$317,715 |
| 95th-percentile ending value | \$624,886 | \$811,014 | \$752,106 |
| Probability of ending below the initial investment | 0.15% | 2.18% | 1.66% |
| Probability of reaching \$200,000 | 86.47% | 78.80% | 80.79% |
| Median maximum drawdown | -20.25% | -20.33% | -20.86% |
| 5th-percentile maximum drawdown | -32.32% | -35.26% | -35.91% |
| Probability of at least a 20% drawdown | 52.13% | 52.03% | 55.18% |

The median barely changes between models, but the lower tail changes a lot. Allowing the average return to be uncertain raises the probability of loss from 0.15% to 2.18%, and the bootstrap, which re-uses real historical returns, produces slightly deeper drawdowns than the normal model.

### Base Model: Block Bootstrap with Parameter Uncertainty

Starting investment: **\$100,000**

| Metric | Result |
|---|---:|
| Median ending value after 10 years | \$317,715 |
| 5th-percentile ending value | \$130,816 |
| 95th-percentile ending value | \$752,106 |
| Probability of ending below the initial investment | 1.66% |
| Probability of reaching \$200,000 | 80.79% |
| Median maximum drawdown | -20.86% |
| Probability of at least a 20% drawdown | 55.18% |

These values are simulation estimates under the specified model assumptions, not forecasts or guarantees of future performance.

### Value at Risk and Expected Shortfall (95%, base model)

| Horizon | VaR (\$) | VaR (%) | ES (\$) | ES (%) |
|---|---:|---:|---:|---:|
| 1 day | \$1,220 | 1.22% | \$1,934 | 1.93% |
| 10 days | \$3,544 | 3.54% | \$5,561 | 5.56% |
| 1 year | \$8,297 | 8.30% | \$14,194 | 14.19% |
| 10 years | -\$30,816 | -30.82% | -\$5,274 | -5.27% |

The 1-year figures are the most useful for judging how bad a bad year can be. At the 10-year horizon the VaR and ES are negative, which means even the 5th-percentile path and the average of the worst 5% of paths end above the starting value. Expected Shortfall is always at least as large as VaR because it averages the outcomes beyond the VaR threshold.

### Scenario Analysis

| Scenario | Median ending value | 5th-percentile ending value | Probability of loss | Probability of reaching \$200,000 | Median maximum drawdown |
|---|---:|---:|---:|---:|---:|
| Optimistic | \$393,906 | \$177,322 | 0.23% | 91.86% | -17.57% |
| Base | \$317,715 | \$130,816 | 1.66% | 80.79% | -20.86% |
| Pessimistic | \$225,147 | \$74,026 | 11.29% | 57.13% | -29.55% |

The scenario results show that assumptions about returns and volatility can substantially affect the range of simulated outcomes. Moving from the base to the pessimistic case lowers the median by about \$92,600 and multiplies the probability of loss by roughly seven.

### Portfolio Allocation Comparison

| Portfolio | Historical annualized return (arithmetic) | Annualized volatility | Median ending value | 5th-percentile ending value | Probability of loss | Median maximum drawdown |
|---|---:|---:|---:|---:|---:|---:|
| Conservative | 7.24% | 7.25% | \$201,127 | \$119,553 | 1.43% | -12.90% |
| Balanced | 12.30% | 12.83% | \$317,715 | \$130,816 | 1.66% | -20.86% |
| Growth | 15.26% | 16.49% | \$405,368 | \$131,408 | 2.27% | -25.96% |

In this historical sample and simulation, the growth allocation had the highest historical return and median ending value, along with higher volatility and the deepest median drawdowns. The conservative allocation had the lowest return, volatility, and drawdowns.

Two points about reading this table:

- The 5th-percentile ending value should not be used alone to judge risk. The conservative portfolio has the lowest 5th percentile because it earns less, not because it is riskier. The drawdown column shows the real difference in risk.
- Return per unit of volatility is about the same for all three (roughly 1.00, 0.96, and 0.93, ignoring the risk-free rate), so the growth portfolio is not more efficient, only riskier.

## Key Findings

- Monte Carlo simulation provides a distribution of possible portfolio outcomes rather than a single forecast.
- Treating the historical average return as exactly known understates downside risk: the estimated probability of loss rises from 0.15% to about 2% once uncertainty in the mean return is included, while the median result hardly changes.
- Downside risk depends on the model assumptions and the investment horizon. Over 10 years even the worst 5% of outcomes can be gains, so 1-year VaR and ES are more informative.
- Scenario analysis demonstrates how changes in assumed returns and volatility affect simulated outcomes.
- Portfolio allocation involves a trade-off between historical return, volatility, and drawdown risk.
- A low probability of ending below the initial investment does not imply that substantial temporary losses cannot occur: more than half of simulated paths fall at least 20% from a peak at some point.

## Limitations

1. Historical returns from 2015 to 2025 may not represent future market conditions. This was an especially strong period for US equities and gold.
2. The simulation results depend on the selected models and their assumptions.
3. In the base model, the block bootstrap can only resample historical return observations and cannot generate individual daily returns more extreme than those in the historical sample. (The scenarios scale volatility, which goes beyond the historical range.)
4. Historical return blocks preserve dependence only within the chosen block length (21 days); future volatility and correlations may behave differently.
5. The analysis uses a historical portfolio return series rather than separately modelling each asset and its changing correlations.
6. Daily rebalancing is assumed, while actual rebalancing may occur less frequently and involve costs.
7. Taxes, fees, transaction costs, inflation, and withdrawals are excluded. Results are nominal, not inflation-adjusted.
8. Only four US-listed ETFs are included.
9. The uncertainty in the mean return is estimated assuming independent daily returns, which may slightly understate it when volatility clusters.
10. Simulation estimates do not guarantee or predict future investment results.

## Possible Future Improvements

- Incorporate contributions and withdrawals.
- Evaluate inflation-adjusted portfolio values.
- Explore GARCH volatility models and regime-switching approaches.
- Model asset-level returns and changing correlations.
- Compare periodic rebalancing strategies after transaction costs.
- Evaluate portfolio allocations using an efficient frontier.
- Conduct out-of-sample evaluation, for example fitting on 2015 to 2020 and testing against 2021 to 2025.
- Develop an interactive dashboard using Streamlit or Plotly.

## How to Run

1. Clone the repository and open `monte-carlo-portfolio-risk-simulation.ipynb` in Jupyter or Google Colab.
2. Install the dependencies:

   ```bash
   pip install yfinance pandas numpy matplotlib
   ```

3. Run all cells. An internet connection is needed to download the prices.

The random seed (42) makes the simulation repeatable. Results could differ slightly if Yahoo Finance revises its historical data.

## Conclusion

This project demonstrates the use of statistical analysis and Monte Carlo simulation to examine long-term portfolio outcomes and quantify investment risk.

By comparing simulation models, return and volatility scenarios, and portfolio allocations, the analysis illustrates how modelling assumptions and investment choices affect the distribution of possible outcomes.

The key takeaway is that historical performance alone is not sufficient to understand portfolio risk. Simulation provides a structured way to examine uncertainty, downside exposure, and potential trade-offs while recognizing the limitations of historical data and model assumptions.
