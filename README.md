# Northwind Traders: Sales & Operations BI Engine

This repository contains a comprehensive Power BI business intelligence solution focused on analyzing sales and operational performance for Northwind Traders. Utilizing a text-based Power BI Project (`.pbip`) architecture for clean version control, this dashboard transforms raw operational data into actionable insights—tracking key performance metrics, regional sales distributions, and supply chain efficiencies to drive data-driven decision-making.

---

## 📌 Problem Statement & Objectives

### 1. Problem Statement
* **Siloed Operations:** Lacked a centralized, data-driven view across products, customers, employees, and shipping partners.
* **Global Blind Spots:** Operating across 21 countries and 69 cities obscured localized performance gaps and customer behaviors.
* **Logistics Bottlenecks:** Poor operational visibility hid inefficiencies within the end-to-end order fulfillment cycle.
* **Strategic Risk:** Decision-making relied heavily on lagging indicators, unlinked spreadsheets, and fragmented data.

### 2. Objectives
* **Data Modeling:** Transform raw data into a structured Star Schema using Power Query.
* **Advanced Analytics:** Implement DAX to calculate KPIs and time-intelligence metrics.
* **Business Insights:** Analyze sales, customer behavior, and logistics performance.
* **Data Storytelling:** Build interactive dashboards to drive data-driven decisions.
* **Efficiency & Benchmarking:** Measure shipping efficiency (on-time delivery rates, carrier performance) and evaluate employee performance by revenue generation.

---

## 🛠️ Data Engineering & Project Architecture

### 1. Data Processing & Cleansing (Power Query)
* **Missing Value Allocation:** `orders/shippedDate` containing null or blank values were assumed and left as-is to indicate an unfulfilled or pending order to the customer.
* **Text Normalization:** All text-based columns were explicitly processed via a `Trim` function to eliminate trailing whitespaces and enforce clean reporting boundaries.
* **Strong Type Conversions:** Column types were locked down to prevent dynamic computation drifting:
  * `orderDate`, `requiredDate`, `shippedDate` $\rightarrow$ **Date Type** (optimized for time intelligence).
  * `discount` $\rightarrow$ **Decimal Number** (for accurate percentage math).
  * `unitPrice`, `freight` $\rightarrow$ **Fixed Decimal Number** (safeguarding currency calculations).
  * `orderID`, `employeeID`, `customerID` $\rightarrow$ **Whole Number or Text** (assigned for relationship keys).

### 2. Relational Schema Architecture (Star Schema)
The architecture completely avoids row-level filtering or transactional aggregation during the Power Query phase to preserve deep transactional granularity. Transactions are mapped directly through an optimized Star Schema:

```text
                ┌────────────────┐
                │   categories   │
                └───────┬────────┘
                        │ 1
                        │
                        │ *
  ┌──────────────┐      ┌───┴────────────┐      ┌──────────────┐
  │  customers   ├───┐  │    products    │  ┌───┤   shippers   │
  └──────────────┘   │  └───────┬────────┘  │  └──────────────┘
                     │          │ 1         │
                   1 │          │           │ 1
                     │          │ * │
                     │  ┌───────┴────────┐  │
                     └─*│ order_details  │*─┘
                        └───────▲────────┘
                                │ *
                                │
                                │ 1
                        ┌───────┴────────┐
                        │     orders     │
                        └───────▲────────┘
                     ┌──────────┴──────────┐
                   * │                     │ *
                     │                     │
   ┌─────────────────┴──┐               ┌──┴─────────────────┐
   │   FiscalCalendar   │               │     employees      │
   └────────────────────┘               └────────────────────┘
---

## 📈 Analytical Insights Summary

### 1. Financial & Product Performance
* **The System Footprint:** Processed **\$1.27M Net Revenue** across **830 total orders** and **51K units sold**. Gross revenue reached \$1.35M with **\$88.67K given away in discounts**.
* **Category Core:** Revenue remains heavily driven by three inventory spaces: **Beverages (\$268K)**, **Dairy Products (\$235K)**, and **Confections (\$167K)**.
* **The May Collapse Alert:** FY2015 demonstrated strong sales momentum between January and April (climbing past \$124K). However, a sudden **85% collapse down to \$18K in May** was flagged, identifying critical supply chain depletion or structural risk.

### 2. Shipping & Logistics Performance
* **Carrier Efficiencies:** Out of 830 total orders, **772 orders were delivered on time**, **37 were delayed**, and **21 remain unfulfilled**.
* **Carrier Operational Turnaround:** * **Federal Shipping:** Most efficient turnaround times, averaging **7–8 days** across 255 orders.
  * **Speedy Express:** Moderate delivery cycles, averaging **8–9 days** across 249 orders.
  * **United Package:** Slowest pipeline distribution, averaging **9 days** while handling the largest block of **326 orders**.

---

## 🚀 Future Technical Implementations

* **Predictive Machine Learning Integration:** Embed advanced predictive scripts (Python/R modeling or Azure ML) directly inside the Power BI data loop to automate sales forecasting and evaluate customer churn vulnerabilities.
* **Parametric What-If Scenarios:** Build dynamic DAX parameters allowing users to run complex real-time business simulations on pricing elasticity, freight adjustments, or carrier realignment strategies.
