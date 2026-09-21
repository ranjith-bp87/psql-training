# Postgres Intro KT: `shop` Demo Database

A simple two-table e-commerce model (`customers` dimension and `orders` fact) along with pre-built BI views, designed for demonstrating PostgreSQL schema setup, indexing, bloat, and query performance.

## Prerequisites

- **Target Database:** PostgreSQL 15+ (validated on PG 16.15 / Tanzu Postgres 15.x).
- **Privileges:** Run as a login role with permission to create databases and schemas (e.g., owner or superuser).

---

## Setup Instructions

Follow these steps in order to set up and populate the database.

### 1. Create and Connect to the Database

Before running any SQL scripts, create the `shop` database and connect to it using `psql`:

```sql
CREATE DATABASE shop;
\c shop
```

### 2. Build Schema and Objects

Run `01_shop_schema.sql` against the `shop` database. This script sets up a dedicated `sales` schema, sets the `search_path`, builds the tables, and creates the BI views:

```bash
psql -d shop -f 01_shop_schema.sql
```

### 3. Populate Sample Data

Run `load_large.sql` to populate the dataset (~2,000 customers and ~50,000 orders):

```bash
psql -d shop -f load_large.sql
```

---

## Database Architecture

- **Schema:** `sales` (persisted on the `shop` database `search_path`)
- **Tables:**
  - `customers`: Dimension table storing customer identities, locations, and segments.
  - `orders`: Fact table storing transaction timestamps, amounts, channels, and statuses.
- **BI Views:**
  - `v_monthly_sales`: Aggregates monthly revenue metrics, excluding cancelled/returned orders.
  - `v_customer_ltv`: Computes customer lifetime value using a `LEFT JOIN`.

## Key Features

- **Reproducible:** Script uses `setseed()` for consistent pseudo-random data generation.
- **Idempotent:** Executes `TRUNCATE ... RESTART IDENTITY CASCADE` prior to loading, allowing clean re-runs.
- **Self-Verifying:** Prints summary row counts, date ranges, and status distribution upon execution completion.
