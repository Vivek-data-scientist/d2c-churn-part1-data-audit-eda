# Data Quality Report

Snapshot date: **2025-09-30**. The customer universe is `customers.csv` with **2,400** unique customers.

## Dataset Inventory and Join Audit

| dataset | rows | columns | customer_ids | duplicate_rows | missing_cells | unknown_customer_ids | date_min | date_max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| customers | 2400 | 9 | 2400 | 0 | 1787 | 0 | 2024-01-01 | 2025-09-15 |
| orders | 10009 | 10 | 2400 | 0 | 80 | 0 | 2024-01-09 | 2025-11-29 |
| support_tickets | 1921 | 8 | 1247 | 0 | 0 | 0 | 2024-01-13 | 2025-09-30 |
| web_events_snapshot | 2400 | 10 | 2400 | 0 | 0 | 0 | 2025-09-30 | 2025-09-30 |
| churn_labels | 2400 | 4 | 2400 | 0 | 0 | 0 | 2025-09-30 | 2025-09-30 |
| intervention_history | 2400 | 5 | 2400 | 0 | 0 | 0 | 2025-09-30 | 2025-09-30 |
| rfm_modeling_snapshot | 2400 | 29 | 2400 | 0 | 1386 | 0 | 2025-09-30 | 2025-09-30 |

| dataset | rows | unique_customer_ids | customers_missing_from_file | unknown_customer_id_rows |
| --- | --- | --- | --- | --- |
| orders | 10009 | 2400 | 0 | 0 |
| support_tickets | 1921 | 1247 | 1153 | 0 |
| web_events_snapshot | 2400 | 2400 | 0 | 0 |
| churn_labels | 2400 | 2400 | 0 | 0 |
| intervention_history | 2400 | 2400 | 0 | 0 |
| rfm_modeling_snapshot | 2400 | 2400 | 0 | 0 |

All files join back to `customers.customer_id` without unknown customer IDs. Support tickets cover only 1,247 customers, which is expected sparse behavior rather than a join failure.

## Missing Values

| dataset | column | missing_count | missing_rate |
| --- | --- | --- | --- |
| customers | loyalty_tier | 1386 | 57.8% |
| rfm_modeling_snapshot | loyalty_tier | 1386 | 57.8% |
| customers | skin_type | 401 | 16.7% |
| orders | rating | 80 | 0.8% |

Treatment recommendations:

- Treat missing `loyalty_tier` as **Not enrolled**, not as a data error.
- Treat missing `skin_type` as **Missing / not provided** for profile EDA.
- Do not drop orders with missing `rating`; calculate average rating with non-null counts.
- `rfm_modeling_snapshot.loyalty_tier` inherits the same loyalty missingness as `customers.csv`.

## Duplicate and Duplicate-Like Records

- Exact duplicate rows: **0** across all loaded tables.
- `orders.csv` has **12** duplicate-like order IDs ending in `_DUP`. These should be investigated before financial reporting or order-level modeling.

## Invalid or Unusual Values

| dataset | column | invalid_values | invalid_count |
| --- | --- | --- | --- |
| orders | quantity_outside_1_4 |  | 0 |
| orders | discount_outside_0_70pct |  | 0 |
| orders | delivery_days_outside_1_11 |  | 0 |
| orders | returned_not_binary |  | 0 |
| orders | rating_outside_1_5_non_null |  | 0 |
| support_tickets | resolution_hours_negative |  | 0 |
| support_tickets | sentiment_outside_-1_1 |  | 0 |
| support_tickets | reopened_not_binary |  | 0 |
| churn_labels | target_not_binary |  | 0 |
| intervention_history | none_campaign_with_nonzero_cost |  | 404 |
| web_events_snapshot | negative_activity_counts |  | 0 |

One notable issue is `intervention_history`: **404** rows have `last_campaign_received = none` but non-zero campaign cost. This should be clarified before campaign ROI analysis.

## Outliers

`orders.gross_amount` 99th percentile is INR **2,308.62** and maximum is INR **24,789.38**.

| order_id | customer_id | order_date | category | quantity | gross_amount | discount_pct | returned | rating |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ORD006374 | CUST01868 | 2025-03-29 00:00:00 | Skin Care | 3 | 24,789.38 | 13.0% | 0 | 4.00 |
| ORD000701 | CUST00211 | 2024-11-27 00:00:00 | Fragrance | 2 | 22,719.45 | 25.0% | 0 | 5.00 |
| ORD007206 | CUST02106 | 2024-07-13 00:00:00 | Fragrance | 2 | 15,957.48 | 37.0% | 0 | 4.00 |
| ORD009649 | CUST01988 | 2025-10-25 00:00:00 | Fragrance | 1 | 12,312.12 | 4.0% | 0 | 5.00 |
| ORD004428 | CUST01295 | 2025-05-01 00:00:00 | Baby Care | 2 | 10,643.82 | 4.0% | 0 | 4.00 |
| ORD004650 | CUST01360 | 2024-10-09 00:00:00 | Fragrance | 2 | 8,777.20 | 47.0% | 0 | 5.00 |
| ORD005399 | CUST01584 | 2024-12-31 00:00:00 | Fragrance | 1 | 8,022.50 | 17.0% | 0 | 5.00 |
| ORD007765 | CUST02287 | 2025-06-22 00:00:00 | Fragrance | 4 | 3,746.76 | 8.0% | 0 | 5.00 |
| ORD000500 | CUST00159 | 2024-06-13 00:00:00 | Fragrance | 4 | 3,376.32 | 17.0% | 0 | 4.00 |
| ORD001120 | CUST00324 | 2024-12-30 00:00:00 | Fragrance | 4 | 3,341.27 | 16.0% | 0 | 5.00 |

Recommendation: use medians/quantiles for EDA and consider winsorization or robust scaling for modeling.

## Date Consistency

- Customer signup dates range from 2024-01-01 to 2025-09-15.
- Order dates range from 2024-01-09 to 2025-11-29.
- Support ticket dates range from 2024-01-13 to 2025-09-30.
- `orders.csv` contains **1,872** post-snapshot rows after 2025-09-30. These rows explain the churn label window and must not be used as model inputs.

## Leakage Watchlist

- Exclude post-snapshot orders from any features.
- Exclude `churn_next_60d` from inputs.
- Exclude `split` from model features.
- Use campaign history carefully: it is pre-snapshot but observational, so it does not prove campaign effectiveness.
