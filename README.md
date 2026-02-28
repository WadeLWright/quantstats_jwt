# QuantStats JWT

**QuantStats JWT** is a specialized fork of [QuantStats by Ran Aroussi](https://github.com/ranaroussi/quantstats), currently maintained by **Just Wright Technologies**. It performs portfolio profiling to allow quants and portfolio managers to understand their performance better by providing them with in-depth analytics and risk metrics.

## Features & Customizations

This fork includes several specific improvements over the original and the `quantstats_lumi` forks:
* **Updated Tear Sheets**: Improved HTML layouts for parameter display and clearer metric organization.
* **Modern Visualizations**: Switched legacy "Volatility Matched" charts to default to **log-scale cumulative return** charts for better interpretability.
* **Graceful Fallbacks**: Better handling of scenarios with "no-trades" or negligible returns during report generation.
* **Modern Package Management**: Managed via [`uv`](https://github.com/astral-sh/uv) and `pyproject.toml` instead of legacy `setup.py`.

## Modules

QuantStats JWT is comprised of 3 main modules:
1. `quantstats_jwt.stats` - for calculating various performance metrics, like Sharpe ratio, Win rate, Volatility, etc.
2. `quantstats_jwt.plots` - for visualizing performance, drawdowns, rolling statistics, monthly returns, etc.
3. `quantstats_jwt.reports` - for generating metrics reports, batch plotting, and creating HTML tear sheets.

## Installation

This project is managed using `uv`. You can add it to your project via:

```bash
uv add quantstats-jwt
```
*Or via pip if you prefer:*
```bash
pip install quantstats-jwt
```

## Quick Start

```python
import quantstats_jwt as qs

# Fetch some sample data
stock = qs.utils.download_returns('SPY')

# Generate a tearsheet
qs.reports.html(stock, title='SPY Tearsheet', output='SPY_tearsheet.html')
```

## Development

To set up the development environment, ensure you have [`uv`](https://github.com/astral-sh/uv) installed, then run:

```bash
# Sync dependencies
uv sync

# Build the project distributions (sdist & wheel)
uv build
```