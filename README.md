# BuyerLens

## Customer Shopping Behavior Analytics

**A customer shopping behavior analytics portfolio project by Tejas Rastogi**

[GitHub repository](https://github.com/tejasrastogileet/BuyerLens)

BuyerLens adapts an existing customer shopping behavior analysis into a clearly attributed portfolio project. It retains the source dataset and core Python/SQL analysis while aligning the editable notebook, SQL, README, and presentation to a MySQL workflow and verified dataset results. The PDFs and PBIX are preserved unchanged, with their limitations noted below.

## Business problem

Retail teams need to understand how purchase patterns vary across demographics, product categories, discounts, subscriptions, shipping methods, and purchase history. This project uses the supplied transaction-level dataset to explore those questions and support further investigation of customer engagement and product strategy.

### Objectives

- Prepare the source CSV for analysis and document data quality.
- Explore purchasing patterns by gender, product, subscription, shipping type, and age group.
- Use SQL to answer the project's business questions against a cleaned `customer` table.
- Present existing dashboard and report deliverables without inventing features or results.

## Dataset

[`customer_shopping_behavior.csv`](./customer_shopping_behavior.csv) contains **3,900 purchase records and 18 columns**. It includes customer attributes, product and category, purchase amount, season, review rating, subscription status, shipping type, discount and promo-code fields, previous-purchase count, payment method, and purchase frequency.

Verified data-quality observations:

- The source has **37 missing values**, all in `Review Rating`.
- The source has no fully duplicated rows; `Customer ID` is unique in this file.
- `Discount Applied` and `Promo Code Used` match for every row in this dataset. The notebook checks this before dropping `promo_code_used`.
- Each row is a purchase record. Since `Customer ID` is unique within this dataset, the `Previous Purchases` field is historical information; the file does not provide repeated customer records for longitudinal analysis.

## Project workflow

1. **Load and inspect:** Read the CSV with pandas and inspect its structure, descriptive statistics, null counts, and duplicate rows.
2. **Clean:** Impute missing review ratings with the median rating within each product category and standardize column names to snake case.
3. **Engineer features:** Create age quartiles (`age_group`) and map purchase-frequency labels to an approximate number of days (`purchase_frequency_days`).
4. **Check redundant fields:** Compare discount and promo-code indicators; remove the duplicate promo-code field from the analysis table.
5. **Analyze:** Use MySQL queries for the business questions below.
6. **Present:** Explore the existing Power BI report and read the accompanying report and presentation.

The notebook performs data inspection and preparation; it does not generate notebook charts. The supplied Power BI file contains the project's visual report.

## Key results from the source data

The following values were recalculated from the CSV using the notebook's category-median rating imputation where relevant. Purchase-amount totals are sums of the `Purchase Amount (USD)` field; they are not profit or margin figures.

| Analysis | Result |
|---|---|
| Purchase amount by gender | Male: **$157,890**; Female: **$75,191** |
| Average purchase amount by shipping type | Express: **$60.48**; Standard: **$58.46** |
| Average purchase amount by subscription status | Subscribers: **$59.49**; non-subscribers: **$59.87** |
| Purchase amount total by subscription status | Subscribers: **$62,645**; non-subscribers: **$170,436** |
| Subscriber share of total purchase amount | **26.88%** |
| Highest average product ratings | Gloves **3.86**, Sandals **3.84**, Boots **3.82**, Hat **3.80**, Skirt **3.78** |

These are descriptive comparisons, not evidence that a subscription or shipping method causes a change in spend. Customer segments in the SQL use the project's explicit `Previous Purchases` rules; they should not be read as observed customer lifecycles.

## SQL analysis

[`customer_behavior_sql_queries.sql`](./customer_behavior_sql_queries.sql) retains the original ten business questions:

1. Purchase amount by gender
2. Discount users spending above the overall average
3. Products with the highest average review ratings
4. Standard versus Express shipping
5. Subscribers versus non-subscribers
6. Products with the highest discount-use rates
7. New, Returning, and Loyal record segments
8. Top three purchased products per category
9. Repeat-purchase share by subscription status
10. Purchase amount by age group

The queries target **MySQL 8.0 or later** because they use common table expressions and a window function. Query 3 uses MySQL-compatible numeric aggregation. Query 9 compares repeat-buyer rates within subscription groups rather than reporting counts alone. The queries were reviewed against the cleaned column names but **were not executed against a MySQL server** in this workspace.

## Power BI dashboard

[`customer_behavior_dashboard.pbix`](./customer_behavior_dashboard.pbix) is the existing Power BI report. Its accessible package metadata shows one report page with customer-count, average purchase amount, average review rating, subscription, category, shipping, and age-group visuals and slicers for subscription status, gender, category, and shipping type. Open it in Power BI Desktop to inspect and interact with the report.

The PBIX was not rebranded or saved: safe editing requires Power BI Desktop, and the dashboard was reviewed through package metadata only. The report page was not rendered or visually validated in Power BI in this workspace.

## How to run the notebook

Run these commands from the repository directory so the notebook can find the CSV:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install pandas jupyter
jupyter lab
```

Open [`Customer_Shopping_Behavior_Analysis.ipynb`](./Customer_Shopping_Behavior_Analysis.ipynb) and run the cells in order. The analysis and feature engineering run without a database connection. The optional MySQL load cell is skipped unless its database environment variables are set.

To enable the optional MySQL load, install its additional dependencies:

```powershell
python -m pip install sqlalchemy pymysql
$env:BUYERLENS_DB_USER = "your-local-user"
$env:BUYERLENS_DB_PASSWORD = "your-local-password"
$env:BUYERLENS_DB_NAME = "customer_behavior"
# Optional; defaults to localhost and port 3306:
$env:BUYERLENS_DB_HOST = "localhost"
$env:BUYERLENS_DB_PORT = "3306"
```

Create the `customer_behavior` database in MySQL before loading. The notebook uses `if_exists="fail"` so it will not silently replace an existing `customer` table. Keep credentials in local environment variables; never commit them.

To run the SQL analysis, execute [`customer_behavior_sql_queries.sql`](./customer_behavior_sql_queries.sql) in a MySQL 8.0+ client after the cleaned table has been loaded. To view the dashboard, open the PBIX in Power BI Desktop.

## Repository structure

```text
.
├── .gitignore
├── Business Problem  Document.pdf
├── Customer Shopping Behavior Analysis.pdf
├── Customer-Shopping-Behavior-Analysis.pptx
├── Customer_Shopping_Behavior_Analysis.ipynb
├── README.md
├── customer_behavior_dashboard.pbix
├── customer_behavior_sql_queries.sql
└── customer_shopping_behavior.csv
```

## Original work, attribution, and license

The project is adapted from **Customer Shopping Behavior Analysis** by **Meghan Gowda B Y**. The original source repository is [meghangowdru-debug/customer-shopping-behavior-analysis](https://github.com/meghangowdru-debug/customer-shopping-behavior-analysis). The source dataset and original analytical deliverables are retained and attributed; the BuyerLens branding does not imply authorship of the original work.

No license file is present in the cloned source repository reviewed for this update (the repository history records removal of its MIT license). This README does not create or imply a license for the original material. Confirm reuse and redistribution rights with the original author where needed.

## Deliverables and known limitations

- [Business problem statement](./Business%20Problem%20%20Document.pdf)
- [Data analysis report](./Customer%20Shopping%20Behavior%20Analysis.pdf)
- [Project presentation](./Customer-Shopping-Behavior-Analysis.pptx)
- The notebook, SQL file, README, dataset, and Power BI report are maintained in this repository.
- Both supplied PDFs are preserved unchanged. They have no editable source files, and their database narrative still refers to PostgreSQL; that connection was not independently verified. Update them from their source documents if a consistent MySQL narrative is required.
- The PBIX remains unchanged and still needs manual review/rebranding in Power BI Desktop.
- The presentation was updated in place with BuyerLens branding and corrected dataset-backed figures. Its original design graphics remain part of the source presentation.

## Author

**Tejas Rastogi**<br>
[BuyerLens on GitHub](https://github.com/tejasrastogileet/BuyerLens)
