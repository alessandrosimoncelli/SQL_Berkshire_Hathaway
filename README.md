# SQL Berkshire Hathaway - Portfolio Optimization Analysis
Group project - Hult International Business School

## Purpose and Learning Objectives
This project was built as part of a data management course at Hult International Business School. The main goal was not portfolio optimization. It was to get hands-on experience with the full SQL workflow from scratch:
- **Database design** - structuring a relational schema to store financial time series data
- **DBMS setup** - configuring and connecting to a MySQL instance via MySQL Workbench (local server at 127.0.0.1, port 3306)
- **Relational model** - defining tables, primary keys, and data types suited for multi-ticker daily price data
- **Data ingestion** - pulling historical prices through Python (yfinance) and loading them into the database via bulk INSERT statements
- **SQL syntax** - writing SELECT, WHERE, GROUP BY, and aggregate queries to compute return, volatility, and correlation metrics directly in SQL

One important caveat worth stating upfront: SQL is a data storage and retrieval language, not a statistical computing environment. It can handle basic aggregates like averages and standard deviations, but deeper analysis like portfolio optimization or regression belongs in Python or R. The financial analysis in this project is intentionally illustrative. It shows how SQL-extracted metrics can feed a real investment use case, without pretending SQL is doing the heavy lifting that a proper quant tool would.

## The Brief
For more than six decades, Berkshire Hathaway's patience and discipline have built extraordinary wealth. This project does not challenge that approach. It tries to sharpen it.
Using SQL, we analyzed three years of daily pricing data across Berkshire's largest equity holdings to answer one question: can a half-trillion-dollar portfolio earn more while taking on less risk, without stepping outside Berkshire's own investment philosophy?
Our answer is yes, through a focused three-move equity rotation that improves the portfolio Sharpe ratio from **1.09 to 1.46**, raises annualized return by **7.6%**, and reduces volatility by **16%**, while leaving the 61% cash position completely untouched.

## Berkshire's Investment Philosophy
Every recommendation in this analysis passes through Berkshire's own filter. Understanding what Berkshire values and what it avoids is the foundation of the whole approach.

**What Berkshire values:**
- Durable economic moats
- Predictable and growing earnings
- High quality and trustworthy management
- Simple and understandable business models
- Long term buy and hold
- Fair price, not necessarily cheap

**What Berkshire avoids:**
- Assets that generate no cash flows
- Speculative and early stage companies
- High leverage and excessive debt
- Commoditized manufacturing and low margin retail
- Crypto and complex derivatives
- Micro and small caps

One point worth noting: Berkshire holds zero gold today, and that is entirely consistent with this philosophy. Gold generates no cash flows. Keep that in mind, because it becomes relevant when we get to our recommendations.


## Current Portfolio
As of March 31, 2026, Berkshire's portfolio is diversified across tech, banking, energy, and consumer sectors, but it is heavily anchored by cash. 61% of the fund sits in short-term Treasury bills earning around 4%. That cash position reflects both the fund's risk profile and its view on current market valuations. We decided not to touch it.
On the equity side, Apple leads in weight despite the fund having already been reducing its position. Coca-Cola and Chevron sit at the lower end, providing defensive stability. The goal of this analysis was to look at the equity sleeve specifically and ask whether the allocation could be made more efficient.

### Portfolio Snapshot

| Instrument | Weight | Sharpe |
| BIL (T-Bills) | 61% | 0.08 |
| AAPL | 12% | 0.58 |
| AXP | 9% | 0.74 |
| KO | 6% | 0.68 |
| BAC | 5% | 0.89 |
| GOOGL | 4% | 1.17 |
| CVX | 3% | 0.38 |
| **Portfolio** | | **1.09** |

## Portfolio Analysis

### Risk-Adjusted Performance
Looking at Sharpe ratios across the holdings, GOOGL stands out as the strongest performer at 1.17, the only equity clearly above the portfolio average of 1.09. BAC comes in second at 0.89. At the other end, CVX is the weakest equity at 0.38, and AAPL at 0.58 is the weakest among the larger positions. Not all holdings are pulling equal weight for the risk they carry.

### Correlation Analysis
Running a correlation matrix across the equities reveals two key redundancies:

**AXP and BAC move together at 0.68.** They are the most redundant pair in the portfolio. Holding both is effectively doubling one financial sector bet without getting any additional diversification in return.
**AAPL and GOOGL overlap at 0.41.** Both are tech names, and since GOOGL delivers more than double the risk-adjusted return, leaning on GOOGL over AAPL makes the tech exposure more efficient.
On the positive side, KO is the best diversifier in the book, with near-zero correlation across everything. CVX also moves independently, providing genuine energy sector diversification. These two are earning their place not through high returns but through low correlation.

## Recommendations
Three moves, funded entirely by trimming redundancy. The 61% cash position is not touched.

### 1. Trim AAPL by 6%
AAPL has the weakest Sharpe among the equities at 0.58, and it is 41% correlated with GOOGL which delivers more than double the risk-adjusted return at 1.17. Reducing Apple and leaning on the stronger tech name improves efficiency without losing tech exposure.

### 2. Trim AXP by 6%
AXP is the most redundant holding in the portfolio, with a 0.68 correlation to BAC and a lower Sharpe of 0.74 against BAC's 0.89. We keep the better performer and cut the overlap. To be clear, the trim is driven by redundancy, not by low Sharpe alone. That is why CVX stays despite a lower ratio. CVX is uncorrelated and earns its seat through diversification.

