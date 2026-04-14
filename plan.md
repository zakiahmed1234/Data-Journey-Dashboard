# Data Journey | Detailed Technical Specification & Content Ledger

This document provides an exhaustive technical and narrative breakdown of the Data Journey dashboard, detailing every UI component, transition logic, and analytical metric with 100% precise text content.

---

## Global Architectural Constants
- **Typography:** `Inter` (sans-serif) for general UI; `Fira Code` (monospace) for data paths and technical annotations.
- **Color Palette:**
  - `Primary`: `#3b82f6` (Blue)
  - `Background`: `#0f172a` (Deep Navy)
  - `Text`: `#f8fafc` (Off-white)
  - `Accent`: `#10b981` (Emerald Green)
  - `Alert/Warning`: `#ef4444` (Red) / `#f59e0b` (Amber)
- **Global Layout:** All content is wrapped in a `story-container` with a `scroll-track` of `600vh` to drive scroll-linked animations.

---

## 0. Hero Entry
**Visuals:**
- Full-viewport (`100vh`) radial gradient background (`#1e293b` to `#0f172a`).
- Centralized typography: `h1` at `4rem` with `-2px` letter spacing.
- **Interaction:** "Enter" button (`enter-btn`) with `0.3s` transform transition and blue box-shadow.
- **Transition Logic:** Clicking "Enter" triggers a smooth programmatic scroll to `10%` of the `story-container`'s scroll height, centering Stage 1.

**Precise Text Content:**
- **Title:** `Lending Analytics Platform`
- **Subtitle:** `End-to-end forensic auditing and real-time portfolio intelligence.`
- **Button:** `Enter`

---

## 1. Data Ingestion Layer
**Title:** `1. Data Ingestion Layer`
**UI Composition:**
- **Description Pill (LHS):** Styled with `background: rgba(59, 130, 246, 0.05)` and a `1px` border of the primary blue.
- **Data Preview (RHS):** Two stacked `mini-table` components within a `1.5rem` gap flex column.

**Precise Pill Text (LHS):**
- Lender **A1B** submits structured daily loan events (payments, balances, write-offs) via a validated API
- Data is validated and queued for ingestion into the S3 data lake for downstream processing

**Precise Table Headers/Data (RHS):**
- **Table 1 Header:** `Daily Loans (2026-04-12)`
- **Table 1 Columns:** `Loan ID`, `Amount`, `City`
- **Table 2 Header:** `Daily Payments (2026-04-12)`
- **Table 2 Columns:** `Pay ID`, `Amount`, `Date Paid`

---

## 2. The Multi-Tenant Data Lake
**Title:** `2. The Multi-Tenant Data Lake`
**UI Composition:**
- **Description Pill (LHS):** Emerald-themed (`rgba(16, 185, 129, 0.05)`) focusing on logical grouping and Parquet storage in S3.
- **Central Lake Table (RHS):** A wider implementation of the `lake-table`. 

**Precise Pill Text (LHS):**
- Data is logically grouped by lender, but physically stored as distributed Parquet files in S3.
- This enables partition pruning, so only relevant data partitions are read for a given lender or time range.

**Precise Table Headers (RHS):**
- **Columns:** `Lender & Date`, `Entity ID`, `Category`, `Amount`, `Location`

---

## 3. Loan State Engine
**Title:** `3. Loan State Engine`
**Layout:** A complex `250vh` scroll-track (`sync-scroll-track`) with a `sticky` container.

**Precise Pill 1 Text (Initial Phase - LHS):**
- Outstanding balances evolve based upon interest accrual, writeoffs, late fees, etc.
- Calculating monthly change in balance requires complex recursive computations based on previous months.
- We leverage DuckDB's fast in-memory and iterative execution to provide speedy and reliable calculations

**Precise Pill 2 Text (Batch/Matrix Phase - LHS):**
- Each computed loan-state is materialised into a **"loan-state table"**, representing a full snapshot of portfolio performance at a given point in time.
- This enables real-time analysis of delinquency behaviour, including late payments, DPD trajectories, and risk assessments

**Precise Chart Labels & Interaction (RHS):**
- **Annotation Labels:** `PARTIAL PAYMENT`, `MISSING PAYMENT`, `WRITEOFF`, `CATCH-UP PAYMENTS`
- **Hover Template:** `Status: [Status]`, `Balance: $[Value]`, `Paid: $[Value]`

---

## 4. Serverless Analytics Layer
**Title:** `4. Serverless Analytics Layer`
**Layout:** `350vh` scroll-track featuring a complex SVG directed graph.

**Precise Pill Text (Top):**
- Athena is used as a serverless SQL layer over the precomputed loan-state table in S3, enabling scalable cohort and risk analytics without managing a database cluster.
- This cleanly separates heavy per-loan computation (DuckDB) from large-scale aggregation queries, improving scalability and simplifying the overall architecture.

**Precise SVG Node Content:**
- **Root Node:** `Loan State Table (S3)`
- **Leaf Node 1:** `Grade Vintage Curves`
- **Leaf Node 2:** `Regional Principal Mix`
- **Leaf Node 3:** `Sector Default Rates`
- **Leaf Node 4:** `Credit Grade Distribution`
- **Leaf Node 5:** `Score Evolution`
- **Annotations:** `DuckDB (local engine)`, `Distributed Athena Queries (serverless SQL)`

---

## 5. Portfolio Evolution
**Title:** `5. Portfolio Evolution`
**Layout:** A comprehensive analytics cockpit.

**Precise Institutional Notes (LHS):**
- We output real time dashboards that visualise Athena-aggregated cohort and risk metrics.
- Supports portfolio monitoring, including delinquency tracking, cohort performance, and risk segmentation.
- Used by 10+ commercial banking clients supporting portfolios of up to 100k+ loans.

**Precise Stats Grid Labels (LHS):**
- `Loans`, `Volume`, `Avg Rate`

**Precise Metric Navigation Labels:**
- `📊 Grade Vintage Curves`, `🌍 Regional Principal Mix`, `💼 Sector Default Rates`, `🛡️ Credit Grade Distribution`, `📈 Score Evolution`

**Precise Metric Definitions (Description Box - RHS):**
- **Grade Vintage Curves:** Analyzes cumulative default trajectories normalized by Months on Books (MOB).
- **Regional Principal Mix:** Visualizes geographic principal distribution using a 100% stacked area model.
- **Sector Default Rates:** Benchmarks sector-specific risk by calculating the default rate per loan use category. 
- **Credit Grade Distribution:** Displays the static credit quality mix of the portfolio. 
- **Score Evolution:** Tracks the historical correlation between external Credit Bureau indicators and proprietary Internal risk scores.

---

## Scroll & Transition Engine
- **Progression Logic:** The `initStoryScroll` function maps the global `0.0` to `1.0` scroll progress to specific `activeIdx` values. 
  - `0.05 to 0.5`: Stage 1
  - `0.5 to 1.0`: Stage 2
- **Stage Visibility:** Stages are `position: absolute` within the `sticky-wrapper`. Only the `active` stage has `opacity: 1` and `pointer-events: auto`.
- **Easing:** All opacity transitions use a standardized `0.8s ease-in-out` to ensure a cinematic feel during manual scrolling.
- **Scroll Synchronization (Stage 3):** The `initSyncAnimation` maps `300vh` scroll height to a `33-step` animation sequence (16 forensic months + 17 batch matrix steps).
