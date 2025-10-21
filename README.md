# E-Commerce-Sales-Analysis
Olist Store is the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. The Brazilian ecommerce public dataset of orders (from 2016 to 2018) made at Olist Store is provided to your company for analysis.
# E-Commerce Sales Analysis

This repository contains SQL queries and exploratory analysis for an e-commerce dataset (Olist-style). I generated a README that explains the repository structure, the main SQL file (`ecommercesql.sql`), the queries included, and how to run them locally. Below you'll find a concise guide explaining what each query does, how to execute the queries, and suggestions for next steps.

## Table of contents
- Overview
- Dataset (structure expectations)
- Files
- Queries included (what each does)
- How to run (local MySQL / MariaDB)
- Notes & tips
- Contributing
- License
- Author / Contact

## Overview
This project analyzes order, payment, review, product and customer data from an e-commerce dataset using SQL. The provided SQL file contains queries to compute insights such as:
- Payment and review relationships
- Weekday vs weekend sales statistics
- Delivery time (days) by category
- City-level price/payment summaries
- Shipping days vs review score relationships
- Year-over-year payment type trends
- Year-wise order counts

These queries are intended for exploratory analysis and to form a basis for visualizations or additional analysis in notebooks or BI tools.

## Dataset (expected tables)
The SQL expects a database named `ecommerce` and the following tables (names used exactly as in the SQL file):
- `orders`
- `olist_order_payments_dataset`
- `reviews`
- `geolocation`
- `items`
- `customers`
- `products`
- `sellers`
- `category`

Columns used by the queries include (examples — actual dataset may contain more fields):
- orders: `order_id`, `order_purchase_timestamp`, `order_delivered_customer_date`, `customer_id`
- olist_order_payments_dataset: `order_id`, `payment_type`, `payment_value`
- reviews: `order_id`, `review_score`
- items: `order_id`, `product_id`, `price`
- products: `product_id`, `product_category_name`
- customers: `customer_id`, `customer_city`

Adjust table or column names if your data uses different naming.

## Files
- `ecommercesql.sql` — Main SQL script with data selection and analytics queries:
  - Creates/uses database `ecommerce`
  - Select * queries for multiple tables (quick preview)
  - Analytical queries (explained below)

## Queries included (explanations)

1) Count of orders paid by credit card with 5-star reviews
- Purpose: Measures how many orders paid via `credit_card` received a perfect review (score 5).
- Core join: `orders` → `olist_order_payments_dataset` → `reviews`
- Useful for: Understanding satisfaction by payment type.

2) Weekday vs Weekend statistics (payment totals, count, average)
- Purpose: Split orders by weekday/weekend and compute:
  - Total payment (rounded)
  - Total orders
  - Average payment value
- Note: The SQL uses `weekday(a.order_purchase_timestamp)` with a case to classify weekend vs weekday.

3) Average delivery days by product category (example uses `pet_shop`)
- Purpose: For `pet_shop` category, compute average days between purchase and delivery.
- Core join: `orders` → `items` → `products`
- Useful for: Category-specific shipping performance.

4) São Paulo customers — average item price and average payment value
- Purpose: Compute average item price and average payment value for customers in city "sao paulo".
- Core join: `items` → `olist_order_payments_dataset` → `orders` → `customers`
- Useful for: City-level revenue & price insights.

5) Shipping days vs review score
- Purpose: Calculate average shipping days grouped by `review_score` to explore correlation between delivery speed and rating.
- Core join: `orders` → `reviews`
- Note: The query uses `abs(avg(datediff(...)))` and groups by `review_score`.

6) Year-wise payment type counts
- Purpose: For each year (from `order_purchase_timestamp`) and payment type, count number of payments. Useful for trend analysis by payment method.

7) Year-wise order counts
- Purpose: Counts orders per year to show order volume trends over time.

## How to run
Requirements:
- MySQL or MariaDB (or any SQL engine that supports the functions used: YEAR(), WEEKDAY(), DATEDIFF(), ROUND()).
- The dataset loaded into the database with the expected table names and columns.

Steps:
1. Start your MySQL server and connect:
   - mysql -u <user> -p
2. Create the database and load your tables (if not already loaded). Example (if you have .sql/table dumps):
   - SOURCE path/to/dump.sql;
3. Run the provided SQL file:
   - From shell: mysql -u <user> -p < ecommerce_init.sql
   - Or inside the mysql shell: SOURCE /path/to/ecommercesql.sql;
4. Inspect results for each SELECT query. Adjust column/table names if your dataset differs.

If you prefer an interactive approach, copy individual query blocks from `ecommercesql.sql` into your SQL client and run them one at a time.

## Example: Running one of the queries
To run the credit-card & 5-star review count query, copy this (from the SQL file) into your SQL client:
SELECT 
    count(orders.order_id) AS total_count
FROM 
    orders
JOIN 
    `olist_order_payments_dataset`
ON 
    orders.order_id = `olist_order_payments_dataset`.order_id
JOIN 
    reviews
ON 
    `olist_order_payments_dataset`.order_id = reviews.order_id
WHERE 
    payment_type = 'credit_card' 
    AND review_score = 5;

The result will be a single number column `total_count`.

## Notes & tips
- Date/time functions and weekday indices might differ across DBMS. Verify `WEEKDAY()` behavior and adjust if necessary (some systems use 0–6 mapping where 0 is Monday).
- The dataset naming is Olist-like; if you imported Olist CSVs, ensure encoding and NULL handling are consistent.
- Consider indexing `order_id` columns across tables for faster joins.
- When moving to production analysis or dashboards, convert these queries into parameterized views or materialized tables for performance.

