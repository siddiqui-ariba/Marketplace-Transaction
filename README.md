# Marketplace-Transaction

# Marketplace Commercial Performance Analysis (SQL Case Study)

A MySQL case study that analyses a marketplace's transaction data to understand where growth is profitable, where it is not, and how dependent the business is on discounts and a small group of customers.

## Project Overview

The analysis is built on a single table, `marketplace_transactions`, in the database `case_study_1`. It goes beyond top-line GMV and builds a **contribution proxy** to measure the true economics of each month, customer, category and acquisition cohort.

**Contribution proxy (Delivered orders only):**

```
(quantity × unit_selling_price − discount_amount) − refund_amount − payment_fee − fulfilment_cost
```

## Tools & Skills

- MySQL
- CTEs (multi-step `WITH` queries)
- Window functions: `LAG`, `NTILE`, `CUME_DIST`, `ROW_NUMBER`
- Conditional aggregation with `CASE WHEN`
- Date handling: `DATE_FORMAT`, `STR_TO_DATE`, `DATE_ADD`
- Cohort analysis, concentration analysis, scenario modelling

## Business Questions

### 1. Commercial Performance Bridge
- Monthly GMV, delivered GMV, cancelled GMV, returned GMV
- Discounts, refunds, seller commission, payment fees, fulfilment costs
- Contribution proxy per month
- Month-over-month growth in order count, GMV, delivered GMV and contribution proxy
- Months with **positive GMV growth but negative contribution growth**

### 2. Customer Concentration & Economic Dependency
- Share of delivered GMV and contribution proxy from the top 1%, 5% and 10% of customers
- Repeat-purchase participation
- High-GMV customers (above the 90th percentile) with **negative contribution**
- Are the largest customers also the most profitable?

### 3. Discount Dependency & Commercial Quality
- Discount rate by product category and acquisition channel
- Categories where higher discounts coincide with lower contribution per delivered order
- Customer-level view of average order value, repeat frequency, discount dependency, refund burden and contribution
- High-value transactions with weak post-discount economics

### 4. Acquisition Cohort Quality
- Acquisition-month cohorts by channel
- First-purchase contribution vs. 90-day cumulative contribution
- Repeat-purchase rate by channel
- Cohorts with positive first-purchase economics that deteriorate by day 90
- Flagging of **incomplete 90-day windows** so recent cohorts are not misread

### 5. Commercial Downside Scenario
- Current monthly contribution by category
- Contribution benefit of a 10% reduction in discount expense
- Break-even delivered-GMV decline that cancels that benefit
- Categories with the smallest tolerance for volume decline

## Repository Structure

```
├── case_study_1.sql   # All queries, step by step, with a final consolidated query per section
└── README.md
```

## How to Run

1. Create the database and load the data:
   ```sql
   CREATE DATABASE case_study_1;
   USE case_study_1;
   ```
2. Import the `marketplace_transactions` table (CSV import via MySQL Workbench works fine).
3. Run `case_study_1.sql` section by section. Each section starts with exploratory steps and ends with a final query.

## Expected Columns in `marketplace_transactions`

`order_id`, `customer_id`, `order_timestamp`, `order_status` (Delivered / Cancelled / Returned), `product_category`, `acquisition_channel`, `customer_acquisition_date`, `quantity`, `unit_list_price`, `unit_selling_price`, `discount_amount`, `refund_amount`, `seller_commission_rate`, `payment_fee`, `fulfilment_cost`

## Key Findings

_Add your results here after running the queries, for example:_

- Months where GMV grew but contribution fell, and the main driver (discounts, refunds or fulfilment cost)
- Share of contribution held by the top 10% of customers
- Categories with the lowest break-even tolerance for volume decline
- Channels whose cohorts look profitable on first purchase but weaken by day 90

## Assumptions & Notes

- GMV is calculated as `quantity × unit_selling_price − discount_amount`.
- Contribution proxy is calculated on **Delivered** orders only.
- Seller commission is estimated as `GMV × seller_commission_rate` for delivered orders.
- Cohorts whose 90-day window extends past the last order date are flagged as incomplete (`cohort_complete = 'No'`).

## Author

Ariba
