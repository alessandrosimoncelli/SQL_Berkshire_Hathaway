# SQL Berkshire Hathaway - portfolio optimization analysis
**Hult International Business School | Group Project**

---

## Purpose

This project was built as part of a data management course at Hult International Business School. The primary objective was not portfolio optimization. It was to practice building a relational database from scratch using MySQL, the same kind of infrastructure a real financial firm would use to store and query client and market data.

A spreadsheet can store data, but it breaks under volume, has no data integrity controls, cannot handle concurrent users, and offers no real query language. A relational database solves all of those problems. Building this database from schema design through data ingestion to querying is the core skill this project was designed to develop.

SQL concepts covered:

- Database and schema creation (DDL)
- Table design with primary keys and data types
- Logical foreign key relationships between tables
- Bulk data ingestion via INSERT statements
- SELECT queries with aggregates, filtering, and subqueries to extract financial metrics

---

## Database structure

The database is named `Hult_Capital_9` and runs on a local MySQL server (127.0.0.1, port 3306), managed via MySQL Workbench. It contains four tables.

**customer_details**
Stores client records. Each client has a unique `customer_id` (primary key), along with name, email, and location fields. Berkshire Hathaway is loaded as customer_id 1.

**accounts_dim**
Stores accounts linked to each client. `account_id` is the primary key. The column `customer_id` references `customer_details.customer_id` logically but is not enforced as a foreign key constraint in the DDL.

**holdings_dim**
Stores each position held per account. The primary key is `account_id_ticker`, a composite string (e.g. `101_AAPL`) that uniquely identifies one holding within one account. The columns `account_id` and `ticker` reference other tables logically but are not enforced as foreign key constraints in the DDL.

**security_masterlist**
Stores reference data for each security: name, ticker (primary key), asset class, and security type.

**pricing_daily_new**
The main data table. Stores daily OHLCV price records for every ticker. The primary key is `ticker_date_type`, a composite string (e.g. `AAPL_2023-06-12_Adj Close`) that uniquely identifies one price type for one ticker on one date. This table contains roughly three years of daily data across all holdings, generated via Python and yfinance and loaded through bulk INSERT statements.

**Note on foreign keys:** the relationships between tables (customer_id in accounts_dim, account_id and ticker in holdings_dim) are logical references only. No `FOREIGN KEY ... REFERENCES` constraints were declared in the DDL. The schema enforces uniqueness through primary keys but does not enforce referential integrity at the database level. For a project of this size with a single user and controlled data ingestion, this was a conscious simplification — in a production environment with multiple users or automated data pipelines, enforced foreign keys would be necessary to guarantee data consistency.

---

## Analysis

