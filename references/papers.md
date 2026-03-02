# References and Reading Materials

## Primary Paper

### 1. Managing Smile Risk - Hagan, Kumar, Lesniewski, Woodward (2002)
- **What it is:** The original paper that introduced the SABR model
- **Why it matters:** This is the foundational reference for the entire 
  project. Contains the Hagan approximation formula we implement.
- **What to read:** Introduction (motivation) and Section 2 (the formula)
- **Link:** https://www.researchgate.net/publication/235622541_Managing_Smile_Risk
- **Citation:** Hagan, P., Kumar, D., Lesniewski, A., & Woodward, D. (2002). 
  Managing Smile Risk. Wilmott Magazine, 84-108.

---

## Background Reading

### 2. Black-Scholes Original Paper (1973)
- **What it is:** The original Black-Scholes paper introducing the options 
  pricing model
- **Why it matters:** Foundation of everything we build on and compare against
- **Link:** https://www.cs.princeton.edu/courses/archive/fall09/cos323/papers/black_scholes73.pdf
- **Citation:** Black, F., & Scholes, M. (1973). The Pricing of Options and 
  Corporate Liabilities. Journal of Political Economy, 81(3), 637-654.

### 3. The Volatility Surface - Jim Gatheral (Book)
- **What it is:** A book covering volatility modeling in depth
- **Why it matters:** Chapter 1 gives excellent intuition for the volatility 
  smile and why Black-Scholes fails
- **Where to find it:** Most university library systems or Google Scholar

---

## Online Resources

### 4. SABR Model - Wikipedia
- **What it is:** Clean summary of the SABR model with the Hagan formula 
  written out clearly
- **Why it matters:** Good starting point before reading the original paper
- **Link:** https://en.wikipedia.org/wiki/SABR_volatility_model

### 5. Implied Volatility - Investopedia
- **What it is:** Beginner friendly explanation of implied volatility
- **Link:** https://www.investopedia.com/terms/i/iv.asp

### 6. Volatility Smile - Investopedia
- **What it is:** Explanation of the volatility smile and why it exists
- **Link:** https://www.investopedia.com/terms/v/volatilitysmile.asp

---

## Python Libraries Used

### 7. yfinance Documentation
- **What it is:** Python library for fetching market data from Yahoo Finance
- **Link:** https://ranaroussi.github.io/yfinance/

### 8. scipy.optimize Documentation
- **What it is:** Used for root finding (brentq) and optimization (minimize)
  in our implied vol extraction and SABR calibration
- **Link:** https://docs.scipy.org/doc/scipy/reference/optimize.html

### 9. NumPy Documentation
- **Link:** https://numpy.org/doc/

### 10. Pandas Documentation
- **Link:** https://pandas.pydata.org/docs/

### 11. Matplotlib Documentation
- **Link:** https://matplotlib.org/stable/index.html

---

## How to Use These References

- Start with resources **4, 5, 6** if you are completely new to the concepts
- Read resource **1** (Hagan paper) before implementing the SABR formula
- Use resources **7-11** when working on the Python implementation
- Resource **3** (Gatheral book) is optional but highly recommended for 
  deeper understanding