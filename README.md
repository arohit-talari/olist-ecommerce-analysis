<p align="center">
  <img src="images/olist.png" width="300">
</p>

<h1 align="center">Olist</h1>

<h2 align="center">Project Overview</h2>

Olist is a Brazilian e-commerce platform connecting small and medium-sized merchants to major online marketplaces across the country. Unlike direct retailers, who control inventory, fulfillment, and customer experience end to end, Olist operates as a marketplace intermediary, a third-party connector that brings sellers and consumers together and facilitates the transaction without owning either side of it. Because Olist does not control seller behavior, product selection, or fulfillment directly, performance varies independently across thousands of transactions, making concentrated underperformance difficult to isolate without multi-variable analysis across the full relational structure of the platform.

That challenge defines this project:

> Which combination of seller behavior, product category, and delivery patterns drives the highest concentration of late deliveries, low customer satisfaction, and revenue risk?

The data spans 8 relational tables covering **99,441** orders, **3,095** sellers, and **32,951** products — capturing the full transactional picture from purchase through delivery across payment behavior, customer geography, product catalog, and seller fulfillment patterns.

Scoping the relational structure was the first and most consequential decision. The geolocation table — **1,000,163** rows — was excluded entirely. Nearly all of its records were duplicates - geographic coverage was already served by existing state-level columns across the data and coordinate-level precision adds no analytical value to marketplace performance analysis. Column selection across the remaining eight tables applied the same standard — every excluded column had a reason for removal, and data quality issues across all eight were resolved column by column — **five** distinct strategies applied to fundamentally different problems rather than a single blanket approach imposed across all of them.

The analysis identified where underperformance concentrates and what it costs. Across the **3,095** seller base, **308** sellers simultaneously deliver late above the **8.11%** marketplace benchmark and generate satisfaction below the **4.09** marketplace average — a segment carrying **27.39%** of total marketplace revenue, **$5,562,819.39** at risk. Within that segment, bed_bath_table products from sellers in Paraná (PR) represent the sharpest concentration: Olist's highest revenue category at **$1,712,553.67** across **11,115** orders, fulfilled by sellers delivering late at **22.73%** — nearly triple the benchmark — with an average review score of **3.42**.

---
<h2 align="center">Dataset & Scope</h2>

The analysis uses the Olist Brazilian E-Commerce dataset — order-level marketplace transaction data capturing delivery performance, seller behavior, and customer satisfaction across eight relational tables. The order is the central analytical unit connecting every other table — fulfillment, payment, review, seller, product, and customer all trace back to a single order across **99,441** transactions and **112,650** line items in order_items.

Before analysis began, the geolocations table was excluded entirely and columns were scoped across the remaining eight. The full rationale for every exclusion decision is documented in the Data Cleaning section.

| | |
|---|---|
| **Source** | Kaggle — [Olist Brazilian E-Commerce](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce/data) |
| **Files** | 8 relational CSV files |
| **Total Records** | 99,441 orders · 112,650 line items across order_items |
| **Tables Retained** | 8 of 9 - geolocation excluded |
| **Structure** | Relational, order-level analytical dataset |

--- 
<h2 align="center">Analytical Methods</h2>

<h3 align="left">Relational Data Integration</h3>

No single table contained sufficient information to answer the core question independently — every meaningful finding required combining order, seller, product, customer, payment, and review data simultaneously across eight relational tables.

<h3 align="left">Missingness and Invalid Value Strategy</h3>

Eight distinct data quality scenarios were resolved on their own terms — each handled according to its specific characteristics rather than a blanket rule applied uniformly across all eight tables.

<h3 align="left">Derived Metric Construction</h3>

Constructed three derived metrics from raw source fields — none existed in the source data. **is_late_delivery** measured binary delivery outcome across **96,478** delivered orders. **delivery_days_variance** measured the magnitude of early and late deliveries in days — revealing Olist's estimates are systematically conservative by an average of **11.9** days. **freight_ratio** measured shipping cost as a proportion of product price — in high freight burden categories, customers pay nearly as much or more in shipping as the product itself.

<h3 align="left">Delivery Performance and Satisfaction Cross-Analysis</h3>

Examined how delivery outcome translates into customer satisfaction across geographic, category, and seller dimensions simultaneously — confirming a **1.72** point review score gap between on-time and late deliveries on a 5-point scale. Delivery timing is the single largest lever Olist has on customer satisfaction, and the primary driver of review score variation across the entire marketplace.

<h3 align="left">Composite Risk Segmentation</h3>

Combined seller late delivery rate and average review score against marketplace benchmarks to classify **3,095** sellers into four performance tiers. The revenue at risk calculation — **$5,562,819.39** carried by **308** sellers delivering late above benchmark and generating below-benchmark satisfaction simultaneously — translates operational underperformance directly into a financial exposure figure.

<h3 align="left">SQL Execution</h3>

Extracted delivery performance metrics, satisfaction scores, seller performance tiers, and revenue exposure figures across **99,441** orders and eight relational tables — applying multi-table JOINs and window functions to support every layer of the analysis from data preparation through composite risk profiling.

---
<h2 align="center">Data Cleaning & Transformation</h2>

---
<h2 align="center">Key Findings</h2>


---
<h2 align="center">Scripts & Documentation</h2>

- **[Data Cleaning Script](scripts/olist_data_cleaning.sql)**: Resolves the structural, missingness, and encoding issues documented in the Data Cleaning & Transformation section. Each script block is annotated with the reasoning behind every decision — not just what the code does, but why.

- **[Exploratory Data Analysis (EDA) Script](scripts/olist_eda.sql)**:  Runs univariate profiling, bivariate cross-tabulations, threshold analysis, and composite risk segmentation across the cleaned dataset. Each analytical block is documented with the question it was designed to answer.

---
<h2 align="center">Technical Stack</h2>

- **MySQL (v9.5.0)** — data cleaning, transformation, and exploratory analysis




