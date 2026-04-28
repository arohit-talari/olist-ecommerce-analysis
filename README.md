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