## Next steps / suggested enhancements
- Add visualization notebooks (Jupyter / R Markdown) that connect to the database and produce charts (sales over time, payment type mix, delivery-day distribution).
- Add data-cleaning queries to handle missing delivery dates and inconsistent timestamps.
- Create reproducible ETL scripts to load raw CSVs into the `ecommerce` schema.
- Add unit tests or query validation steps to ensure results are stable as data changes.

## Contributing
Contributions are welcome. Suggested workflow:
- Fork the repo
- Create a feature branch
- Add SQL improvements, documentation, or notebooks
- Open a pull request describing changes

Please add clear descriptions for any new queries and include expected output examples.

## License
This repository does not specify a license. If you want this repository to be open-source, add a LICENSE file (for example MIT or Apache 2.0).

## Author / Contact
Repository owner: vishwanreddy

---


# Olist Store Analysis Dashboard

A concise, actionable README for the Tableau dashboard "Olist Store Analysis" — built to summarize marketplace performance, surface trends, and guide decisions for merchants, operations and data teams.

---

## Overview
This dashboard aggregates order and payment data from the Olist marketplace to present high-level KPIs, temporal trends, and customer / product behavior. It helps stakeholders quickly answer questions like:
- How many customers, sellers and unique products are active?
- What are total sales and profit?
- Which payment methods and purchase days dominate?
- How do review scores relate to shipping time?

---

## Key Metrics (Quick glance)
- Total Customers: 96,096  
- Total Sales (Payment Value): 16,008,872  
- Total Profit: 2,417,228  
- Total Unique Products: 73  
- Total Sellers: 3,095

---

## Visuals & What They Show
- KPI Tiles: Top-of-dashboard summary for Customers, Sales, Profit, Unique Products, Sellers.
- Weekday vs Weekend Pie: Weekday orders = 77.2% (12,367,988), Weekend orders = 22.8% (3,640,884).  
  - Interpretation: Most purchases occur on weekdays; weekend campaigns could target a smaller but valuable segment.
- Payment Type (bar chart): Counts by payment method:
  - credit_card: 76,313
  - boleto: 19,762
  - voucher: 3,856
  - debit_card / not_defined / Null: minor counts
  - Interpretation: Credit cards dominate, so optimizing card payment flows and fraud checks yields high ROI.
- Avg. Price vs Avg. Payment Value (city-level example: São Paulo)
  - Avg. Price ~ 107.53, Avg. Payment Value ~ 135.83 (indicating discounts, shipping add-ons, or multi-item purchases)
- Review Score vs Avg. Shipping Days (vertical bars):
  - review_score 1 → avg_shipping_days 21
  - Null → 19
  - 2 → 17
  - 3 → 14
  - 4 → 12
  - 5 → 11
  - Interpretation: Higher review scores correlate with lower shipping times — shipping reliability impacts satisfaction.
- Order Purchase Timestamp (annual payment value):
  - 2018 → 8,699,763
  - 2017 → 7,249,747
  - 2016 → 59,362
  - Interpretation: Strong growth year-over-year across available years.
- Product Category Highlights (arrow markers show counts/importance):
  - fashion_calcados: 16
  - pcs: 14
  - pet_shop: 11
  - Interpretation: Footwear and general-purpose products are among top categories by metric shown.

---

## Data Sources
- Primary: Olist transaction/order dataset (orders, payments, order_items, sellers, customers, reviews)
- Key fields used: order_purchase_timestamp, payment_value, payment_type, customer_city, product_category_name, seller_id, order_id, review_score, shipping_days

---

## Methodology & Calculations
- Total Sales: sum(payment_value) by filters applied
- Total Profit: revenue - cost (as available or approximate/proxy from dataset)
- Avg. Price: average unit price per order or per product, depending on dataset join
- Shipping days: calculated from order_purchase_timestamp → order_delivered_carrier_date / order_delivered_customer_date (difference)
- Review-score grouping: group NULL and numeric values, plot avg shipping days per group

---

## Dashboard Interactions & Filters
- Filters available on right pane: Year of Order Purchase, review_score slider, Payment Type (multi-select), Customer City (scroll list)
- Interactive features: Hover tooltips on bars/pie, clickable legends to isolate categories, top KPI tiles update with applied filters

---

## Key Insights & Recommendations
- Prioritize improvements in shipping operations — largest impact on review scores and customer satisfaction.
- Focus conversion and UX tests on credit-card flow; it's the largest payment channel.
- Target weekday marketing for scale; consider weekend promotions to increase share on lower-traffic days.
- Promote top product categories (fashion_calcados, pcs, pet_shop) and analyze margin contribution per category.
- Investigate 2016 vs later years to validate data completeness; apparent low value in 2016 suggests partial data or low adoption.

---

## Suggested Next Steps
- Add profitability by category and seller-level margins to identify high-value segments.
- Build cohort analysis to measure retention and repeat purchase behavior.
- Add map / geospatial visual to analyze city-level concentration and logistics hubs.
- Integrate shipping SLA KPIs and vendor-level performance dashboards.
- Expose CSV/SQL query or API endpoint to reproduce the charts programmatically.

## Reproducing the Key Charts
1. Data cleaning: dedupe orders, join payments/order_items/products/reviews, compute shipping_days.
2. Aggregate KPIs at chosen grain (order-level for most metrics).
3. Build visuals (Tableau or viz library): KPI tiles, pie for weekday/weekend, bar charts for payment types and yearly totals, line/bar for avg shipping days by review score.
4. Add interactive filters and test performance on sample vs full data.

---
