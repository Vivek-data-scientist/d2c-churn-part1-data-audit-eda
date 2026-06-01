# Part 1: Data Audit, EDA & Business Understanding

This standalone repository audits the D2C churn dataset, performs exploratory analysis, and converts the findings into churn-risk hypotheses and a business memo.

## How to Run

```bash
pip install -r requirements.txt
jupyter notebook eda_audit.ipynb
```

The raw CSV files are included under `data/raw/`. The notebook writes supporting tables and SVG charts under `outputs/`.

## Key Files

- `eda_audit.ipynb` - full data loading, schema audit, joins, EDA, and hypotheses
- `data_quality_report.md` - missing values, duplicate-like records, invalid values, outliers, join/date/leakage issues
- `business_memo.md` - concise business-facing memo grounded in dataset patterns
- `outputs/` - evidence tables and charts used in the analysis

## Leakage Note

`orders.csv` contains post-snapshot rows after 2025-09-30 for target construction. The EDA audits these rows, but customer-level behavioral features use only pre-snapshot orders.
