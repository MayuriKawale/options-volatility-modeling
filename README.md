# Options Volatility Modeling
### Comparing Black-Scholes and SABR Models on Real Market Data

## Project Overview
This project explores the limitations of the Black-Scholes options pricing 
model and demonstrates how the SABR (Stochastic Alpha Beta Rho) stochastic 
volatility model better captures real market behavior.

Using real SPY (S&P 500 ETF) options data, we:
- Extract implied volatilities to visualize the volatility smile
- Implement and calibrate the SABR model to real market data
- Compare Black-Scholes vs SABR quantitatively using RMSE

## Key Results
- **Black-Scholes RMSE:** 11.03 vol points
- **SABR RMSE:** 1.02 vol points
- **SABR is 90.78% more accurate than Black-Scholes**

SABR captures the volatility skew that Black-Scholes completely misses,
demonstrating why stochastic volatility models are essential in practice.

## Repository Structure
```
options-volatility-modeling/
├── notebooks/
│   └── main.ipynb              # Main analysis notebook
├── notes/    
│   └── concepts.md             # Key concepts and learning notes
├── data/                       # Raw options data (not tracked by git)
├── references/    
│   └── papers.md               # Papers and reading materials
└── requirements.txt            # Python dependencies
```

## Models Covered
### Black-Scholes Model
- Assumes constant volatility across all strikes
- Produces a flat implied volatility line
- Cannot capture the volatility smile/skew

### SABR Model (Hagan et al. 2002)
- Stochastic volatility model
- Parameters: Alpha (vol level), Beta (fixed=0.5), Rho (skew), Nu (curvature)
- Calibrated to real market data using L-BFGS-B optimization
- Captures the volatility skew seen in real equity markets

## Data
- **Underlying:** SPY (S&P 500 ETF)
- **Expiry:** April 17, 2026
- **Data collected:** March 18, 2026
- **Source:** Yahoo Finance via yfinance

## Getting Started

### Prerequisites
- Python 3.8 or higher
- Git

### Installation

1. Clone the repository
```bash
git clone https://github.com/MayuriKawale/options-volatility-modeling.git
cd options-volatility-modeling
```

2. Create and activate a virtual environment

   **Mac/Linux:**
```bash
python -m venv venv
source venv/bin/activate
```

   **Windows (Command Prompt):**
```bash
python -m venv venv
venv\Scripts\activate.bat
```

3. Install required packages
```bash
pip install numpy pandas scipy matplotlib yfinance jupyterlab
#pip install -r requirements.txt
```

4. Launch Jupyter Notebook
```bash
jupyter lab
```

5. Open `notebooks/main.ipynb` and run the cells in order

## Learning Resources
See `notes/concepts.md` for detailed explanations of all concepts covered
including Black-Scholes, implied volatility, the volatility smile, SABR
model parameters, calibration, and model comparison.

See `references/papers.md` for all papers and resources used.

## References
- Hagan, P., Kumar, D., Lesniewski, A., & Woodward, D. (2002). 
  Managing Smile Risk. Wilmott Magazine, 84-108.
- Black, F., & Scholes, M. (1973). The Pricing of Options and 
  Corporate Liabilities. Journal of Political Economy, 81(3), 637-654.

## Author
Mayuri Kawale
Erdos Institute Quant Finance Boot Camp, Spring 2026