# Automated Data Quality Validation for Large-Scale AIS Vessel Telemetry

This repository contains the replication code and pipeline for automated data quality validation on large-scale Automatic Identification System (AIS) vessel telemetry. 

This work is part of a case study developed for the **CITE Expanded Abstract (Big Data Track)**.

## 📌 Project Overview

Processing large-scale maritime IoT data requires reliable data quality assurance before downstream analytical tasks. This project presents an end-to-end processing and validation pipeline using **DuckDB**, **Parquet**, and **Python**.

The dataset used is the **NOAA / BOEM MarineCadastre - Nationwide AIS 2023** (1-minute sampled, daily CSV files).

> **Note on Dataset Selection:** The 2023 raw dataset was intentionally selected over 2025 releases because 2025 files are pre-cleaned (sentinel values replaced by nulls, invalid MMSIs filtered out). The 2023 raw files preserve authentic AIS transmission anomalies, allowing accurate measurement of data quality issues.

---

## 🛠️ Architecture & Pipeline

```text
Raw CSV (NOAA Zip) ➔ Parquet (zstd) ➔ DuckDB Storage ➔ Quality Rule Assessment ➔ Summary Metrics & Visuals
```

1. **Extraction & Ingestion:** Automated downloading and unzipping of raw daily AIS datasets.
2. **Storage Optimization:** Conversion of raw CSVs to compressed `.parquet` files for optimized I/O operations.
3. **Database Integration:** Local querying and aggregation leveraging `DuckDB`.
4. **Validation Pipeline:** Execution of 11 record-level and trajectory-level validation checks.
5. **Reporting:** Generation of summary tables (`.csv`) and high-resolution figures (`300 DPI`).

---

## 📐 Data Quality Framework (Wang & Strong, 1996)

The implemented rules are framed around key Data Quality dimensions:

| Rule | Dimension | Check / Validation Description |
|---|---|---|
| **R1** | Validity | Coordinates out of valid ranges / sentinel values (`91`, `181`) / null island (`0, 0`). |
| **R2** | Validity | Malformed or non-vessel MMSI strings (must be 9-digit, starting with 2-7). |
| **R3** | Completeness | Speed Over Ground (SOG) missing or set to sentinel (`102.3`). |
| **R4** | Validity | SOG out of valid physical range (`< 0` or `> 102.3` knots). |
| **R5** | Completeness | Course Over Ground (COG) missing or set to sentinel (`360`). |
| **R6** | Validity | COG out of valid range (`< 0` or `> 360` degrees). |
| **R7** | Completeness | Heading missing or set to sentinel (`511`). |
| **R8** | Uniqueness | Duplicate records matching the same `(MMSI, timestamp)`. |
| **R9** | Timeliness | Timestamps missing or falling outside the source file's target date. |
| **R10**| Completeness | Trajectory gap exceeding 30 minutes between consecutive vessel points. |
| **R11**| Plausibility | Implied vessel speed between consecutive points > 50 knots (physically implausible). |

---

## 🚀 Getting Started

### Prerequisites

Ensure you have Python 3.9+ installed on your system.

### Installation & Environment Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME.git
   cd YOUR_REPOSITORY_NAME
   ```

2. **Create and activate a virtual environment (recommended):**
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies:**
   Create a `requirements.txt` file (or let the notebook install them) containing:
   ```text
   duckdb
   pandas
   numpy
   matplotlib
   requests
   tqdm
   tabulate
   ```
   Install via pip:
   ```bash
   pip install -r requirements.txt
   ```

---

## 💻 Running the Notebook

Open the Jupyter Notebook (`.ipynb`) in VS Code or Jupyter Lab:

```bash
jupyter lab
```

Select the kernel corresponding to your created virtual environment and execute the pipeline cells sequentially.

### Generated Artifacts
* Complete metrics including affected record counts, percentages, and execution runtimes per rule.
* Data quality issues chart across evaluated rules
* Distribution of reporting intervals between consecutive vessel transmissions

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.# ais-data-quality-validation
This repo contains information of a AIS public data study for data validation.
