<p align="center">
  <img src="images/olist.png" width="300">
</p>

<h1 align="center">Olist</h1>

<h2 align="center">Project Overview</h2>

Olist is a Brazilian e-commerce platform connecting small and medium-sized merchants to major online marketplaces across the country. Unlike direct retailers, who control inventory, fulfillment, and customer experience end-to-end, Olist operates as a marketplace intermediary — a third-party connector that brings sellers and consumers together and facilitates the transaction without owning either side. Because Olist does not control seller behavior, product selection, or fulfillment directly, performance varies independently at the transaction level, making concentrated underperformance difficult to isolate without multi-variable analysis across the full relational structure of the platform.

That challenge defines this project:

> Which combination of seller behavior, product category, and delivery patterns drives the highest concentration of late deliveries, low customer satisfaction, and revenue risk?

Eight relational tables capture the full transactional picture from purchase through delivery — **99,441** orders, **3,095** sellers, and **32,951** products spanning payment behavior, customer geography, product catalog, and seller fulfillment patterns.

Scoping the relational structure was the first and most consequential decision. The geolocation table — **1,000,163** rows — was excluded entirely. Nearly all of its records were duplicates — geographic coverage was already served by existing state-level columns across the data and coordinate-level precision adds no analytical value to the analysis. Column selection across the remaining eight tables applied the same standard — every excluded column had a reason for removal, and data quality issues in each table were resolved column by column — **five** distinct strategies applied to fundamentally different problems rather than a single blanket approach imposed across all of them.

The analysis identified where underperformance concentrates and what it costs. Among the **3,095** sellers, **308** simultaneously deliver late above the **8.11%** marketplace benchmark and generate satisfaction below the **4.09** marketplace average — a segment carrying **27.39%** of total marketplace revenue — **$5,562,819.39** at risk. Within that segment, bed_bath_table products from sellers in Paraná (PR) represent the sharpest concentration: Olist's highest revenue category at **$1,712,553.67** across **11,115** orders, fulfilled by sellers delivering late at **22.73%** — nearly triple the benchmark — with an average review score of **3.42**.

---
<h2 align="center">Dataset & Scope</h2>

The analysis uses the Olist Brazilian E-Commerce dataset — order-level marketplace transaction data capturing delivery performance, seller behavior, and customer satisfaction across eight relational tables. The order is the central analytical unit connecting every other table — fulfillment, payment, review, seller, product, and customer all trace back to a single order spanning **99,441** transactions and **112,650** line items in `order_items`.

Before analysis began, the `geolocation` table was excluded entirely and columns were scoped across the remaining eight. The full rationale for every exclusion decision is documented in the Data Cleaning section.

| | |
|---|---|
| **Source** | Kaggle — [Olist Brazilian E-Commerce](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce/data) |
| **Files** | 8 relational CSV files |
| **Total Records** | **99,441** orders · **112,650** line items across `order_items `|
| **Tables Retained** | **8** of **9** — geolocation excluded |
| **Structure** | Relational, order-level analytical dataset |

--- 
<h2 align="center">Analytical Methods</h2>

<h3 align="left">Relational Data Integration</h3>

No single table contained sufficient information to answer the core question independently — every meaningful finding required combining order, seller, product, customer, payment, and review data simultaneously across eight relational tables.

<h3 align="left">Missingness and Invalid Value Strategy</h3>

Eight distinct data quality scenarios were resolved on their own terms — each handled according to its specific characteristics rather than a blanket rule applied uniformly across all eight tables.

<h3 align="left">Derived Metric Construction</h3>

Constructed three derived metrics from raw source fields — none existed in the source data. **`is_late_delivery`** measured binary delivery outcome across **96,478** delivered orders. **`delivery_days_variance`** measured the magnitude of early and late deliveries in days — revealing Olist's estimates are systematically conservative by an average of **11.9** days. **`freight_ratio`** measured shipping cost as a proportion of product price — in high freight burden categories, customers pay nearly as much or more in shipping as the product itself.

<h3 align="left">Delivery Performance and Satisfaction Cross-Analysis</h3>