All financial metrics were computed manually in SQL by querying `pricing_daily_new` directly. This includes annualized return, annualized volatility, Sharpe ratio (using BIL's yield as the risk-free rate proxy), and pairwise correlation across holdings.

In a real-world scenario, this kind of statistical work would normally be done in Python or R after extracting the data from SQL. SQL is a storage and retrieval language and is not built for statistical computation. The metrics here are achievable with SQL aggregates but the approach is not scalable or flexible for more complex analysis.

---

## Portfolio context

The analysis covers a normalized subset of Berkshire's portfolio as of March 31, 2026: the six largest equity holdings (AAPL, AXP, KO, BAC, GOOGL, CVX) plus a T-Bill sleeve proxied by BIL. Minor positions were excluded and weights were reallocated. The 61% cash weight applies to this subset, not the full Berkshire portfolio.

Data sources: Berkshire Hathaway 10-Q (Q1 2026), CNBC Portfolio Tracker, yfinance.

---

## Berkshire's investment philosophy

Every recommendation in this analysis was evaluated against Berkshire's own investment filter before being proposed. Understanding what Berkshire values and what it avoids is the foundation of the whole approach.

What Berkshire values: durable economic moats, predictable and growing earnings, high quality management, simple and understandable business models, long term buy and hold, and buying at a fair price rather than necessarily a cheap one.

What Berkshire avoids: assets that generate no cash flows, speculative and early stage companies, high leverage and excessive debt, commoditized low margin businesses, crypto and complex derivatives, and micro and small caps.

One point worth highlighting: Berkshire holds zero gold, and that is entirely consistent with this philosophy. Gold produces nothing, pays nothing, and generates no cash flows. This becomes directly relevant when we get to the NEM recommendation below.

---

## Recommendations and justifications

### Why trim AAPL

**Quantitative:** Apple is the weakest equity in the portfolio on a risk-adjusted basis with a Sharpe ratio of 0.58. It is also 41% correlated with GOOGL, meaning a significant portion of the risk it carries is already covered by another holding that delivers more than double the risk-adjusted return at 1.17. Holding both at full size adds concentration without adding diversification.

**Qualitative:** Apple's valuation has become stretched, trading at around 30x forward earnings. Berkshire itself has been reducing its Apple position over recent quarters, signaling that even from Buffett's perspective the risk/reward at current prices is less compelling than it once was. The business remains high quality, which is why it is trimmed rather than exited entirely, but leaning on GOOGL as the primary tech exposure is the more efficient choice given the data.

---

### Why trim AXP

**Quantitative:** AXP and BAC move together with a 0.68 correlation, making them the most redundant pair in the portfolio. Holding both is effectively doubling one financial sector bet without receiving any diversification benefit in return. Between the two, BAC has the stronger Sharpe ratio (0.89 vs AXP's 0.74), so retaining BAC and trimming AXP is the straightforward choice.

**Qualitative:** Both are high quality financial businesses that fit Berkshire's philosophy. The trim is not a view on AXP as a company. It is purely a portfolio construction decision: when two positions move almost in lockstep, one of them is redundant, and the weaker risk-adjusted performer is the one to reduce.

---

### Why add AEP

**Quantitative:** AEP has a Sharpe ratio of 0.79, which beats most current holdings in the portfolio. It is also decorrelated from every existing position, meaning it adds genuine diversification rather than duplicating existing risk.

**Qualitative:** AI infrastructure has been the dominant investment theme in markets, but semiconductor and chip names have become far too expensive for Berkshire's value approach. That door is closed. AEP offers a second-order play on the same theme: data centers require enormous and growing amounts of electricity, and AEP is a regulated utility positioned to supply it. It is a simple, understandable business with predictable cash flows and a defensible market position — exactly the kind of company Berkshire has always favored. It captures the AI infrastructure trend at a price that actually fits the discipline.

---

### Why add NEM

**Quantitative:** NEM has a Sharpe ratio of 0.84, among the strongest in the optimized portfolio. Like AEP, it moves independently from every other holding, adding a form of diversification the portfolio currently lacks entirely.

**Qualitative:** Berkshire holds zero gold because gold generates no cash flows. NEM is different. As a gold miner, it has real revenues, real operating earnings, and a real P&L. It behaves like a gold hedge in terms of its price behavior and its low correlation with equities, but it passes Berkshire's cash flow filter because it is a business, not a commodity. It introduces exposure to a macro hedge that the portfolio has never had, without violating the core philosophy that every holding must produce something.

---

## Results

| Metric | Before | After | Change |
|---|---|---|---|
| Annualized return | 11.43% | 12.30% | +7.6% |
| Annualized volatility | 6.37% | 5.35% | -16.0% |
| Portfolio Sharpe | 1.09 | 1.46 | +33.9% |

Three moves, all within Berkshire's investment philosophy. The 61% cash position is not touched.

---

## Risks

**Opportunity cost of holding 61% in cash**
Keeping BIL at 61% is a deliberate and active choice, consistent with Berkshire's philosophy of waiting for the right moment. But it is not free. Cash earns roughly 4% while the equity sleeve historically returns closer to 10%. It remains the single biggest drag on the portfolio's blended return. The position is not being recommended for deployment here, but it is worth naming the trade-off explicitly.

**AEP sector concentration**
The investment case depends on AI-driven electricity demand continuing to grow. A slowdown in AI infrastructure spending or regulatory changes in energy markets could hurt the position.

**NEM gold volatility**
NEM is a miner, not physical gold. It carries operational leverage, meaning it amplifies gold price moves in both directions. If gold sells off, NEM tends to fall harder than the underlying metal.

---

## Files in this repository

| File | Description |
|---|---|
| `[FINAL]SQL_CODE_Berkshire.sql` | Full database script: schema creation, all four tables, data ingestion |
| `SQL Berkshire Hathaway Presentation.pdf` | Client-facing slide deck with charts, Sharpe rankings, correlation matrix, and before/after comparison |
| `README.md` | This file |

---

## Team

Group 9 - Hult International Business School, 2026
