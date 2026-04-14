# Data Journey | Master Design Document & Verbatim Content Ledger

This document serves as the absolute source of truth for the Data Journey dashboard. It describes every visual component, technical implementation detail, and contains the exact verbatim text used throughout the application.

---

## 1. Global Design System & Core Architecture

### Visual Identity
- **Typography:** 
  - `Inter`: Primary sans-serif for headers, UI, and body text.
  - `Fira Code`: Monospace for technical labels, data paths, and annotations.
- **Color Palette:**
  - `Primary (Blue)`: `#3b82f6` - Used for active states, dots, and highlights.
  - `Background (Deep Navy)`: `#0f172a` - Primary background.
  - `Section Background (Black)`: `#020617` - Contrast background for Stages 3 and 4.
  - `Text (Main)`: `#f8fafc` - High-contrast off-white.
  - `Text (Subtle)`: `#94a3b8` - Medium grey for subtitles and descriptions.
  - `Text (Muted)`: `#64748b` - Dimmed grey for inactive progress labels.
  - `Emerald`: `#10b981` - Used for Stage 2 highlights and success states.

### Layout & Interaction Model
- **Presentation Mode:** Unified `1000vh` scroll-track driving a sequence of 5 stages.
- **Sticky Stage Engine:** All stages are contained within a single `position: sticky` wrapper.
- **Fade Logic:** Stages are `position: absolute` and utilize `transition: opacity 0.8s ease-in-out`. Only the active stage has `opacity: 1`.
- **Progress Navigator:** A fixed vertical bar on the right side (`right: 2rem`) containing dots and labels for all sections.
  - **Inactive State:** Labels are `#64748b` (dimmed grey).
  - **Active State:** Label highlights in bold white; Dot scales to `1.8x` with a primary blue shadow.
  - **Context-Aware:** Hidden on the Hero page; fades in upon entering the story container.

---

## 2. Hero Section (The Entry Point)

**Layout:** Full-viewport (`100vh`) with a radial gradient background.
**Verbatim Text:**
- **Main Title:** `Lending Analytics Platform`
- **Description:** `Turn daily loan data into actionable precise dashboards.`
- **Call to Action:** `Enter` (Button)

**UI Elements:**
- **Social Icons:** Row of grey icons (`#94a3b8`) for LinkedIn, GitHub, and Email, transitioning to blue on hover.
  - **LinkedIn:** `https://www.linkedin.com/in/zaki-ahmed-14b907220/`
  - **GitHub:** `https://github.com/zakiahmed1234`
  - **Email:** `zaki.ahmed@aol.com`

---

## 3. Stage-by-Stage Content Ledger

### Stage 1: Data Ingestion Layer
- **Subtitle:** `Input daily data` (mid-grey, `#94a3b8`, positioned 0.5cm below title)
- **LHS Narrative Pill:**
  - `Lender A1B submits structured daily loan events (payments, balances, write-offs) via a validated API`
  - `Data is validated and queued for ingestion into the S3 data lake for downstream processing`
- **RHS Visuals:**
  - **Table 1:** `Daily Loans (2026-04-12)`
    - Columns: `Loan ID`, `Amount`, `City`
    - Rows: `L-9901 | $15,000 | London`, `L-9902 | $8,200 | Nairobi`
  - **Table 2:** `Daily Payments (2026-04-12)`
    - Columns: `Pay ID`, `Amount`, `Date Paid`
    - Rows: `P-551 | $1,248 | 2026-04-11`, `P-552 | $500 | 2026-04-10`

### Stage 2: The Multi-Tenant Data Lake
- **Subtitle:** `Secure, scalable data storage`
- **LHS Narrative Pill:**
  - `Data is logically grouped by lender, but physically stored as distributed Parquet files in S3.`
  - `This enables partition pruning, so only relevant data partitions are read for a given lender or time range.`
