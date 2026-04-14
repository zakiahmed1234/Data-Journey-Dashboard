# Data Journey | Interactive Storyboard & Technical Plan

This document provides a literal extraction of the narrative, titles, and technical layouts used in the Data Journey dashboard.

---

## Stage 1: Edge Ingestion
**Title:** `1. Edge Ingestion`
**Layout:** 2-column grid. Left-hand side (LHS) features a styled description pill. Right-hand side (RHS) displays stacked mini-tables simulating raw incoming data.

**Exact Pill Text:**
- Lender **A1B** submits structured daily loan events (payments, balances, write-offs) via a validated API
- Data is validated and queued for ingestion into the S3 data lake for downstream processing

**Visual Components:**
- **Mini-Table 1:** `Daily Loans (2026-04-12)` showing Loan ID, Amount, and City.
- **Mini-Table 2:** `Daily Payments (2026-04-12)` showing Pay ID, Amount, and Date Paid.

---

## Stage 2: The Multi-Tenant Lake
**Title:** `2. The Multi-Tenant Lake`
**Layout:** 2-column grid. LHS contains a green-themed description pill. RHS features a wide "Lake Table" showing how data from multiple lenders is interleaved.

**Exact Pill Text:**
- Data is logically grouped by lender, but physically stored as distributed Parquet files in S3.
- This enables partition pruning, so only relevant data partitions are read for a given lender or time range.

**Visual Components:**
- **Central Lake Table:** Columns include `Lender & Date`, `Entity ID`, `Category`, `Amount`, and `Location`. Rows for `Lender_A1B` are highlighted to show they are co-mingled with other lenders.

---

## Stage 4: Forensic Reconstruction
**Title:** `4. Forensic Reconstruction`
**Subtitle:** `Data pulled from Hive partitions generates the final Audit Trail in real-time.`
**Layout:** Scroll-synchronized dual-pill transition. LHS contains transitioning pills and a live-updating balance table. RHS contains a Plotly line chart.

**Exact Pill 1 Text (Initial Phase):**
- Outstanding balances evolve based upon interest accrual, writeoffs, late fees, etc.
- Calculating monthly change in balance requires complex recursive computations based on previous months.
- We leverage DuckDB's fast in-memory and iterative execution to provide speedy and reliable calculations

**Exact Pill 2 Text (Batch/Matrix Phase):**
- Each computed loan-state is materialised into a **"loan-state matrix"**, representing a full snapshot of portfolio performance at a given point in time.
- This enables real-time analysis of delinquency behaviour, including late payments, DPD trajectories, and risk assessments

**Visual Components:**
- **Amortization Chart:** Draws "Ideal" vs "Actual" paths. Includes interactive labels for `PARTIAL PAYMENT`, `MISSING PAYMENT`, `WRITEOFF`, and `CATCH-UP PAYMENTS`.
- **Batch Table:** Shows Loan IDs (e.g., `L-FORENSIC-001`) and their calculated balances updating in real-time.

---

## Stage 5: Serverless Analytics Layer
**Title:** `5. Serverless Analytics Layer`
**Subtitle:** `Athena enables scalable aggregation without database cluster management.`
**Layout:** A large interactive SVG Tree Map showing data flow from a root node to distributed analytics outputs.

**Exact Pill Text:**
- Athena is used as a serverless SQL layer over the precomputed loan-state table in S3, enabling scalable cohort and risk analytics without managing a database cluster.
- This cleanly separates heavy per-loan computation (DuckDB) from large-scale aggregation queries, improving scalability and simplifying the overall architecture.

**Visual Components:**
- **Tree Nodes:** Root is `Loan State Table (S3)`. Leaf nodes include `Grade Vintage Curves`, `Regional Principal Mix`, `Sector Default Rates`, `Credit Grade Distribution`, and `Score Evolution`.
- **Annotations:** Explicit labels for `DuckDB (local engine)` and `Distributed Athena Queries (serverless SQL)`.

---

## Stage 6: Portfolio Evolution
**Title:** `6. Portfolio Evolution`
**Subtitle:** `Historical Portfolio Cutoff` (with Date Display)
**Layout:** Full-screen analytics cockpit with a time-scrubbing slider and navigation for different charts.

**Exact Institutional Notes (Bullet Points):**
- We output real time dashboards that visualise Athena-aggregated cohort and risk metrics.
- Supports portfolio monitoring, including delinquency tracking, cohort performance, and risk segmentation.
- Used by 10+ commercial banking clients supporting portfolios of up to 100k+ loans.

**Metric Navigation & Descriptions:**
- **📊 Grade Vintage Curves:** Analyzes cumulative default trajectories normalized by Months on Books (MOB). This visualization benchmarks risk performance across credit tiers, demonstrating the sharp predictive divergence between prime (Grade A) and sub-prime (Grade D) cohorts.
- **🌍 Regional Principal Mix:** Visualizes geographic principal distribution using a 100% stacked area model. This highlights relative shifts in regional concentration and capital allocation trends across the global portfolio over time.
- **💼 Sector Default Rates:** Benchmarks sector-specific risk by calculating the default rate per loan use category. This identifies volatility clusters and industry-specific performance markers within the current active portfolio.
- **🛡️ Credit Grade Distribution:** Displays the static credit quality mix of the portfolio. This proportional breakdown provides an institutional-view of capital distribution across the risk spectrum up to the selected cutoff date.
- **📈 Score Evolution:** Tracks the historical correlation between external Credit Bureau indicators and proprietary Internal risk scores. This dual-axis time-series is critical for validating internal model efficacy against standardized market benchmarks.

**Visual Components:**
- **Time Slider:** Allows user to scrub through history (accessing min/max timestamps from `agg_meta.json`).
- **Stats Grid:** Displays `Loans` (Count), `Volume` ($M), and `Avg Rate` (%).
- **Active Chart Window:** Renders multi-trace Plotly graphs based on the selected metric and date cutoff.
