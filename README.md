# 📦 Pakistan E-Commerce: Supply Chain Resilience Data Lake  
**Course:** DS 312 – Data Warehousing & Business Intelligence  
**Instructor:** Dr. Rabia Irfan | **Semester:** Spring 2026 | **Institution:** NUST (SEECS)  

---

## 📝 Project Overview  
This project examines how Pakistan’s macroeconomic volatility—particularly fuel price fluctuations and inflation—affects e-commerce logistics. A professional-grade **Medallion Data Lake** architecture has been implemented to ingest, clean, and analyze ~585k order records, enriched with global economic indicators.  

### Problem Statement  
Fuel price volatility and persistent inflation (12.6% in 2024) have contributed to delivery delays averaging 15% in Pakistan’s e-commerce sector. This pipeline quantifies supply chain resilience by correlating logistics performance with external economic factors.  

---

## 🛠️ Tech Stack  
- **Data Warehouse:** Google BigQuery  
- **Orchestration:** Apache Airflow  
- **Transformation:** dbt (Data Build Tool)  
- **Languages:** SQL & Python  
- **BI & Analytics:** Power BI & BigQuery ML (Hybrid Approach)  

---

## 📂 Data Sources & Integration  

| Dataset | Source | Format | Join Key |
|---------|--------|--------|----------|
| **Pakistan E-Commerce** | OpenData.com.pk | CSV (585k) | `order_date` |
| **Global Fuel Prices** | GlobalPetrolPrices | CSV (Monthly) | `Year-Month` |
| **World Bank Economics** | World Bank API | CSV (Yearly) | `Year` |

**Join Strategy:** Temporal alignment. Daily e-commerce orders are conformed to monthly/yearly grains in the Silver layer, enabling many-to-one mapping with macroeconomic indicators.  

---

## 📂 Project Structure  

```
├── src/ingestion/          # Python scripts for extracting CSVs into Cloud Storage
├── dbt_project/            # Core ELT transformations
│   ├── models/
│   │   ├── bronze/         # Raw landing tables with ingestion metadata
│   │   ├── silver/         # Cleaned, standardized, and quality-checked data
│   │   └── gold/           # Curated analytics-ready tables
├── data/raw_samples/       # Sample subsets for local testing
```

---

## 🏗️ Medallion Pipeline  

### 1. Bronze Zone (Raw & Immutable)  
- Ingests files from GCS into BigQuery landing tables.  
- Records tagged with `_ingested_at` and `_source_file`.  
- No transformations applied.  

### 2. Silver Zone (Cleaned & Conformed)  
- **Standardization:** Dates → ISO 8601; currency → numeric types.  
- **Deduplication:** Removes duplicate `order_id` entries.  
- **Quality Gates:** Flags invalid records (e.g., negative prices, future delivery dates).  
- **Quarantine:** Invalid records moved to `quarantine_rejects` with reason codes (`ERR_NULL_PRICE`, `ERR_FUTURE_DATE`).  

### 3. Gold Zone (Curated & Analytical)  
- Produces wide-fact table `fct_supply_chain_resilience`.  
- Aggregates metrics such as Delivery Delay Index (DDI) and Fuel Sensitivity.  

---

## 📈 Analysis Layer (Hybrid Approach)  
1. **BI Dashboard:** Power BI visualizes delivery delays across provinces against fuel price hikes.  
2. **ML Forecasting:** BigQuery ML predicts shipping cost fluctuations for the next quarter, driven by inflation trends.  

---

## 🚀 Setup & Installation  

1. **Clone the Repository:**  
   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```  

2. **Configure Environment Variables:**  
   Create a `.env` file (excluded via `.gitignore`):  
   ```text
   GCP_PROJECT_ID=your_project_id
   BIGQUERY_DATASET=ds312_dw
   ```  

3. **Run dbt Models:**  
   ```bash
   cd dbt_project
   dbt deps
   dbt build   # Executes models and quality tests
   ```  

---

## 🛡️ Security & Governance  
- `.gitignore` prevents accidental commits of sensitive files (`service_account_keys.json`, `.env`).  
- Branch protection on `main` enforces code quality via Pull Requests.  