- **RHS Visuals:** 5-row wide table with the following headers:
  - `Lender & Date`, `Entity ID`, `Category`, `Amount`, `Location`
  - **Critical Rows:** Includes `LENDER_BETA (2026-04-10)` and `LENDER_056 (2025-08-15)` to demonstrate multi-tenancy.

### Stage 3: Loan State Engine
- **Subtitle:** `Calculating loan performance`
- **Interaction:** Synchronized 33-step animation (16 forensic months + 17 batch matrix months).
- **LHS Narrative Pills:**
  - **Pill 1 (Initial):** `Outstanding balances evolve based upon interest accrual, writeoffs, late fees, etc. Calculating monthly change in balance requires complex recursive computations based on previous months. We leverage DuckDB's fast in-memory and iterative execution to provide speedy and reliable calculations`
  - **Pill 2 (Transition):** `Each computed loan-state is materialised into a "loan-state table", representing a full snapshot of portfolio performance at a given point in time. This enables real-time analysis of delinquency behaviour, including late payments, DPD trajectories, and risk assessments`
- **RHS Chart Legend Items:**
  - `Total Due (incl. interest)`
  - `Partial Payment`
  - `Missing Payment`
- **Annotation Labels:** `PARTIAL PAYMENT`, `MISSING PAYMENT`, `WRITEOFF`, `CATCH-UP PAYMENTS`

### Stage 4: Serverless Analytics Layer
- **Subtitle:** `Computing statistical aggregations`
- **Narrative Pill:**
  - `Athena is used as a serverless SQL layer over the precomputed loan-state table in S3, enabling scalable cohort and risk analytics without managing a database cluster. This cleanly separates heavy per-loan computation (DuckDB) from large-scale aggregation queries, improving scalability and simplifying the overall architecture.`
- **Verbatim Node Labels (SVG):**
  - **Root:** `Loan State Table (S3)`
  - **Leaves:** `Grade Vintage Curves`, `Regional Principal Mix`, `Sector Default Rates`, `Credit Grade Distribution`, `Score Evolution`
  - **Meta Labels:** `DuckDB (local engine)`, `Distributed Athena Queries (serverless SQL)`

### Stage 5: Portfolio Evolution
- **Subtitle:** `Real-time metrics`
- **Institutional Notes (LHS):**
  - `We output real time dashboards that visualise Athena-aggregated cohort and risk metrics.`
  - `Supports portfolio monitoring, including delinquency tracking and cohort performance.`
  - `Used by 10+ commercial banking clients supporting 100k+ loans.`
- **Stats Grid Labels:** `Loans`, `Volume`, `DPD rate`
- **Navigation Buttons:** `📊 Grade Vintage Curves`, `🌍 Regional Principal Mix`, `💼 Sector Default Rates`, `🛡️ Credit Grade Distribution`, `📈 Score Evolution`
- **Metric Descriptions:**
  - `Analyzes cumulative default trajectories normalized by Months on Books (MOB).`
  - `Visualizes geographic principal distribution using a 100% stacked area model.`
  - `Benchmarks sector-specific risk by calculating the default rate per loan use category.`
  - `Displays the static credit quality mix of the portfolio.`
  - `Tracks the historical correlation between external Credit Bureau indicators and proprietary Internal risk scores.`

---

## 4. Technical Logic Specifications

### Scroll Mapping
The unified scroll track maps the global `1000vh` scroll height to stages as follows:
- **0.0 - 0.2:** Stage 1
- **0.2 - 0.4:** Stage 2
- **0.4 - 0.6:** Stage 3 (Sync Internal: 0-1)
- **0.6 - 0.8:** Stage 4 (Athena Internal: 0-1)
- **0.8 - 1.0:** Stage 5 (Aggregate Internal: 0-1)

### Slider Logic (Stage 5)
- **Min/Max Initialization:** Dynamically set using `aggMeta.access_min_ts` and `aggMeta.access_max_ts`.
- **Bidirectional Control:** The slider updates via scroll position AND manual user input (`oninput`).
- **Date Display:** Verbatim split-string formatting: `new Date(ts).toISOString().split('T')[0]`.
