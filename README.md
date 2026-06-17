# Customer Churn Analysis, RFM & Segmentation (SQL - BigQuery)

## Overview

This project analyzes 541,909 e-commerce transactions from a UK-based online retail store using SQL in BigQuery to identify customer churn patterns, build RFM-based customer profiles, and segment customers into actionable groups for targeted retention and marketing strategies.

---

## Executive Summary

This project applies RFM (Recency, Frequency, Monetary) analysis and behavioral churn detection to 4,338 unique customers across 1 full year of transaction data (December 2010 – December 2011).

Key findings reveal that **22.2% of customers (Champions) generate 65.2% of total revenue** — making retention of this group the single highest-priority business action. Meanwhile, **33.4% of customers have churned** (no purchase in 90+ days), putting £1,035,270 in historical revenue at risk. The At Risk segment, representing 10.5% of customers, shows a 75.6% churn rate and requires immediate win-back campaigns.

> **Dataset Note:** Raw dataset contained 541,909 rows. After removing cancelled transactions (InvoiceNo prefix 'C'), NULL CustomerIDs, and invalid Quantity/UnitPrice values, **397,884 valid rows across 4,338 customers** were used for analysis. All cleaning decisions are documented in Step 0–1 of the SQL file.

---

## Business Problem

Acquiring new customers costs significantly more than retaining existing ones. Without visibility into *which* customers are at risk of churning, *how valuable* each customer segment is, and *what action to take* per segment — businesses apply blanket marketing strategies that waste budget on low-value customers while neglecting high-value ones.

This project answers three core questions:

1. **Who are our most and least valuable customers — and why?**
2. **Which customers are at risk of churning, and how much revenue is at stake?**
3. **What is the right action for each customer segment?**

---

## Project Objectives

- Clean and validate raw transactional data before any analysis
- Calculate RFM metrics (Recency, Frequency, Monetary) per customer
- Score customers on a 1–5 scale across all three RFM dimensions
- Segment customers into 8 behavioral groups based on RFM scores
- Define and measure customer churn (90-day inactivity threshold)
- Quantify revenue at risk from churned and at-risk segments
- Provide actionable, segment-specific business recommendations

---

## Key Performance Indicators (KPIs)

| Metric | Value |
|---|---|
| Total Transactions (raw) | 541,909 rows |
| Valid Transactions (after cleaning) | 397,884 rows |
| Unique Customers | 4,338 |
| Total Orders | 18,532 |
| Total Revenue | £8,911,407.90 |
| Analysis Period | Dec 2010 – Dec 2011 |
| Snapshot Date (for Recency) | 10 Dec 2011 |
| Avg Customer Recency | 92.5 days |
| Avg Order Frequency | 4.3 orders |
| Avg Customer Monetary Value | £2,054 |

---

## Methodology

### RFM Framework
RFM is a proven customer analytics framework that scores each customer across three dimensions:

| Dimension | Definition | Direction |
|---|---|---|
| **Recency (R)** | Days since last purchase | Lower = Better |
| **Frequency (F)** | Number of unique orders | Higher = Better |
| **Monetary (M)** | Total spend in £ | Higher = Better |

Each dimension is scored 1–5 using `NTILE(5)`, where **5 = best** and **1 = worst**. The three scores are combined into an RFM Score (e.g., "554" = Champion).

### Churn Definition
A customer is defined as **churned** if their most recent purchase occurred **more than 90 days** before the snapshot date. This threshold reflects typical e-commerce repurchase cycles and is consistent with industry benchmarks.

### SQL Techniques Used
- `CREATE OR REPLACE TABLE` for intermediate tables
- Common Table Expressions (CTE)
- `NTILE(5)` window function for percentile-based RFM scoring
- `DATE_DIFF()` for recency calculation
- `CASE WHEN` for segment labeling and churn flagging
- `PARSE_DATETIME()` for date parsing
- Aggregation: `SUM`, `COUNT DISTINCT`, `AVG`, `ROUND`
- `LAG()` for month-over-month revenue trend
- `SUM() OVER()` for revenue contribution percentages

---

## Key Findings

### 1. Data Cleaning
| Issue | Rows Affected |
|---|---|
| NULL CustomerID | 135,080 (24.9%) |
| Cancelled transactions | 9,288 |
| Invalid Quantity (≤0) | 10,624 |
| Invalid UnitPrice (≤0) | 2,517 |
| **Valid rows retained** | **397,884 (73.4%)** |

### 2. Revenue Trend
- Revenue grew steadily throughout 2011, peaking at **£1.16M in November 2011**
- December 2011 shows an apparent drop — this is due to **partial data (only 9 days)**
- United Kingdom accounts for **82% of total revenue (£7.3M)** — dominant market
- Netherlands, EIRE, Germany, and France are the top international markets

### 3. RFM Customer Segmentation

