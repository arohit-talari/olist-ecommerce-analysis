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
