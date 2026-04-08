# Portfolio Optimizer ™ by AEG

A complete portfolio optimization tool in Python based on the **Markowitz Modern Portfolio Theory**.  
Select financial assets, retrieve historical data, simulate thousands of portfolios and find the optimal allocation.

![Dashboard example](docs/portfolio_result.png)

---

## Features

- Interactive GUI to search and select assets (equities, ETFs, indices, crypto, currencies)
- Historical data download via Yahoo Finance
- Monte Carlo simulation of 8 000 random portfolios
- Efficient Frontier computation
- Tangent Portfolio (max Sharpe) and Minimum Variance Portfolio
- Full graphical dashboard exported as PNG

---

## Installation

```bash
git clone https://github.com/your-username/portfolio-optimizer.git
cd portfolio-optimizer
pip install -r requirements.txt
```

---

## Usage

```bash
python3 portfolio_optimizer.py
```

1. Search and add at least 2 assets in the GUI
2. Set the analysis period and risk-free rate
3. Click **RUN ANALYSIS**
4. The dashboard is saved as `portfolio_result.png`

---

## Project Structure

```
portfolio-optimizer/
├── portfolio_optimizer.py   # Main script
├── requirements.txt
├── README.md
├── .gitignore
├── LICENSE
└── docs/
    ├── fiche_projet.pdf     # Project report
    └── fiche_projet.tex
```

---

## Dependencies

| Library | Purpose |
|---|---|
| `numpy` | Matrix algebra |
| `pandas` | Time series handling |
| `scipy` | SLSQP optimization |
| `matplotlib` | Dashboard visualization |
| `yfinance` | Market data download |
| `financedatabase` | Asset catalog |
| `tkinter` | Graphical interface |

---

## Theory

The optimizer is based on the **Markowitz (1952)** framework:

- **Portfolio return:** $R_p = w^T \mu$
- **Portfolio volatility:** $\sigma_p = \sqrt{w^T \Sigma w}$
- **Sharpe ratio:** $S = \frac{R_p - R_f}{\sigma_p}$

The efficient frontier is traced by solving:

$$\min_w \ w^T \Sigma w \quad \text{s.t.} \quad w^T \mu = R_{\text{target}}, \quad \sum w_i = 1$$

See [`docs/Portfolio_Optimization_Markowitz.pdf`](docs/Portfolio_Optimization_Markowitz.pdf) for the full mathematical write-up.

---

## License

MIT — © 2026 Adam El Gbouri


# CFCAP — Commodity Forward Curve Analytics Platform ™ by AEG
A complete commodity forward curve analysis tool in Python covering **65+ commodities** across 8 asset classes.  
Select a commodity, retrieve live futures data, fit the Nelson-Siegel model and generate a full quantitative dashboard with 51 trading signals.

![Dashboard example](docs/dashboard_example.png)

---

## Features

- Interactive GUI to select commodity family and contract (Energy, Metals, Agriculture, Freight, Carbon...)
- Live futures data via Yahoo Finance and TradingView (automatic routing per commodity)
- Nelson-Siegel curve fitting with robust multi-attempt strategy
- Implied convenience yield and roll yield — full term structure
- Calendar spreads M+1−M with backwardation/contango classification
- 51 trading signals across 11 categories (carry, spreads, arbitrage, hedging, risk, temporal)
- EIA fundamentals overlay — US crude/gas inventories, production, spot prices
- Interactive Streamlit dashboard with Plotly charts and historical date comparison
- Daily scheduler with CSV persistence for J-7/J-14 real comparisons
- Full graphical dashboard exported as PNG

---

## Installation

```bash
git clone https://github.com/your-username/cfcap.git
cd cfcap
pip install -r requirements.txt
```

For TradingView-sourced contracts (Jet Kerosene, LME metals, TTF Gas, Carbon...):
```bash
pip install git+https://github.com/StreamAlpha/tvdatafeed.git
```

---

## Usage

