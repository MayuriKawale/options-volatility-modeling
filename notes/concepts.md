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
- **bid/ask:** Current market quotes, we use mid price (average of both)
- **lastPrice:** Avoid using this, may be stale from hours or days ago
- **volume:** Contracts traded today; liquidity indicator
- **openInterest:** Total outstanding contracts; stronger liquidity indicator
- **impliedVolatility:** Market's expectation of future volatility

## 10. Why Liquidity Matters for Options Data

- Liquid options have tight bid-ask spreads (e.g., $0.02)
- Illiquid options have wide spreads (e.g., $0.50)
- We use mid price = (bid + ask) / 2 as our market price
- Wide spread → unreliable mid price → corrupted implied vol
- SPY is ideal because it is the most liquid options market in the world
- Rule of thumb: always use the most liquid instrument available 
  for volatility analysis

### Why Mid Price Instead of Last Price?
- Last price could be from hours or days ago when market conditions 
  were different
- Bid and ask are live quotes reflecting current market
- Mid price = (bid + ask) / 2 gives the fairest current estimate

## 11. Put-Call Parity
- Call prices decrease as strike increases
- Put prices increase as strike increases  
- The two curves cross at the current stock price
- This is put-call parity: a fundamental no-arbitrage relationship
- If this relationship is violated in real data, something is wrong

## 12. Fear Asymmetry in Options Markets
- Open interest in SPY is heavily concentrated in downside puts
- Institutional investors buy puts as portfolio insurance (hedging)
- This is called the "fear trade" or "tail risk hedging"
- Markets price downside risk much higher than upside risk
- This creates the volatility skew; higher implied vol for lower strikes
- Black-Scholes cannot capture this with constant volatility assumption

## 13. Continuous Compounding and Discounting

### Where e^(rT) comes from:
- Simple annual compounding: V = P × (1 + r)^T
- Compounding n times per year: V = P × (1 + r/n)^(nT)
- As n → infinity (continuous compounding): V = P × e^(rT)
- e = 2.71828... is the natural limit of compounding infinitely frequently

### Why finance uses continuous compounding:
- Markets operate continuously, prices change every millisecond
- Exponentials are mathematically clean and easy to work with
- Consistent with stochastic calculus underlying Black-Scholes

### Present Value and Discounting:
- Future value: multiply by e^(rT), growing forward in time
- Present value: multiply by e^(-rT), discounting back in time
- K × e^(-rT) = what you'd need to invest today at rate r 
  to have exactly K in T years

### In Black-Scholes:
- K × e^(-rT) is the present value of the strike price
- We discount K because in an option contract, the strike is 
  paid in the future, not today

## 14. ATM Option Price Approximation

For at-the-money options (S ≈ K), Black-Scholes simplifies to:
```
ATM Price ≈ 0.4 × S × sigma × sqrt(T)
```

Where:
- S = current stock price
- sigma = volatility
- T = time to expiry in years
- 0.4 ≈ 1/sqrt(2π) comes from simplifying N(d1) - N(d2) when S = K

### Key Assumption:
- We set r = 0 (risk-free rate = 0) to simplify the derivation
- This makes e^(-rT) = e^0 = 1, eliminating the discounting term
- In reality r > 0, which is why our actual Black-Scholes result 
  ($18.55) is slightly higher than the approximation ($16.92)
- A non-zero r adds value to calls because you save on not having 
  to pay the strike price upfront, that money earns interest

### Derivation intuition:
- When S = K, log(S/K) = 0, simplifying d1 and d2
- d1 = -d2 = 0.5 × sigma × sqrt(T)
- N(d1) - N(d2) ≈ sigma × sqrt(T) × N'(0) = sigma × sqrt(T) / sqrt(2π)
- Final result: Price ≈ S × sigma × sqrt(T) × 0.3989 ≈ 0.4 × S × sigma × sqrt(T)

### Key insight:
- Three main drivers of ATM option price: S, sigma, and sqrt(T)
- Double the volatility → double the option price (approximately)
- Useful as a quick sanity check on option prices
- More accurate when r is small or T is short

## 15. Implied Volatility Extraction via Root Finding

### Core idea:
- We want sigma such that BS(sigma) = market_price
- Equivalent to finding root of: BS(sigma) - market_price = 0

### Objective function:
- Returns positive if BS price > market price (sigma too high)
- Returns negative if BS price < market price (sigma too low)
- Returns zero at the correct sigma (our answer)

### brentq algorithm:
- Finds root by repeatedly narrowing an interval [a, b]
- Requires opposite signs at endpoints (one positive, one negative)
- a = 1e-6 (near zero volatility lower bound)
- b = 10.0 (1000% volatility upper bound, covers all real cases)
- xtol = 1e-6 (stop when accurate to 0.0001%)
- Converges very quickly, typically ~50 iterations

### Why try/except:
- brentq fails if market price is outside BS bounds
- Happens for very deep ITM or OTM options
- We return np.nan and drop these contracts later to avoid skewing our analysis with bad data

