# Business Memo: Pre-Campaign Investigation Priorities

## Executive Summary

The dataset contains **2,400** customers and an overall 60-day churn rate of **47.0%**. The strongest evidence points to three different retention problems: purchase dormancy, recent digital disengagement, and service/product friction. These should not receive the same campaign treatment.

## Evidence-Backed Churn Hypotheses

| hypothesis | evidence | business_action |
| --- | --- | --- |
| Stale order recency is the clearest churn warning sign. | Customers in the 181+ day recency band churn at 91.4%, while customers in the 0-30 day band churn at 11.7%. | Use replenishment/win-back journeys before sending expensive discount offers. |
| Recent web/app inactivity is a strong disengagement signal. | The 31+ days-since-visit band churns at 88.4%, versus 16.1% for customers seen within 0-3 days. | Trigger low-cost reactivation messages before paid incentives. |
| Support tickets need issue-level triage rather than one generic offer. | One-ticket customers churn at 50.3%; the highest ticket-level churn rate is 44.8% for `product_reaction`. | Review complaint type and sentiment before choosing service recovery versus marketing outreach. |
| Return-heavy customers need product or fulfillment investigation. | Customers with 50%+ return rate churn at 75.0%. | Audit product fit, delivery, and quality issues before offering discounts. |
| Campaign history reflects risk state and needs causal testing. | The highest campaign-history churn rate is 51.0% for `new_launch`. | Use holdouts; do not infer campaign lift from observational assignment. |
| Loyalty status is associated with different churn levels. | The highest loyalty-status churn rate is 48.8% for `Silver`. | Separate loyalty recognition from acquisition/reactivation messaging. |

## What The Company Should Investigate Before Launch

1. **Dormancy versus dissatisfaction:** Customers with stale order recency and no recent visits need reactivation, while return-heavy or support-heavy customers need issue resolution first.
2. **Campaign measurement:** Campaign-history churn differs by last campaign type, but this is not causal proof. Use holdout cells before shifting budget.
3. **Order data reliability:** Duplicate-like orders and high `gross_amount` outliers can distort monetary segmentation and ROI estimates.
4. **Campaign cost data:** Rows with `last_campaign_received = none` and non-zero cost need clarification before campaign ROI reporting.
5. **Feature leakage discipline:** Post-snapshot orders exist in the raw order table and must be excluded from any predictive or segmentation feature.

## Recommended First Retention Test

Run a small segmented pilot rather than a blanket discount:

- **Dormant / low-visit customers:** low-cost replenishment or win-back message.
- **Support or return friction customers:** service recovery before any offer.
- **Recent active customers:** category/bundle personalization.
- **High-value customers:** concierge review and loyalty recognition, not automatic coupons.