| Segment | Customers | % Customers | Avg Recency | Avg Frequency | Avg Monetary | Revenue | % Revenue |
|---|---|---|---|---|---|---|---|
| 🏆 Champions | 962 | 22.2% | 13 days | 11.1 orders | £6,039 | £5,809,341 | **65.2%** |
| 💙 Loyal Customers | 998 | 23.0% | 34 days | 3.7 orders | £1,477 | £1,474,128 | 16.5% |
| ⚠️ At Risk | 454 | 10.5% | 142 days | 3.8 orders | £1,635 | £742,150 | 8.3% |
| 😴 Hibernating | 241 | 5.6% | 182 days | 1.3 orders | £1,368 | £329,638 | 3.7% |
| ❌ Lost | 1,000 | 23.1% | 219 days | 1.3 orders | £244 | £243,935 | 2.7% |
| 🌱 Potential Loyalists | 319 | 7.4% | 19 days | 1.2 orders | £458 | £146,167 | 1.6% |
| 📣 Promising | 351 | 8.1% | 54 days | 1.2 orders | £461 | £161,833 | 1.8% |
| 🚨 Cant Lose Them | 13 | 0.3% | 230 days | 3.5 orders | £324 | £4,217 | 0.05% |

### 4. Churn Analysis

| Metric | Value |
|---|---|
| **Overall Churn Rate** | **33.4%** |
| Churned Customers | 1,449 out of 4,338 |
| Active Customers | 2,889 |
| Revenue at Risk | **£1,035,270 (11.6% of total)** |

**Churn Rate by Segment:**

| Segment | Churn Rate | Revenue at Risk |
|---|---|---|
| Cant Lose Them | 100.0% | £4,217 |
| Lost | 90.6% | £243,935 |
| Hibernating | 77.6% | £329,638 |
| At Risk | **75.6%** | **£742,150** |
| Champions | 0.0% | — |
| Loyal Customers | 0.0% | — |
| Potential Loyalists | 0.0% | — |
| Promising | 0.0% | — |

> At Risk is the most critical segment: 75.6% churn rate with £742,150 revenue at stake — the highest risk-adjusted priority for win-back campaigns.

---

## Business Insights

- **Champions are irreplaceable.** 962 customers (22.2%) generate £5.8M (65.2%) of total revenue. Losing even a fraction of this group would have an outsized impact on business performance.
- **One-third of customers have churned.** With a 33.4% churn rate and £1M+ in historical revenue now silent, reactive retention is no longer sufficient — proactive early-warning systems are needed.
- **At Risk segment is the highest-value win-back opportunity.** These customers have proven purchase history (avg 3.8 orders, £1,635 spend) but haven't bought in 142 days. Re-engaging them costs far less than acquiring equivalent new customers.
- **Lost segment should not be the focus.** Despite being the largest segment by count (1,000 customers), they contribute only 2.7% of revenue (£244K). Blanket re-engagement here would be inefficient.
- **Potential Loyalists need nurturing, not discounts.** Recent buyers (avg 19 days) with low frequency (1.2 orders) — the right intervention is personalized recommendations and onboarding, not price cuts.

---

## Business Recommendations

| Priority | Segment | Action | Rationale |
|---|---|---|---|
| 🔴 Critical | Champions | Loyalty program, exclusive perks, early access | Protects 65.2% of revenue — any churn here is catastrophic |
| 🔴 Critical | At Risk | Personalized win-back email, time-limited offer | 75.6% churn rate, £742K at risk — highest ROI intervention |
| 🟠 High | Loyal Customers | Upsell campaigns, product recommendations | Already engaged — natural candidates for spend increase |
| 🟠 High | Cant Lose Them | Immediate personal outreach, direct re-engagement | 100% churned — high frequency history suggests salvageable |
| 🟡 Medium | Potential Loyalists | Welcome series, product education, gentle nudges | Recent buyers who haven't become habitual yet |
| 🟡 Medium | Promising | Engagement campaigns, first-repeat-purchase incentive | Moderate recency — window of opportunity still open |
| 🟢 Low | Hibernating | Low-cost reactivation (email only), survey for feedback | Some historical value, but low conversion probability |
| ⚪ Deprioritize | Lost | Exit survey only, do not invest in paid re-engagement | 2.7% revenue contribution — cost of re-acquisition too high |

---

## Technical Notes

### Tools
- SQL (BigQuery)
- Looker Studio (Dashboard)

### Dataset
- [Online Retail Transaction Data — Kaggle](https://www.kaggle.com/datasets/thedevastator/online-retail-transaction-data)
- UK-based e-commerce store | Dec 2010 – Dec 2011
- 541,909 raw rows → 397,884 validated rows | 4,338 unique customers

---

## Interactive Dashboard

> [*Looker Studio dashboard link — coming soon*](https://datastudio.google.com/reporting/49096c50-dac4-4ef3-8b0d-fb3606736d12)

---

## Conclusion

This project demonstrates how SQL-based RFM analysis and behavioral churn detection can transform raw transaction logs into a clear, actionable customer strategy.

By scoring 4,338 customers across Recency, Frequency, and Monetary dimensions, segmenting them into 8 behavioral groups, and quantifying £1,035,270 in revenue at risk from churn — this analysis provides a data-driven foundation for prioritizing retention investments where they matter most.

The core finding is not just that churn is happening, but *where it matters*: the At Risk segment represents the intersection of high historical value and active disengagement — making it the single most important target for immediate business action.