Examined how delivery outcome translates into customer satisfaction across geographic, category, and seller dimensions simultaneously — confirming a **1.72** point review score gap between on-time and late deliveries on a 5-point scale. Delivery timing is the single largest driver of customer satisfaction across the marketplace.

<h3 align="left">Composite Risk Segmentation</h3>

Combined seller late delivery rate and average review score against marketplace benchmarks to classify **3,095** sellers into four performance tiers. The revenue at risk calculation — **$5,562,819.39** carried by **308** sellers delivering late above benchmark and generating below-benchmark satisfaction simultaneously — translates operational underperformance directly into a financial exposure figure.

<h3 align="left">SQL Execution</h3>

Extracted delivery performance metrics, satisfaction scores, seller performance tiers, and revenue exposure figures across **99,441** orders and eight relational tables — applying multi-table JOINs and window functions to support every layer of the analysis from data preparation through composite risk profiling.

---
<h2 align="center">Data Cleaning & Transformation</h2>

Before any cleaning strategy could be applied, eight relational tables required a deliberate load sequence — tables with no dependencies loaded first, tables with relational dependencies loaded after their prerequisites existed.

Duplicate review records posed the highest data integrity risk in the cleaning process — a single order carrying more than one review misrepresents the customer's true satisfaction, and at **547** duplicates across `order_reviews` that distortion compounds into unreliable satisfaction benchmarks at the marketplace level. The **4.09** average review score and the **1.72** point gap between on-time and late deliveries both depend on review records accurately representing one experience per order. Resolving it required three successive approaches before the deduplication held — the most recent review per order was preserved. Where two reviews shared an identical creation date, the higher review score was retained as the tiebreaker.

Cleaning moved column by column across all eight tables — each decision made on the specific characteristics of the column and its role in the analysis rather than a uniform rule applied across all of them.

| Strategy | Table | Column | Decision |
|---|---|---|---|
| Missing value identification | `products`  | `product_category_name` | **610** blank strings marked as missing before missing value flag applied |
| Missing value identification | `orders` | `order_approved_at` | **160** empty strings marked as missing before timestamp conversion |
| Missing value identification | `orders` | `order_delivered_carrier_date` | **1,783** empty strings marked as missing before timestamp conversion |
| Missing value identification | `orders` | `order_delivered_customer_date` | **2,965** empty strings marked as missing before timestamp conversion |
| Implausible value removal | `products` | `product_weight_g` | **6** zero values marked as missing — zero weight physically implausible for any shipped product |
| Implausible value removal | `products` | `product_length_cm` | **2** zero values marked as missing — zero length physically implausible for any shipped product | 
| Implausible value removal | `products` | `product_height_cm` | **2** zero values marked as missing — zero height physically implausible for any shipped product |
| Invalid classification correction | `order_payments` | `payment_type` | **3** records carrying an unrecognizable payment classification marked as missing | 
| Flag, do not impute | `products` | `flag_null_category` | **610** records flagged — no category reliably inferable from other product attributes |
| Flag, do not impute | `products` | `flag_suspect_weight` | **6** records flagged — no reliable estimate possible without knowing actual product weight |
| Structural validation | `orders` | `order_approved_at`, `order_delivered_carrier_date`, `order_delivered_customer_date` | **160**, **1,783**, and **2,965** missing values structurally valid — orders never approved, never picked up, or never delivered have no timestamps by definition |
| Duplicate review removal | `order_reviews` | `review records` | **551** records removed. Most recent review per order preserved — higher review score retained as tiebreaker for identical creation dates |

With all eight tables structurally sound and cleaning decisions documented for every retained column, the data entering the analysis was positioned to answer where Olist's marketplace underperforms and which combination of seller behavior, product category, and delivery patterns drives the highest concentration of risk.

---
<h2 align="center">Key Findings</h2>

<h3 align="left">Marketplace Benchmarks</h3>

| Metric | Value |
|---|---|
| **Late Delivery Rate** | **8.11%** across **96,478** delivered orders |
| **Average Review Score** | **4.09** across **98,673** reviews |
| **Total Marketplace Revenue** | **$16,008,872.12** across **99,441** orders |

