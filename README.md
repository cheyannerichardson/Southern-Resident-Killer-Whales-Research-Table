# Southern Resident Killer Whale Population & Chinook Salmon Analysis

**Author:** Cheyanne Richardson  
**Tools:** PopSQL (SQL Only)  
**Focus:** Data Modeling • Ecological Data Aggregation • SQL Schema Design

---

## Objective  

Create SQL database structure to store **53 years of SRKW census data** (1973-2025) for pods **J**, **K**, and **L**.  
Build aggregation queries to summarize **3 available years of Chinook salmon data** (2001, 2019, 2023) alongside whale census records for comparative analysis.

---

## Executive Summary    

**53 years of SRKW census data** (1973-2025) were standardized into a normalized SQL schema for pod-level trend analysis.  
**3 years of Chinook salmon data** (2001, 2019, 2023) reveal 90% salmon decline alongside relatively stable whale counts (57-61 individuals).  

This project demonstrates **SQL table design and aggregation** for SRKW census data, enabling pod-level population summaries across 53 years.

--

## Dataset Summary  

**Table:** `pod_census`  
**Total Records:** 159 rows (3 pods × 53 years)  

| Column | Type | Description |
|---------|------|-------------|
| pod_id | CHAR(1) | Pod identifier (J, K, L) |
| census_year | INT | Year of census (1973–2025) |
| individuals | INT | Number of individuals in pod |
| chinook_salmon | INT | Chinook salmon count (select years) |
| survey_date | DATE | Standardized date (YYYY‑07‑15) |
| source_org | VARCHAR(10) | Data source (CWR, BOTH) |

**Schema Highlights:**  
- Composite key `(pod_id, census_year)` enforces annual uniqueness  
- `STR_TO_DATE()` standardizes temporal fields  
- `COALESCE()` ensures NULL‑safe aggregations  

---

## Methodology  

1. **Data Structuring:** Created normalized schema with composite primary key `(pod_id, census_year)`.  
2. **Data Ingestion:** Inserted 159 annual whale census records for pods J, K, L (1973–2025).  
3. **Quality Control:** Used `INSERT IGNORE` to prevent duplicate records.  
4. **Data Enrichment:** Added Chinook salmon counts for 3 available years (2001, 2019, 2023).  
5. **Analysis:** Built aggregation queries to summarize population and salmon data across overlapping years.

---

## Core SQL Queries  

### 1. Key Year Summary  
    Aggregates SRKW and Chinook data for overlapping years.  

      '''sql
    SELECT
        census_year,
        ROUND(SUM(individuals)/3, 1) AS total_pods,
        COALESCE(AVG(chinook_salmon)/1000, 0) AS chinook_thousands
        FROM pod_census
        WHERE census_year IN (2001, 2019, 2023)
        GROUP BY census_year
        ORDER BY census_year;

### 2. Chinook Averages by Year  
Calculates mean salmon abundance in recorded years.  

### 3. Pod‑Specific Trends  
Tracks whale counts per pod annually for long‑term trend analysis.  

---

## Analytical Impact & Key Metrics  

SQL served as both data storage and analytical platform, streamlining analysis and output generation.

Key Metrics:

    Dataset Scope: 159 records covering 3 SRKW pods (J, K, L) across 1973–2025
    Chinook Data Points: 3 years available (2001: 45,273 | 2019: 4,183 | 2023: 8,465)
    SRKW Population Range: 57.7–61.7 individuals across overlapping Chinook data years
    Trend Observation: Both whale counts and salmon abundance show decline patterns in available data  

Key Insights / Actions:

    Real-world data constraint: Chinook salmon data available only for 2001, 2019, 2023   
    Despite 90% salmon decline (45k → 4k), SRKW populations remained stable (57-61 individuals), 
    suggesting prey resilience or behavioral adaptation  
    Normalized SQL schema enables scalable data ingestion for future conservation monitoring
    Actionable Next Steps: Extend schema to include **vessel noise levels** and **PCB toxin concentrations** 
    — both scientifically documented threats to SRKW alongside prey scarcity — enabling multi-factor analysis of population stressors
   

---

## Data Pipeline  


1. **Create Table** → Define normalized schema with composite primary key (`pod_id`, `census_year`)  
2. **Insert Records** → Load 159 SRKW census records (53 years × 3 pods)  
3. **Populate Salmon Counts** → UPDATE 3 years (2001, 2019, 2023) with NOAA Chinook data  
4. **Data Validation** → `INSERT IGNORE` prevents duplicates, `COALESCE()` handles NULL salmon values  
5. **Query Execution** → Run aggregation queries to summarize population and salmon counts   

**Sample Trend Output (Population vs. Salmon):**  

| Year | Avg SRKW Count | Chinook (Thousands) |
|------|----------------|---------------------|
| 2001 | 60.0 | 45.3 |
| 2019 | 57.7 | 4.2 |
| 2023 | 61.7 | 8.5 |

---

## Data Limitations & Assumptions  

- Chinook salmon data available only for three sample years (2001, 2019, 2023).  
- Assumes consistent census methodology and equal effort across pods.  
- SRKW population calculated as simple average across 3 pods (may mask pod-specific variation)
---

## Next Steps  

- Incorporate annual Chinook counts for full historical coverage.  
- Execute formal correlation test using `CORR(individuals, chinook_salmon)` in MySQL 8+.  
- Expand dataset with vessel‑traffic and toxin concentration variables.  
- Automate ingestion for ongoing annual updates.  

---

## Sources  

- **Southern Resident Killer Whale Census:** Center for Whale Research (CWR)  
- **Chinook Salmon Data:** NOAA Fisheries (select years)
- **Data Last Updated:** February 2026   
- **Data Sources Verified:** February 2026 

**License:** MIT — Free for research, conservation, and educational use.  

---

## Skills Demonstrated  

**SQL Query Design** • **Data Modeling** • **Data Aggregation** • **Data Validation** • **Ecological Data Structuring**
