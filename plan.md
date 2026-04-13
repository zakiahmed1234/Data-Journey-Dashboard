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
- Data is stored in global table partitioned by lender and ingestion date.
- This enables partition pruning for fast per-lender time-range queries.

**Visual Components:**
- **Central Lake Table:** Columns include `Lender & Date`, `Entity ID`, `Category`, `Amount`, and `Location`. Rows for `Lender_A1B` are highlighted to show they are co-mingled with other lenders.

---

## Stage 3: The Efficiency Gap
**Title:** `3. The Efficiency Gap`
**Layout:** 3-panel horizontal grid comparing scanning techniques.

**Exact Panel Text (Info Panel):**
- Lender A1B's metrics are computed by scanning partitioned S3 loan data for relevant lender.
- We use a hive partition to increase speed and reduce cost (often ~90% reduction in I/O)

**Visual Components:**
- **Panel 2 (Traditional DB: Full Scan):** A scrolling list of 150+ rows where a "scanner" must pass through every entry to find matches.
- **Panel 3 (S3 Hive Partition Pruning):** A visualization of a file directory path (`s3://zakis-parquet-storage/loans/`) where only the relevant folders are accessed while others are dimmed out.

---

## Stage 4: Forensic Reconstruction
**Title:** `4. Forensic Reconstruction`
**Subtitle:** `Data pulled from Hive partitions generates the final Audit Trail in real-time.`
**Layout:** Scroll-synchronized dual-pill transition. LHS contains transitioning pills and a live-updating balance table. RHS contains a Plotly line chart.

**Exact Pill 1 Text (Initial Phase):**
- Outstanding balances evolve based upon interest accrual, writeoffs, late fees, etc.
- Each month's balance is dependent on previous month, following a recursive update rule
- DuckDB is used for complex recursive calculations, leveraging fast in-memory, iterative execution

**Exact Pill 2 Text (Batch/Matrix Phase):**
- Loan-level outstanding balances for today are computed for all loans within a single lender's portfolio, and are stored in a **"loan-state matrix"**, which represents the performance of the entire portfolio.
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
- Athena is used as a serverless SQL layer over the precomputed loan-state matrix in S3, enabling scalable cohort and risk analytics without managing a database cluster.
- This cleanly separates heavy per-loan computation (DuckDB) from large-scale aggregation queries, improving scalability and simplifying the overall architecture.

**Visual Components:**
- **Tree Nodes:** Root is `Loan State Matrix (S3)`. Leaf nodes include `Grade Vintage Curves`, `Regional Principal Mix`, `Sector Default Rates`, `Credit Grade Distribution`, and `Score Evolution`.
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

**Exact Metric Navigation Labels:**
- 📊 Grade Vintage Curves
- 🌍 Regional Principal Mix
- 💼 Sector Default Rates
- 🛡️ Credit Grade Distribution
- 📈 Score Evolution

**Visual Components:**
- **Time Slider:** Allows user to scrub through history (accessing min/max timestamps from `agg_meta.json`).
- **Stats Grid:** Displays `Loans` (Count), `Volume` ($M), and `Avg Rate` (%).
- **Active Chart Window:** Renders multi-trace Plotly graphs based on the selected metric and date cutoff.