```bash
# Interactive dialog + matplotlib dashboard (PNG export)
python cfcap.py

# Interactive browser dashboard (Streamlit)
streamlit run cfcap.py

# Single commodity via CLI
python cfcap.py --commodity "WTI Crude Oil" --family "Energy" --rf 4.25

# Daily scheduler — runs automatically at 09:15 EST
python cfcap.py --schedule

# List saved historical snapshots
python cfcap.py --list
```

1. Select an asset class and commodity in the dialog
2. Set the risk-free rate and number of months forward
3. Click **RUN ANALYSIS**
4. The dashboard is saved in `data/dashboards/<commodity>/`

---

## Project Structure

```
cfcap/
├── cfcap.py                 # Main application (single-file)
├── requirements.txt
├── README.md
├── .gitignore
├── LICENSE
├── config.yaml.example      # Configuration template (copy to config.yaml)
└── docs/
    └── dashboard_example.png
```

Data is generated automatically at runtime and excluded from version control:
```
data/                        # auto-created, gitignored
├── curves/<commodity>/      # daily CSV snapshots
├── dashboards/<commodity>/  # PNG exports
├── eia_cache/               # EIA API cache (24h TTL)
└── logs/                    # scheduler logs
```

---

## Commodities Covered

| Family | Commodities | Source |
|---|---|---|
| Energy | WTI, Brent, Natural Gas, RBOB, Heating Oil, Gasoil | Yahoo Finance |
| Energy+ | Jet Kerosene, TTF, NBP, Coal API2/API4, Uranium | TradingView |
| Metals | Gold, Silver, Copper, Platinum, Palladium | Yahoo Finance |
| Base Metals | LME Copper, Aluminum, Zinc, Nickel, Lead, Tin, Cobalt | TradingView |
| Agriculture | Corn, Wheat, Soybeans, Sugar, Coffee, Cocoa, Cotton | Yahoo Finance |
| Agriculture+ | Soybean Oil/Meal, Oats, OJ, Cattle, Hogs, Lumber, Palm Oil | Yahoo Finance / TradingView |
| Freight | Capesize, Panamax, Supramax, VLCC | TradingView |
| Carbon | EU EUA, UK UKA, California CCA, RGGI | TradingView |

---

## Dependencies

| Library | Purpose |
|---|---|
| `numpy` | Numerical computations, Nelson-Siegel fitting |
| `pandas` | DataFrames, CSV persistence, historical data |
| `scipy` | Curve fitting (`curve_fit`), spline interpolation |
| `matplotlib` | 4-panel PNG dashboard |
| `requests` | EIA API calls |
| `yfinance` | Live futures data (Yahoo Finance) |
| `streamlit` | Interactive browser dashboard |
| `plotly` | Interactive charts in Streamlit |
| `schedule` | Daily scheduler automation |
| `tkinter` | Desktop selection dialog (built-in) |

---

## Theory

The forward curve analytics are based on the **Nelson-Siegel (1987)** parametric model:

$$F(T) = \beta_0 + \beta_1 \cdot \frac{1 - e^{-T/\tau}}{T/\tau} + \beta_2 \cdot \left[\frac{1 - e^{-T/\tau}}{T/\tau} - e^{-T/\tau}\right]$$

Where:
- $\beta_0$ — long-term level (fair value as $T \to \infty$)
- $\beta_1$ — slope (short-term component, $\beta_0 + \beta_1$ = spot level)
- $\beta_2$ — curvature (medium-term hump or valley)
- $\tau$ — decay parameter (speed of mean-reversion)

The **implied convenience yield** is derived from the cost-of-carry model:

$$cy(T) = r + u - \frac{1}{T} \ln\left(\frac{F(T)}{S}\right)$$

Where $r$ is the risk-free rate, $u$ the storage cost, $S$ the spot price and $F(T)$ the $T$-maturity futures price.

The **roll yield** measures the annualised return from rolling a long futures position:

$$ry(T) = \frac{S - F(T)}{F(T) \cdot T}$$

---

## License

MIT — © 2026 Adam El Gbouri
