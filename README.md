# SQL Berkshire Hathaway - Portfolio Optimization Analysis

## Purpose & Learning Objectives

This project was developed as part of a data management course at Hult International Business School. The primary objective was **not** portfolio optimization, it was to get hands-on experience with the full SQL workflow:
- **Database design**: structuring a relational schema to store financial time series data
- **DBMS setup**: configuring and connecting to a MySQL instance (MySQL Workbench)
- **Relational model**: defining tables, primary keys, and data types appropriate for financial time series
- **Data ingestion**: loading historical daily price data via bulk INSERT statements generated through Python (yfinance library)
- **SQL syntax**: writing SELECT, WHERE, GROUP BY, and aggregate queries to extract return, volatility, and correlation metrics from raw price data

SQL is fundamentally a data storage and retrieval language, not a statistical computing environment. The Sharpe ratios and correlation figures extracted here represent what SQL can reasonably produce; deeper portfolio optimization would typically be handled downstream in Python or R. The portfolio analysis built on top of this infrastructure is illustrative of how SQL output can feed a real financial use case.

## Overview

For more than six decades, Berkshire Hathaway's patience and discipline have built extraordinary wealth. This project doesn't challenge that - it sharpens it.
Using SQL, we analyzed three years of daily pricing data across Berkshire's top equity holdings to answer one question: **can a half-trillion-dollar portfolio earn more while risking less, without going outside Berkshire's investment philosophy?**
The answer is yes, through a focused, three-move equity rotation that improves the portfolio Sharpe ratio from **1.09 to 1.46**, raises annualized return by **7.6%**, and cuts volatility by **16%**, while leaving the 61% cash position entirely untouched.

## Technical Stack & Skills Developed

| Area | Detail |
| **DBMS** | MySQL, managed via MySQL Workbench |
| **Database** | `hult_Capital_9`, local server (127.0.0.1:3306) |
| **Table** | `pricing_daily_new` - stores daily OHLCV data per ticker |
| **Data ingestion** | Python + yfinance → bulk SQL INSERT statements |
| **SQL concepts** | DDL (CREATE TABLE), DML (INSERT, SELECT), aggregate functions (AVG, STDDEV), subqueries, filtering, aliasing |
| **Relational model** | Composite primary key (`ticker_date_type`), normalized schema for multi-ticker time series |
| **Financial metrics** | Annualized return, annualized volatility, Sharpe ratio, pairwise correlation - all computed via SQL queries |

## Methodology

**Metrics computed in SQL:**
- **Annualized return** - computed from daily adjusted closing prices over a 3-year window
- **Annualized volatility** - standard deviation of daily returns × √250
- **Sharpe ratio** - (annualized return − risk-free rate) / annualized volatility, using BIL's annualized yield as the T-Bill proxy
- **Correlation matrix** - pairwise Pearson correlation of daily returns across all equity holdings

**Portfolio construction:**
- Weighted Sharpe calculated using portfolio weights from the normalized 7-instrument subset model
- Before/after comparison computed by substituting AEP and NEM for the trimmed AAPL and AXP allocations

## Scope & Data

> **Note:** This analysis is based on a focused investable universe of Berkshire's **six largest equity holdings** (AAPL, AXP, KO, BAC, GOOGL, CVX) plus a **T-Bill sleeve proxied by BIL** (SPDR Bloomberg 1-3 Month T-Bill ETF). Minor positions were excluded and weights were reallocated accordingly. The normalized cash weight within this subset is **61%**; the full portfolio cash figure differs.

| Source | Usage |
| Berkshire Hathaway 10-Q (Q1 2026) | Holdings, share counts, portfolio weights |
| CNBC Portfolio Tracker | Price and value cross-reference |
| yfinance / SQL ingestion | 3-year daily adjusted close prices |
| BIL ETF yield | Risk-free rate proxy for Sharpe calculation |

**Snapshot date:** March 31, 2026

## Key Findings

### Current Portfolio
| Instrument | Weight | Sharpe |
| BIL (T-Bills) | 61% | 0.08 |
| AAPL | 12% | 0.58 |
| AXP | 9% | 0.74 |
| KO | 6% | 0.68 |
| BAC | 5% | 0.89 |
| GOOGL | 4% | 1.17 |
| CVX | 3% | 0.38 |
| **Portfolio** | | **1.09** |

### Three Recommendations
1. **Trim AAPL -6%** - weakest equity Sharpe (0.58), 41% correlated with GOOGL which delivers 2× the risk-adjusted return
2. **Trim AXP -6%** - most redundant holding (0.68 correlation with BAC), lower Sharpe (0.74 vs BAC's 0.89)
3. **Add AEP +6% and NEM +6%** - both outperform most current holdings on Sharpe (0.79 and 0.84), decorrelated from existing positions, and add two exposures the portfolio currently lacks entirely

**AEP rationale:** AI chip stocks are too expensive for Berkshire's value discipline - but data centers still need electricity. AEP is a regulated utility that captures AI infrastructure demand at a value price.
**NEM rationale:** Berkshire avoids gold because it generates no cash flows. NEM does - it's a gold miner with real earnings and a real P&L. It provides a decorrelated hedge without violating the "no non-cash-flow assets" rule.

### Optimized Portfolio Results
| Metric | Before | After | Change |
| Annualized Return | 11.43% | 12.30% | +7.6% |
| Annualized Volatility | 6.37% | 5.35% | −16.0% |
| Portfolio Sharpe | 1.09 | 1.46 | +33.9% |

## Risks
- **Opportunity cost of 61% cash** - intentional and consistent with Berkshire's "wait for fat pitches" philosophy, but cash earns ~4% vs the equity sleeve's ~10% historical return. It remains the single largest drag on blended portfolio returns.
- **AEP sector concentration** - thesis depends on sustained AI-driven electricity demand. Slower AI capex or regulatory shifts in energy markets could undermine the position.
- **NEM gold volatility** - as a miner, NEM carries operational leverage and amplifies gold price moves in both directions.

## Repository Contents
| File | Description |
| `_FINAL__SQL_Code_Group_9.sql` | SQL data ingestion - daily pricing for AEP and NEM loaded into `pricing_daily_new` |
| 'SQL_Berkshire_Hathaway_Presentation.pdf` | Full slide deck with charts, correlation matrix, Sharpe rankings, and before/after comparison |
| `README.md` | This file |


'SQL_Berkshire_Hathaway_Presentation.pdf`: Client-facing portfolio optimization presentation delivered to Berkshire Hathaway. Covers current portfolio analysis, SQL-driven quantitative findings, and three targeted recommendations to improve risk-adjusted returns while preserving the fund's core investment philosophy.

