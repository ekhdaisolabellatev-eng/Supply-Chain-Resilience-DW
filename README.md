# 📦 Pakistan E-Commerce: Supply Chain Resilience Data Lake
**Course:** DS 312 - Data Warehousing & Business Intelligence  
**Instructor:** Dr. Rabia Irfan | **Semester:** Spring 2026 | **Institution:** NUST (SEECS)



## 📝 Project Overview
This project addresses the impact of Pakistan's macroeconomic volatility (fuel prices and inflation) on e-commerce logistics. We have implemented a professional-grade **Medallion Data Lake** architecture to ingest, clean, and analyze ~585k order records joined with global economic indicators.

### Problem Statement
With fuel price volatility and high inflation (12.6% in 2024), delivery delays in Pakistan's e-commerce sector have reached an average of 15%. This pipeline quantifies supply chain resilience by correlating logistics data with external economic factors.

---

## 🛠️ Tech Stack
* **Data Warehouse:** Google BigQuery
* **Orchestration:** Apache Airflow
* **Transformation:** dbt (Data Build Tool)
* **Language:** SQL & Python
* **BI & Analytics:** Power BI & BigQuery ML (Option C - Hybrid Approach)

---

## 📂 Data Sources & Multi-Source Integration
| Dataset | Source | Format | Join Key |
| :--- | :--- | :--- | :--- |
| **Pakistan E-Commerce** | OpenData.com.pk | CSV (585k) | `order_date` |
| **Global Fuel Prices** | GlobalPetrolPrices | CSV (Monthly) | `Year-Month` |
| **World Bank Economics** | World Bank API | CSV (Yearly) | `Year` |

> **Join Justification:** Datasets are joined on temporal keys. E-commerce orders (Daily) are conformed to Monthly/Yearly grains in the Silver layer to allow many-to-one mapping with economic indicators.

---

## 📂 Project Structure

├── src/ingestion/          # Extraction scripts (Python) to pull CSVs into the Cloud.
├── dbt_project/            # Main transformation logic (ELT) using SQL.
│   ├── models/
│   │   ├── bronze/         # Landing area: Raw data with ingestion metadata.
│   │   ├── silver/         # Transformation area: Cleaning & Quality Gates.
│   │   └── gold/           # Analytics area: Business-ready joined tables.
├── data/raw_samples/       # Small subsets of data for local testing (Sample only).

---

## 🏗️ The Medallion Pipeline

### 1. Bronze Zone (Raw & Immutable)
* **Process:** Files are ingested from GCS into BigQuery landing tables.
* **Traceability:** Every record includes `_ingested_at` (timestamp) and `_source_file` (filename).
* **Rule:** No transformations are applied at this stage.

### 2. Silver Zone (Cleaned & Conformed)
* **Standardization:** Date formats conformed to `ISO 8601`; currency converted to standard numeric types.
* **Deduplication:** Removal of duplicate `order_id` entries.
* **Quality Gates:** * Orders with negative prices or impossible delivery dates are flagged.
    * **Quarantine Mechanism:** Failed records are moved to `quarantine_rejects` with **Reason Codes** (e.g., `ERR_NULL_PRICE`, `ERR_FUTURE_DATE`).

### 3. Gold Zone (Curated & Analytical)
* **Output:** A wide-fact table `fct_supply_chain_resilience`.
* **Business Logic:** Aggregated metrics for Delivery Delay Index (DDI) and Fuel Sensitivity.

---

## 📈 Analysis Layer (Option C - Hybrid)
Building on the **Gold Dataset**, we provide:
1.  **BI Dashboard:** A Power BI report visualizing delay trends across Pakistan’s provinces vs. fuel price hikes.
2.  **ML Insight:** A **BigQuery ML** Time-Series forecast predicting shipping cost fluctuations for the next quarter based on inflation trends.

---

## 🚀 Setup & Installation
1.  **Clone the Repo:**
    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    ```
2.  **Environment Variables:** Create a `.env` file (do not push to Git!) with:
    ```text
    GCP_PROJECT_ID=your_project_id
    BIGQUERY_DATASET=ds312_dw
    ```
3.  **Run dbt:**
    ```bash
    cd dbt_project
    dbt deps
    dbt build # This runs models and quality tests
    ```

---

### 🛡️ Security Note
This repository uses `.gitignore` to prevent the accidental upload of `service_account_keys.json` or sensitive `.env` files. Branch protection is enabled on `main` to ensure code quality through Pull Requests.