<h3 align="left">Finding 1 - Olist's delivery promise is systematically conservative but operationally fragile</h3>

**91.89%** of orders arrive early — **36.22%** by more than 14 days and **55.67%** by 1 to 14 days. The **8.11%** late delivery rate sits against a backdrop of systematic over-estimation. When Olist misses its own conservative promise the failures are significant — averaging **8.9** days late with a maximum of 188 days.

<h3 align="left">Finding 2 - Delivery outcome is the primary driver of customer satisfaction</h3>

On-time deliveries average **4.29** review score across **88,168** orders. Late deliveries average **2.57** across **7,662** orders — a **1.72** point gap on a 5-point scale. Undelivered orders average **1.75** across **2,843** orders — the most severe satisfaction failure in the data, lower even than late deliveries, confirming that customers who never received their order are more dissatisfied than those who received it late.

<h3 align="left">Finding 3 - November 2017 and Q1 2018 represent the two most severe operational findings</h3>

November 2017 late delivery rate spiked to **14.31%** as order volume surged **63%** month over month — Black Friday demand overwhelmed operational capacity. February and March 2018 reached **15.99%** and **21.36%** respectively — the worst two consecutive months in the data. Volume alone does not explain Q1 2018 — an unresolved operational anomaly requiring further investigation.

<h3 align="left">Finding 4 - Maranhão (MA) underperforms on both sides of the transaction simultaneously</h3>

MA customers receive late deliveries at **19.67%** — second highest customer state in the data. MA sellers ship late at **23.63%** — the highest seller state in the data. MA customers average **3.76** review score — joint lowest in the data alongside Alagoas (AL). Maranhão (MA) is the only state appearing in the top two of both the delivery failure and satisfaction failure rankings simultaneously.

<h3 align="left">Finding 5 - Freight burden and late delivery risk concentrate in the same categories</h3>

christmas_supplies carries the highest freight burden at **0.6755** freight ratio — nearly double the **0.3209** marketplace benchmark — combined with a **12.00%** late delivery rate. fashion_underwear_beach at **0.5512** freight ratio and **12.60%** late delivery rate confirms the pattern. In these categories customers pay nearly as much or more in shipping as the product itself — a cost burden that intensifies dissatisfaction when delivery also fails.

<h3 align="left">Finding 6 - bed_bath_table is the highest commercial risk category</h3>

The highest revenue category at **$1,712,553.67** across **11,115** orders with a below-benchmark satisfaction score of **3.90** against the **4.09** marketplace average. At the category-state level, bed_bath_table from Paraná (PR) sellers carries the worst simultaneous delivery and satisfaction performance in the data — **22.73%** late delivery rate and **3.42** average review score. The combination of highest revenue concentration and worst composite operational performance makes this the single most actionable category finding in the data.

<h3 align="left">Finding 7 - 27.39% of marketplace revenue flows through the highest-risk seller segment</h3>

**308** sellers simultaneously deliver late at **16.34%** — double the **8.11%** benchmark — and generate a **3.65** average review score against the **4.09** marketplace benchmark. More than one quarter of Olist's marketplace revenue — **$5,562,819.39** — is carried by sellers underperforming on both delivery and satisfaction metrics at the same time.

---
<h2 align="center">Scripts & Documentation</h2>

- **[Data Cleaning Script](scripts/olist_data_cleaning.sql)**: Resolves the structural, missingness, and encoding issues documented in the Data Cleaning & Transformation section. Each script block is annotated with the reasoning behind every decision — not just what the code does, but why.

- **[Exploratory Data Analysis (EDA) Script](scripts/olist_eda.sql)**:  Runs univariate profiling, bivariate cross-tabulations, threshold analysis, and composite risk segmentation across the cleaned dataset. Each analytical block is documented with the question it was designed to answer.

---
<h2 align="center">Technical Stack</h2>

- **MySQL (v9.5.0)** — data cleaning, transformation, and exploratory analysis




