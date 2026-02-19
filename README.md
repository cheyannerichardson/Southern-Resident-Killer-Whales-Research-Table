# Southern Resident Killer Whale Research

PopSQL project analyzing correlations between Chinook salmon populations and Southern Resident Killer Whale (SRKW) pod census data (J, K, L pods). Includes SQL queries, research tables, and documentation supporting analysis of salmon abundance impacts on SRKW population trends and conservation insights. [stackoverflow](https://stackoverflow.com/questions/48760362/how-to-create-tables-for-the-following-data-model)

## Table Schema

The `pod_census` table tracks annual census data:

```
pod_id        CHAR(1)     - Pod identifier (J, K, L)
census_year   INT         - Year of census (1973-2025)
individuals   INT         - Number of individuals in pod
chinook_salmon INT       - Chinook salmon count (select years)
survey_date   DATE        - Standardized survey date (YYYY-07-15)
source_org    VARCHAR(10) - Data source (CWR, BOTH)
PRIMARY KEY (pod_id, census_year)
```

## Dataset Overview

- **Time Period**: 1973–2025 (53 years)
- **Pods**: J (stable/growing), K (declining), L (peak 1993, declining)
- **Chinook Data**: Available for 2001 (45,273), 2019 (4,183), 2023 (8,465)
- **Total Records**: 159 rows across 3 pods

Key trends visible in raw data:
- SRKW population peaked ~1990s, declined since
- Chinook salmon shows sharp declines in recent decades

## Core Queries

### 1. Key Year Summary
Aggregates totals for years with Chinook data:
```sql
SELECT
  census_year,
  survey_date,
  ROUND(SUM(individuals)/3, 1) as "Total_Pods",
  COALESCE(AVG(chinook_salmon)/1000, 0) as "Chinook_Thousands"
FROM pod_census
WHERE census_year IN (2001, 2019, 2023)
GROUP BY census_year, survey_date
ORDER BY census_year;
```
**Sample Output**:
| census_year | Total_Pods | Chinook_Thousands |
|-------------|------------|------------------|
| 2001        | 60.0       | 45.3             |
| 2019        | 57.7       | 4.2              |
| 2023        | 61.7       | 8.5              |

### 2. Chinook Averages by Year
```sql
SELECT census_year, AVG(chinook_salmon) as Chinook_Count
FROM pod_census 
WHERE census_year IN (2001, 2019, 2023)
GROUP BY census_year;
```

### 3. Pod-Specific Trends
```sql
SELECT census_year, pod_id, individuals
FROM pod_census 
ORDER BY pod_id, census_year;
```

## Data Pipeline

1. **Create Table** → Composite primary key on `(pod_id, census_year)`
2. **Insert Data** → 1973–2025 census counts for J/K/L pods
3. **Populate Chinook** → Update select years with salmon abundance
4. **Add Metadata** → `survey_date` (July 15 standard), `source_org` (CWR/BOTH)
5. **Analysis Queries** → Aggregates, correlations, pod totals

**Notable Operations**:
- `INSERT IGNORE` prevents duplicates
- `STR_TO_DATE` standardizes dates
- `COALESCE` handles NULL salmon values
- Repeated `UPDATE` statements for data verification

## Usage in PopSQL

1. Copy the full SQL script (`pod_census_setup.sql`) into PopSQL
2. Execute to recreate table and data
3. Run analysis queries for correlations
4. Export results for visualization (Tableau, R, Python)

## Next Steps

- Add correlation analysis: `CORR(individuals, chinook_salmon)`
- Expand Chinook dataset for all years
- Integrate vessel traffic, toxin levels
- Build time-series visualizations

## Sources

- SRKW census: Center for Whale Research (CWR)
- Chinook salmon: NOAA Fisheries (select years) [stackoverflow](https://stackoverflow.com/questions/48760362/how-to-create-tables-for-the-following-data-model)
- Data current as of February 2026

```
MIT License - Free for research, conservation, educational use.
```