## 16. Risk-Free Rate in Options Pricing
- Standard choice: 3-month US Treasury bill rate
- Considered risk-free, backed by US government
- Should match the maturity closest to your options expiry
- Changes over time, always look up current rate before analysis
- As of March 16, 2026: 3.69%
- Source: https://tradingeconomics.com/united-states/3-month-bill-yield

## 17. Applying Functions Across DataFrame Rows

### dataframe.apply(function, axis=1):
- Applies a function to every row one by one
- axis=1 means apply along rows (axis=0 would apply along columns)
- Equivalent to a for loop but cleaner and faster
- Each row is passed as input to the function

### Lambda functions:
- Compact way to define a single-use function without naming it
- lambda row: expression is equivalent to def f(row): return expression
- Preferred when function is only needed once

### Example pattern:
df['newColumn'] = df.apply(lambda row: some_function(row['col1'], row['col2']), axis=1)

## 18. Finding ATM Option (Closest Strike to Current Price)

### Pattern:
atm_idx = (df['strike'] - current_price).abs().argsort()[:1]
atm_vol = df.iloc[atm_idx]['impliedVol'].values[0]

### Step by step:
1. df['strike'] - current_price → distance of each strike from current price
2. .abs() → absolute distance (remove negative signs)
3. .argsort()[:1] → index of smallest distance (closest strike)
4. .iloc[...] → select that row
5. ['impliedVol'].values[0] → extract the implied vol value

### Why we need ATM vol:
- Used as reference point on volatility smile plot
- Represents Black-Scholes' best single volatility estimate
- All other strikes deviate from this, showing the smile

## 19. Volatility Skew vs Volatility Smile

### Volatility Smile:
- Symmetric U-shape implies higher IV for both low and high strikes
- Common in FX options markets

### Volatility Skew (what we see in SPY):
- Asymmetric means higher IV for low strikes, lower for high strikes
- Also called "smirk" or "skew"
- Common in equity markets
- Driven by fear asymmetry when investors hedge against crashes 
  but not against rallies

### What ATM vol represents:
- Black-Scholes uses one single constant vol for all strikes
- ATM vol (19.22% for SPY Apr 17) is the best single estimate
- But it overestimates vol for high strikes and underestimates 
  for low strikes
- SABR fixes this by fitting the entire skew shape

## 20. Why Put-Call Parity Doesn't Hold Perfectly in Practice

Theoretical put-call parity assumes:
- No dividends
- Constant risk-free rate
- Simultaneous price quotes
- No transaction costs

In practice small violations occur because:
- Stocks and ETFs pay dividends (not in basic BS formula)
- Interest rate term structure is not flat
- Bid/ask prices captured at different times
- Transaction costs and market frictions exist

Result: calls and puts give slightly different implied vols 
for the same strike, this is normal and expected

## 21. Forward Price vs Spot Price

### Spot Price:
- Current market price of the asset right now
- SPY spot price = $669.03

### Forward Price:
- Agreed price today for buying/selling asset at a future date
- Accounts for interest rates and dividends
- Formula: F = S × e^((r - q) × T)
  - S = spot price
  - r = risk-free rate
  - q = dividend yield
  - T = time to expiry in years

### Why forward price is higher than spot:
- You earn interest by keeping cash instead of buying now
- Seller needs compensation for this → forward price > spot

### Why dividends reduce forward price:
- Forward contract holder misses out on dividends
- This reduces the forward price relative to spot

### Why SABR uses forward price:
- Forward price is a martingale under the forward measure
- Makes stochastic calculus cleaner
- Interest rates and dividends already baked in
- No need to handle them separately inside the formula

### Example for our project:
F = 669.03 × e^((0.0369 - 0.013) × 0.0849) = $670.36

## 22. Hagan SABR Formula Structure

### Two cases:
1. ATM (F ≈ K): simplified formula, avoids log(F/K) = 0 issue
2. Non-ATM: full formula with three multiplied components

### ATM formula:
- term1 = alpha / F^(1-beta) → base vol level (backbone)
- term2 = 1 + (...) * T → time correction for beta, rho, nu effects
- result = term1 * term2

### Non-ATM formula has three components multiplied:
1. numerator/denominator → backbone term (base vol level)
  - FK_mid = (F×K)^((1-β)/2) → geometric midpoint
  - log_FK = log(F/K) → log moneyness
  - denominator corrections → Taylor expansion for accuracy

2. z/x(z) → skew term (generates the smile/skew shape)
  - z = (nu/alpha) × FK_mid × log_FK
  - x(z) = log(...) accounts for correlation effects
  - rho < 0 → downward skew (equity markets)

3. correction → time term (vol accumulates over time)
  - Same structure as ATM term2
  - Effects of beta, rho, nu grow with T

### Key insight:
- backbone × skew × time = SABR implied vol
- Each component has a clear financial interpretation