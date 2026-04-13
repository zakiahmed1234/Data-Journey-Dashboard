# Data Journey Dashboard

An interactive, scroll-driven visualization platform for lending analytics. This dashboard demonstrates the end-to-end "data journey" from ingestion to forensic reconstruction and portfolio analysis.

## Quick Start

Due to browser security (CORS), you cannot simply open `index.html` in a browser. You must serve it from a local web server.

### Using Python (Recommended)
1. Open your terminal in this folder.
2. Run the following command:
   ```bash
   python3 -m http.server 8080
   ```
3. Open your browser and go to: `http://localhost:8080`

### Using Node.js
If you have `http-server` installed:
```bash
npx http-server . -p 8080
```

## Folder Structure

- `index.html`: The main dashboard containing all layout, CSS, and interactive logic.
- `agg_payload.csv`: Aggregated portfolio data for the evolution charts.
- `agg_meta.json`: Metadata (min/max timestamps) for the slider synchronization.
- `forensic_history.csv`: Loan-level forensic data for the amortization reconstruction.
- `ideal_amortization.csv`: Baseline data for comparing actual vs. ideal loan performance.

## Key Features

- **Edge Ingestion (Stage 1):** Visualizes raw API data entry.
- **Multi-Tenant Lake (Stage 2):** Shows partitioned S3 storage.
- **Efficiency Gap (Stage 3):** Animates Hive partition pruning vs. full table scans.
- **Forensic Reconstruction (Stage 4):** Real-time amortization charting using DuckDB-style logic.
- **Athena SQL Layer (Stage 5):** Interactive tree map of serverless analytics.
- **Portfolio Evolution (Stage 6):** Time-scrubbing dashboard for institutional risk analysis.