### 3. Add AEP and NEM at 6% each
**AEP - the AI infrastructure play without the AI price tag**
AI has been one of the dominant themes in markets, but semiconductor and chip names have become too expensive for Berkshire's value approach. That door is closed. But there is a second-order opportunity: data centers still need electricity. AEP is a regulated utility that benefits directly from rising power demand driven by AI infrastructure. It fits Berkshire's discipline, trades at a value price, and has a Sharpe of 0.79 that beats most current holdings. It is also decorrelated from everything else in the portfolio.

**NEM - gold exposure with cash flows**
Berkshire holds zero gold because gold generates no cash flows. NEM is different. As a gold miner, it has real revenues, real earnings, and a real P&L. It behaves like a gold hedge but passes Berkshire's cash flow filter. It adds a form of diversification the portfolio currently lacks entirely, moves independently from every other holding, and carries a Sharpe of 0.84.
Together, AEP and NEM outperform most current holdings on a risk-adjusted basis while adding two sector exposures Berkshire does not have at all today.

## Results
| Metric | Before | After | Change |
| Annualized Return | 11.43% | 12.30% | +7.6% |
| Annualized Volatility | 6.37% | 5.35% | -16.0% |
| Portfolio Sharpe | 1.09 | 1.46 | +33.9% |

Better returns, lower risk. That is a more efficient portfolio.

## Risks

**Opportunity cost of holding 61% in cash**
Keeping BIL at 61% is a deliberate and active choice, consistent with Berkshire's philosophy of waiting for the right moment. But it is not free. Cash earns roughly 4% while the equity sleeve historically returns closer to 10%. It remains the single biggest drag on the portfolio's blended return. We are not recommending deployment, but it is worth naming the trade-off explicitly.
**AEP sector concentration**
The investment case depends on AI-driven electricity demand continuing to grow. A slowdown in AI infrastructure spending or changes in energy regulation could hurt the position.
**NEM gold volatility**
NEM is a miner, not physical gold. It carries operational leverage, meaning it amplifies gold price moves in both directions. If gold sells off, NEM tends to fall harder than the underlying metal.


## Conclusion

Berkshire's discipline has built one of the greatest investment track records in history and nothing in this analysis changes that. No speculation, no leverage, no forecasting the unknowable. Every recommendation passes through the same filter Berkshire has always used.
By trimming two redundant positions and adding two high quality decorrelated diversifiers, one capturing AI-adjacent value through power demand and one providing cash-flow-backed gold exposure, the portfolio earns better returns at lower volatility while keeping the full 61% cash position ready to deploy when the moment comes.
The philosophy stays exactly the same. It just compounds a little faster.

## Technical Stack and Skills Developed

| Area | Detail |
| **DBMS** | MySQL, managed via MySQL Workbench |
| **Database** | hult_Capital_9, local server (127.0.0.1:3306) |
| **Main table** | pricing_daily_new - stores daily OHLCV data per ticker |
| **Data ingestion** | Python + yfinance to generate bulk SQL INSERT statements |
| **SQL concepts** | DDL (CREATE TABLE), DML (INSERT, SELECT), aggregate functions (AVG, STDDEV), subqueries, filtering, aliasing |
| **Relational model** | Composite primary key (ticker_date_type), normalized schema for multi-ticker time series |
| **Financial metrics** | Annualized return, annualized volatility, Sharpe ratio, pairwise correlation - all extracted via SQL queries |

---

## Methodology

**Metrics computed in SQL:**
- **Annualized return** - computed from daily adjusted closing prices over a 3-year window
- **Annualized volatility** - standard deviation of daily returns multiplied by the square root of 250
- **Sharpe ratio** - (annualized return minus risk-free rate) divided by annualized volatility, using BIL ETF's annualized yield as the T-Bill proxy
- **Correlation matrix** - pairwise Pearson correlation of daily returns across all equity holdings

## Scope and Data
> **Note:** This analysis covers a focused subset of Berkshire's portfolio, specifically the six largest equity holdings (AAPL, AXP, KO, BAC, GOOGL, CVX) plus a T-Bill sleeve proxied by BIL (SPDR Bloomberg 1-3 Month T-Bill ETF). Minor positions were excluded and weights were reallocated accordingly. The 61% cash weight applies to this normalized subset, not the full Berkshire portfolio.

**| Source | Usage |**
| Berkshire Hathaway 10-Q (Q1 2026) | Holdings, share counts, portfolio weights |
| CNBC Portfolio Tracker | Price and value cross-reference |
| yfinance via Python | 3-year daily adjusted close prices |
| BIL ETF yield | Risk-free rate proxy for Sharpe calculation |

**Snapshot date:** March 31, 2026


## Repository Contents

| File | Description |

| `_FINAL__SQL_CodeBerkshire.sql` | Data ingestion layer - bulk INSERT statements loading daily price data into pricing_daily_new |
| `SQL_Berkshire_Hathaway_Presentation.pdf` | Full slide deck including correlation matrix, Sharpe rankings, and before/after portfolio comparison |
| `README.md` | This file |

## Team

Group 9 - Hult International Business School, 2026


'SQL_Berkshire_Hathaway_Presentation.pdf`: Client-facing portfolio optimization presentation delivered to Berkshire Hathaway. Covers current portfolio analysis, SQL-driven quantitative findings, and three targeted recommendations to improve risk-adjusted returns while preserving the fund's core investment philosophy.

