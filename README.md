# VaR, CVaR, and Stress Testing of a Mutual Fund Portfolio

This project develops a historical-simulation framework for analyzing the risk of a mutual fund portfolio. The main objective is to calculate Value-at-Risk (VaR), Conditional Value-at-Risk (CVaR), and perform sector-level stress testing for an equally weighted portfolio selected from a universe of mutual funds.

The project is implemented in a Jupyter Notebook and follows the requirements of Term Project 1: VaR, CVaR, and Stress Test via Simulation.

## Project Overview

A portfolio manager invests an initial amount of `$1,000,000` equally across `d` mutual funds selected from the available fund universe. The number of selected funds `d`, investment horizon `tau`, rolling step `delta`, start date, end date, and confidence level `alpha` are user-defined parameters.

The notebook constructs historical horizon returns, combines selected fund returns into a portfolio return distribution, and estimates portfolio downside risk using VaR and CVaR. It also extends the analysis by applying sector-level stress shocks and comparing stressed risk metrics with the base-case results.

## Project Structure

```text
TERMPROJECT/
│
├── .venv/                                      # Python virtual environment
│
├── data/                                      # Provided mutual fund data
│   ├── us_bond_intermediate_core_adj_close.csv
│   ├── us_bond_intermediate_core_credit_quality.csv
│   ├── us_bond_intermediate_fixed_income_duration.csv
│   ├── us_bond_intermediate_fixed_income_primary.csv
│   ├── us_bond_intermediate_fund_size.csv
│   ├── us_bond_intermediate_meta_data.csv
│   ├── us_bond_intermediate_net_expense_ratio.csv
│   ├── us_equity_adj_close.csv
│   ├── us_equity_economic_region_exposure.csv
│   ├── us_equity_fund_size.csv
│   ├── us_equity_intermediate_meta_data.csv
│   ├── us_equity_net_expense_ratio.csv
│   ├── us_equity_sectors.csv
│   └── us_equity_styles.csv
│
├── .gitattributes
├── TermProject1_RiskManagement.pdf            # Project requirement document
├── VaR_cVaR.ipynb                             # Main notebook
└── README.md                                  # Project documentation
```

## Data

The project uses the provided mutual fund dataset. The main price data files used in the notebook are:

```text
us_equity_adj_close.csv
us_bond_intermediate_core_adj_close.csv
```

Additional metadata and exposure files are used for sector exposure and stress testing:

```text
us_equity_sectors.csv
us_bond_intermediate_fixed_income_primary.csv
```

The analysis combines equity and bond mutual fund data into a single fund universe and then selects `d` eligible funds for portfolio construction.

## User-Defined Parameters

The main parameters are defined in the notebook:

```python
start_date = "2005-12-31"
end_date = "2024-12-31"

d = 10
tau = 252
delta = 22
alpha = 0.05
initial_value = 1_000_000
random_seed = 42
universe_size = 50
```

Where:

- `start_date`: beginning date of the analysis window
- `end_date`: ending date of the analysis window
- `d`: number of mutual funds selected in the portfolio
- `tau`: investment horizon measured in trading days
- `delta`: rolling step size
- `alpha`: tail probability used for VaR and CVaR
- `initial_value`: initial portfolio value
- `random_seed`: seed used for reproducible fund selection
- `universe_size`: number of funds in the available universe

Typical rolling step choices are:

```text
delta = 1    # daily rolling
delta = 5    # weekly rolling
delta = 22   # monthly rolling
delta = 66   # quarterly rolling
```

## Methodology

### 1. Data Loading and Trading Calendar Construction

The notebook first loads adjusted close price data for equity and bond mutual funds. The two datasets are combined into a single price matrix. A valid trading calendar is then constructed from available price dates.

Calendar dates entered by the user are mapped to valid trading dates. The start date is mapped forward to the next available trading day, while the end date is mapped backward to the most recent available trading day.

### 2. Historical Horizon Return Distribution

For each mutual fund, the notebook calculates horizon returns over the selected investment horizon `tau`.

For a fund price series, the horizon return is calculated as:

```text
return = (future price - current price) / current price
```

The return windows are sampled using the rolling step `delta`. This creates a historical distribution of long-horizon returns for each eligible mutual fund.

### 3. Portfolio Return Distribution

After selecting `d` mutual funds, the notebook constructs an equally weighted portfolio. The portfolio return for each historical scenario is calculated as the average return of the selected funds.

```text
portfolio return = average of selected fund returns
```

