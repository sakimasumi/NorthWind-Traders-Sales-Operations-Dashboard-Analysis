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
