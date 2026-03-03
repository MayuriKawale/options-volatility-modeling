## 1. Black-Scholes Model
- What it is: A mathematical model for pricing European options assuming 
  stock prices follow Geometric Brownian Motion
- Key assumptions:
  - Volatility is constant across all strikes and maturities
  - Returns are log-normally distributed
  - No jumps in price
  - Frictionless markets
- Limitations:
  - Real market data shows volatility is NOT constant across strikes
  - This inconsistency is visible in the volatility smile

## 2. Volatility Smile
- What it is: A pattern where implied volatility varies across different 
  strike prices for the same expiry date
- Shape: typically U-shaped (smile) or downward sloping (skew) 
  depending on the asset
- For equity indices like SPY: usually a skew - higher implied volality 
  for lower strikes, reflecting fear of market crashes
- Why Black-Scholes cannot reproduce it: BS assumes constant volatility, 
  so it produces a flat line across strikes by definition
- What it tells us: markets price in fatter tails and asymmetry that 
  BS ignores, indicating that the assumptions of BS are violated in practice.

## 3. Implied Volatility
- What it is: The volatility value that, when plugged into Black-Scholes, 
  reproduces the observed market option price
- How it differs from historical volatility: Historical vol is calculated 
  from past price movements. Implied volatility is forward-looking - it reflects 
  what the market expects future volatility to be
- How we extract it: Numerical root finding (scipy.optimize.brentq) 
  because there is no closed-form inverse of the BS formula
- Why it matters: Plotting implied volatility across strikes reveals the 
  volatility smile, showing where BS breaks down and how market expectations 
  differ from BS assumptions.

## 4. SABR Model
- What it is: A stochastic volatility model where both the asset price 
  and volatility evolve randomly and are correlated
- Stands for: Stochastic Alpha Beta Rho
- Key insight: by making volatility stochastic and correlated with price, 
  SABR naturally reproduces the volatility smile

### Parameters:
- Alpha (α): initial volatility level - shifts the entire smile up or down
- Beta (β): controls how volatility scales with price - fixed at 0.5 in our project
- Rho (ρ): correlation between price and volatility movements - negative for 
  equities (typical market fear of crashes) and downward skew, positive for 
  commodities (typical market fear of price spikes) and upward skew
- Nu (ν): volatility of volatility - controls the curvature/pronounced 
  shape of the smile

### How it fixes Black-Scholes limitations:
- BS assumes constant volatility → flat implied volatility across strikes
- SABR allows volatility to be stochastic and correlated with price → 
  produces the curved/skewed smile seen in real markets

## 5. Hagan Approximation Formula
- What it is: A closed-form approximation for SABR implied volatility
  derived by Hagan et al. in their 2002 paper "Managing Smile Risk"
- Takes as input: forward price F, strike K, time to expiry T, 
  and parameters (α, β, ρ, ν)
- Returns: implied volatility σ(F, K, T)
- Why it matters: without this approximation, SABR would require 
  expensive numerical simulation to price options
- Two main components:
  - Backbone term: how volatility level scales with price (controlled by Beta)
  - Smile correction term: curvature and skew (controlled by Rho and Nu)

## 6. Model Calibration
- What it means: finding SABR parameters that best fit real market data
- Analogy: identical to training a machine learning model
- Loss function: sum of squared differences between SABR implied volatilities 
  and market implied volatilities across all strikes
- Optimizer: scipy.optimize.minimize with L-BFGS-B method
- Parameter bounds:
  - Alpha > 0
  - -1 < Rho < 1
  - Nu > 0
  - Beta fixed at 0.5 (not calibrated)
- Goal: minimize loss function to find optimal (α, ρ, ν)

## 7. Model Comparison
- Metric used: RMSE (Root Mean Squared Error)
- Formula: sqrt(mean((model_vol - market_vol)^2)) across all strikes
- Interpretation: average deviation between model and market in 
  volatility units (e.g., 0.02 means model is off by 2 vol points on average)
- Black-Scholes RMSE: computed using flat ATM implied volatility for all strikes
- SABR RMSE: computed using calibrated SABR implied volatilities for all strikes
- Expected result: SABR should have significantly lower RMSE than BS, 
  demonstrating its superior fit to the volatility smile.

## 8. Options Expiry and Liquidity

### DTE (Days to Expiry)
- Too short (< 30 days): gamma dominated, noisy implied vols
- Too long (> 60 days): low liquidity, wide bid-ask spreads
- Sweet spot: 30-60 days for clean, reliable data

### Why Monthly Expiries Have High Liquidity
- Monthly options expire on the third Friday of each month
- Institutional hedging is structured around monthly cycles
- More participants = tighter spreads = more reliable prices
- Hierarchy: Monthly > Weekly > Quarterly > LEAPS in terms of 
  day-to-day liquidity

## 9. Options Chain Structure

Key columns in an options chain:
- **strike:** The price at which the option can be exercised
- **bid/ask:** Current market quotes — we use mid price (average of both)
- **lastPrice:** Avoid using — may be stale from hours or days ago
- **volume:** Contracts traded today — liquidity indicator
- **openInterest:** Total outstanding contracts — stronger liquidity indicator
- **impliedVolatility:** Market's expectation of future volatility

### Why Mid Price Instead of Last Price?
- Last price could be from hours or days ago when market conditions 
  were different
- Bid and ask are live quotes reflecting current market
- Mid price = (bid + ask) / 2 gives the fairest current estimate