This produces a historical distribution of portfolio returns.

### 4. VaR and CVaR Calculation

The notebook converts portfolio returns into dollar losses using the initial portfolio value:

```text
loss = - initial portfolio value × portfolio return
```

VaR is calculated as the empirical loss quantile at the selected confidence level. CVaR is calculated as the average loss conditional on losses exceeding the VaR threshold.

Returns are not annualized in the VaR and CVaR calculation because the risk metrics are intended to represent the actual dollar loss over the chosen investment horizon `tau`.

### 5. Portfolio Size and Diversification Analysis

The notebook also compares VaR and CVaR across different portfolio sizes:

```python
d_values = [5, 10, 20, 30, 40, 50]
```

For each portfolio size, the simulation is repeated multiple times with different random selections of funds. The average VaR and CVaR are then compared to study the diversification effect.

### 6. Sector Stress Testing

The project performs sector-level stress testing using available fund-level sector exposure data. Since the dataset does not include full security-level holdings, the notebook uses fund-level sector exposure as an approximation.

The stress test applies a negative shock to the portfolio's largest sector exposure. The stressed return is adjusted based on each fund's exposure to the selected sector. The notebook then recalculates VaR and CVaR under the stressed scenario.

The stress test includes:

```text
- A single -10% sector shock
- A comparison between base-case and stressed VaR/CVaR
- A sensitivity analysis using multiple shock levels
```

The shock sensitivity analysis tests different sector shock sizes, such as:

```python
stress_shocks = [-0.05, -0.10, -0.15, -0.20, -0.25, -0.30]
```

A denser version of the stress test is also included to produce a smoother line plot of VaR and CVaR changes across shock levels.

## Main Functions

The notebook is organized around the following helper functions:

```text
map_to_trading_date()              # Maps calendar dates to valid trading dates
resolve_alpha()                    # Standardizes alpha input
validate_inputs()                  # Checks user-defined parameters
window_positions()                 # Builds rolling return windows
eligible_symbols()                 # Finds funds with sufficient valid data
choose_universe_and_portfolio()    # Selects the fund universe and portfolio
historical_return_distribution()   # Computes fund-level horizon returns
portfolio_distribution()           # Computes equally weighted portfolio returns
var_cvar_from_returns()            # Calculates VaR and CVaR from portfolio losses
load_latest_sector_exposure()      # Loads latest available sector exposure
portfolio_sector_exposure()        # Computes portfolio-level sector exposure
stress_test_sector()               # Applies sector shock and recalculates risk
```

## How to Run

1. Clone or download the project folder.
2. Make sure the `data/` folder is in the same directory as the notebook.
3. Install the required Python packages.

```bash
pip install numpy pandas matplotlib
```

4. Open the Jupyter Notebook.

```bash
jupyter notebook VaR_cVaR.ipynb
```

5. Run all cells in order.

## Dependencies

This project requires:

```text
Python 3.x
numpy
pandas
matplotlib
Jupyter Notebook
```

## Results Summary

The notebook produces several risk analysis outputs:

```text
- Historical portfolio return distribution
- Portfolio loss distribution histogram
- VaR and CVaR estimates
- Portfolio-size diversification comparison
- Sector exposure table
- Base-case vs stressed VaR/CVaR comparison
- Stress shock sensitivity bar chart
- Dense stress shock sensitivity line chart
```

The base-case VaR and CVaR measure the downside risk of the selected mutual fund portfolio under historical simulation. The diversification analysis shows how risk changes as the number of selected funds increases. The stress test shows that stronger negative sector shocks lead to larger VaR and CVaR, meaning the portfolio becomes more exposed to tail losses under adverse sector movements.

## Limitations

This project uses historical simulation, so the estimated risk depends on past market behavior. Extreme future events that are not represented in the historical sample may not be fully captured.

The stress test is based on fund-level sector exposure rather than complete security-level holdings. Therefore, the sector stress results should be interpreted as an approximation of portfolio sensitivity rather than a fully detailed holdings-based stress test.

## Future Extensions

Possible extensions include:

```text
- Testing different rolling steps delta
- Comparing one-year, three-year, and five-year investment horizons
- Running multi-sector stress tests
- Adding Monte Carlo resampling from the empirical return distribution
- Comparing equal-weighted portfolios with value-weighted portfolios
- Testing different confidence levels alpha
- Adding more visualizations for VaR/CVaR sensitivity
```

## Author

Shengsheng He

Chen Ye
