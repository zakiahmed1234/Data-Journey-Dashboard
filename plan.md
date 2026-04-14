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
- **Global Layout:** All five narrative stages are hosted within a single `story-container` with a unified `sticky-wrapper`. This ensures that transitions between stages are exclusively **fades**, rather than physical vertical scrolls.

---

## 0. Hero Entry
**Visuals:**
- Full-viewport (`100vh`) radial gradient background (`#1e293b` to `#0f172a`).
- Centralized typography: `h1` at `4rem` with `-2px` letter spacing.
- **Interaction:** "Enter" button (`enter-btn`) with `0.3s` transform transition and blue box-shadow.
- **Transition Logic:** Clicking "Enter" triggers a smooth programmatic scroll to the start of the story sequence.

**Precise Text Content:**
- **Title:** `Lending Analytics Platform`
- **Subtitle:** `End-to-end forensic auditing and real-time portfolio intelligence.`
- **Button:** `Enter`

---

## 1. Data Ingestion Layer
**Title:** `1. Data Ingestion Layer`
**UI Composition:**
- **Description Pill (LHS):** Styled with `background: rgba(59, 130, 246, 0.05)` and a `1px` border of the primary blue.
- **Data Preview (RHS):** Two stacked `mini-table` components simulating raw incoming data.

**Precise Pill Text (LHS):**
- Lender **A1B** submits structured daily loan events (payments, balances, write-offs) via a validated API
- Data is validated and queued for ingestion into the S3 data lake for downstream processing

**Precise Table Headers/Data (RHS):**
- **Table 1 Header:** `Daily Loans (2026-04-12)`
- **Table 2 Header:** `Daily Payments (2026-04-12)`

---

## 2. The Multi-Tenant Data Lake
**Title:** `2. The Multi-Tenant Data Lake`
**UI Composition:**
- **Description Pill (LHS):** Emerald-themed focusing on logical grouping and Parquet storage in S3.
- **Central Lake Table (RHS):** Interleaved data from multiple lenders (`Lender_A1B`, `LENDER_BETA`, etc.).

**Precise Pill Text (LHS):**
- Data is logically grouped by lender, but physically stored as distributed Parquet files in S3.
- This enables partition pruning, so only relevant data partitions are read for a given lender or time range.

---

## 3. Loan State Engine
**Title:** `3. Loan State Engine`
**Layout:** Dual-pill transition driven by scroll progress. 
- **LHS:** Narrative pills and live-updating balance table.
- **RHS:** Interactive line chart with historical forensic reconstruction.

**Precise Pill 1 Text (Initial Phase):**
- Outstanding balances evolve based upon interest accrual, writeoffs, late fees, etc.
- Calculating monthly change in balance requires complex recursive computations based on previous months.
- We leverage DuckDB's fast in-memory and iterative execution to provide speedy and reliable calculations

**Precise Pill 2 Text (Batch/Matrix Phase):**
- Each computed loan-state is materialised into a **"loan-state table"**, representing a full snapshot of portfolio performance at a given point in time.
- This enables real-time analysis of delinquency behaviour, including late payments, DPD trajectories, and risk assessments

**Precise Chart Legend (RHS):**
- **Dotted Line:** Total Due (incl. interest)
- **Orange Line:** Partial Payment
- **Red Line:** Missing Payment

---

## 4. Serverless Analytics Layer
**Title:** `4. Serverless Analytics Layer`
**Layout:** Animated SVG directed graph showing data distribution.

**Precise Pill Text (Top):**
- Athena is used as a serverless SQL layer over the precomputed loan-state table in S3, enabling scalable cohort and risk analytics without managing a database cluster.
- This cleanly separates heavy per-loan computation (DuckDB) from large-scale aggregation queries, improving scalability and simplifying the overall architecture.

**Precise SVG Node Content:**
- **Root Node:** `Loan State Table (S3)`
- **Leaf Nodes:** `Grade Vintage Curves`, `Regional Principal Mix`, `Sector Default Rates`, `Credit Grade Distribution`, `Score Evolution`.

---

## 5. Portfolio Evolution
**Title:** `5. Portfolio Evolution`
**Layout:** Analytics cockpit with time-scrubbing slider and metric navigation.

**Precise Institutional Notes (LHS):**
- We output real time dashboards that visualise Athena-aggregated cohort and risk metrics.
- Supports portfolio monitoring, including delinquency tracking and cohort performance.
- Used by 10+ commercial banking clients supporting 100k+ loans.

**Precise Metric Definitions (Description Box - RHS):**
- **Grade Vintage Curves:** Analyzes cumulative default trajectories normalized by Months on Books (MOB).
- **Regional Principal Mix:** Visualizes geographic principal distribution using a 100% stacked area model.
- **Sector Default Rates:** Benchmarks sector-specific risk by calculating the default rate per loan use category. 
- **Credit Grade Distribution:** Displays the static credit quality mix of the portfolio. 
- **Score Evolution:** Tracks the historical correlation between external Credit Bureau indicators and proprietary Internal risk scores.

---

## Scroll & Transition Engine (Fade-Only)
- **The Global Track:** The entire dashboard lives in a `story-container` with a `scroll-track` of approx `1500vh`.
- **Sticky Execution:** A `sticky-wrapper` ensures that all content stays centered in the viewport while scrolling.
- **Opacity Toggle:** As the user scrolls through predefined ranges of the global progress (e.g., `0.0-0.2`, `0.2-0.4`), the corresponding stage's `active` class is toggled.
- **Transitions:** All stage transitions use `transition: opacity 0.8s ease-in-out`, creating a seamless cross-fade effect.
- **Intra-Stage Progress:** For stages with internal animations (Stage 3, 4, 5), the local scroll progress is recalculated based on the specific global range for that stage.
