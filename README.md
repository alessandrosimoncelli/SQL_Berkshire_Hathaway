# SQL Berkshire Hathaway - portfolio optimization analysis
**Hult International Business School 
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
Stores accounts linked to each client. `account_id` is the primary key. The column `customer_id` references `customer_details.customer_id` logically but is not enforced as a foreign key constraint in the DDL — it is noted in a comment only.

**holdings_dim**
Stores each position held per account. The primary key is `account_id_ticker`, a composite string (e.g. `101_AAPL`) that uniquely identifies one holding within one account. The columns `account_id` and `ticker` reference other tables logically but again are not enforced as foreign key constraints in the DDL.

**pricing_daily_new**
The main data table. Stores daily OHLCV price records for every ticker. The primary key is `ticker_date_type`, a composite string (e.g. `AAPL_2023-06-12_Adj Close`) that uniquely identifies one price type for one ticker on one date. This table contains roughly three years of daily data across all holdings, generated via Python and yfinance and loaded through bulk INSERT statements.

**Note on foreign keys:** the relationships between tables (customer_id in accounts_dim, account_id and ticker in holdings_dim) are logical references only. No `FOREIGN KEY ... REFERENCES` constraints were declared in the DDL. The schema enforces uniqueness through primary keys but does not enforce referential integrity at the database level.

### Schema diagram

```
customer_details         accounts_dim            holdings_dim
----------------         ------------            ------------
customer_id (PK)  <--   customer_id             account_id_ticker (PK)
full_name                account_id (PK)   <--  account_id
first_name               main_account            ticker  ---------->  security_masterlist
last_name                acct_open_status        value                 ---------------
email                    acct_open_date          quantity              ticker (PK)
location                                         date                  security_name
                                                                       sec_type
                                                                       major_asset_class
                                                                       minor_asset_class

pricing_daily_new
-----------------
ticker_date_type (PK)
date
ticker
price_type
price
```

Arrows indicate logical foreign key references. None are enforced as constraints in the DDL.

---

## Analysis

All financial metrics were computed manually in SQL by querying `pricing_daily_new` directly. This includes annualized return, annualized volatility, Sharpe ratio (using BIL's yield as the risk-free rate proxy), and pairwise correlation across holdings.

In a real-world scenario, this kind of statistical work would normally be done in Python or R after extracting the data from SQL. SQL is a storage and retrieval language and is not built for statistical computation. The metrics here are achievable with SQL aggregates but the approach is not scalable or flexible for more complex analysis.

---

## Portfolio context

The analysis covers a normalized subset of Berkshire's portfolio as of March 31, 2026: the six largest equity holdings (AAPL, AXP, KO, BAC, GOOGL, CVX) plus a T-Bill sleeve proxied by BIL. Minor positions were excluded and weights were reallocated. The 61% cash weight applies to this subset, not the full Berkshire portfolio.

Data sources: Berkshire Hathaway 10-Q (Q1 2026), CNBC Portfolio Tracker, yfinance.

---

## Key findings

| Metric | Before | After | Change |
|---|---|---|---|
| Annualized return | 11.43% | 12.30% | +7.6% |
| Annualized volatility | 6.37% | 5.35% | -16.0% |
| Portfolio Sharpe | 1.09 | 1.46 | +33.9% |

Three moves, all within Berkshire's investment philosophy: trim AAPL (-6%), trim AXP (-6%), add AEP (+6%) and NEM (+6%). The 61% cash position is not touched.

Full rationale, correlation analysis, and slide-by-slide narrative are in the presentation PDF.

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
