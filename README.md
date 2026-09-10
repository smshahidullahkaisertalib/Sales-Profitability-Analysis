# 📊 Sales & Profitability Analysis — Power BI Dashboard

**End-to-end data analysis project** — from raw data audit to a fully modeled, two-page Power BI dashboard — analyzing an e-commerce company's revenue, cost, profit, and margin performance to uncover where profitability is truly being created or lost.

<!-- PLACEHOLDER: wide banner/cover image — assets/images/banner.png -->
Snaps/dashboard - page 1.JPG

<p align="left">
  <img alt="status" src="https://img.shields.io/badge/status-completed-brightgreen">
  <img alt="tool" src="https://img.shields.io/badge/tool-Power%20BI-yellow">
  <img alt="data" src="https://img.shields.io/badge/data-synthetic%20(BD%20context)-blue">
  <img alt="records" src="https://img.shields.io/badge/records-9%2C090%20transactions-informational">
  <img alt="license" src="https://img.shields.io/badge/license-MIT-lightgrey">
</p>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Business Problem](#-business-problem)
- [Objectives](#-objectives)
- [Stakeholders](#-stakeholders)
- [Dataset](#-dataset)
- [Data Audit](#-data-audit)
- [Data Cleaning & Preparation](#-data-cleaning--preparation)
- [Data Modeling — Star Schema](#-data-modeling--star-schema)
- [Tech Stack](#-tech-stack)
- [KPI Framework](#-kpi-framework)
- [Exploratory Data Analysis — Key Findings](#-exploratory-data-analysis--key-findings)
- [Dashboard](#-dashboard)
- [Key Business Findings](#-key-business-findings)
- [Management Recommendations](#-management-recommendations)
- [Repository Structure](#-repository-structure)
- [How to Use](#-how-to-use)
- [Final Conclusion](#-final-conclusion)
- [Author](#-author)

---

## 🧭 Overview

Although the company's revenue was growing YoY/MoM, profit margins were **not** increasing proportionally. Management had no clear visibility into which products, regions, or time periods actually drove profitability — and which ones, despite high sales volume, generated low margins or losses.

**Goal:** Build a data-driven dashboard that reveals the true picture of revenue, profit, and margin, and turn that picture into concrete recommendations for management.

> "Analyze the company's sales and profitability data to identify which products, regions, and time periods generate the most profit, and provide data-driven recommendations to management to improve overall profitability."

---

## ❗ Business Problem

Revenue growth was not translating proportionally into profit growth. Five sub-problems were investigated:

1. **Revenue vs. Profit Gap** — periods where revenue grew but margin declined, and why.
2. **Growth Trend Analysis** — MoM / YoY growth, slowdowns and spikes.
3. **Product Performance** — top vs. bottom (or loss-making) products.
4. **Region/Branch Performance** — most vs. least profitable regions.
5. **Actionable Recommendations** — turning findings into management decisions.

---

## 🎯 Objectives

- Measure overall sales & profitability performance
- Identify profitability gaps and trends
- Evaluate product profitability
- Evaluate regional/branch profitability
- Support data-driven management decisions

---

## 👥 Stakeholders

| Type | Stakeholders |
|---|---|
| **Primary** | Management / Business Leadership, Finance Team, Sales Team, Product/Category Managers, Regional/Branch Managers |
| **Secondary** | Operations / Supply Chain, Business/Data Analysts, Senior Executives |

---

## 🗂 Dataset

A **synthetic, Bangladesh-context e-commerce dataset** was used (no suitable real/public dataset was available; synthetic data avoided licensing/privacy issues and could be tailored to the analysis needs).

| Table | Type | Records | Purpose |
|---|---|---|---|
| Sales | Fact | 9,090 | Sales transactions |
| Customer | Dimension | 1,502 | Customer attributes |
| Date | Dimension | 1,097 | Date & calendar attributes |
| Product | Dimension | 7,714 | Product attributes |

---

## 🔎 Data Audit

A structured audit was run on all four source tables **before** any cleaning or modeling, to confirm structure, coverage, and reliability.

<!-- PLACEHOLDER: data audit summary screenshot — assets/images/data-audit-summary.png -->
![Data Audit Summary](assets/images/data-audit-summary.png)

| Audit Area | Result | Status |
|---|---|---|
| Sales | 261,007,335.30 BDT total revenue · 52,204 units sold · 9,662,948.66 BDT total profit (cost derived as Sales − Profit) | ✅ PASS |
| Product | 7,714 unique products across 7 categories, unique & complete IDs, full coverage in Sales | ✅ PASS |
| Customer | 1,502 unique customers, 2 segments (B2B/B2C), 8 divisions, 26 districts, unique & complete IDs | ✅ PASS |
| Date | Daily granularity, 01/01/2023–31/12/2025 (3 years), no missing/duplicate dates | ✅ PASS |
| Relationships | Sales → Customer / Product / Date — no orphan foreign keys | ✅ PASS |

| Data Quality Check | Result |
|---|---|
| Null values / duplicate rows / duplicate primary keys | None identified |
| Missing Customer IDs / Product IDs | None |
| Orphan foreign keys / date inconsistencies | None |
| Invalid values, negative/zero sales | Identified — deferred to the cleaning stage |

**Overall Audit Status: ✅ PASS** — structure, relationships, date/product/customer coverage, and core sales/profit calculations were confirmed sufficient for sales, profitability, customer, product, time-series, and geographic analysis.

---

## 🧹 Data Cleaning & Preparation

An original backup of the raw dataset was preserved separately for traceability before any cleaning was performed.

- **Missing values** — no nulls/blanks in Customer, Date, or Product; missing `Ship_Mode` values in Sales replaced with `"Unknown Category"`.
- **Derived fields** — `Delivery_Duration` calculated from relevant date fields; formatting inconsistencies and unknown values reviewed and resolved.
- **Cost & profit fields** — no reliable `Cost` field in the source data, so `Cost_BDT` was derived per the project's documented assumption, and `Profit_BDT` / `Profit_Margin_Pct` recalculated accordingly. *(Profitability figures should be read against this assumption rather than as directly reported accounting figures.)*
- **Data types & business rules** — reviewed and corrected; `Is_Return` confirmed as a consistent TRUE/FALSE indicator field.

<!-- PLACEHOLDER: before/after data cleaning screenshot — assets/images/data-cleaning-before-after.png -->
![Data Cleaning Before/After](assets/images/data-cleaning-before-after.png)

**Status: ✅ DATA CLEANING COMPLETED** — no nulls, blank/empty-string issues, or exact duplicates remained; all required calculated fields were in place; the fact table was analysis-ready with relationships and business validations preserved.

---

## ⭐ Data Modeling — Star Schema

The cleaned dataset was transformed into a **Star Schema**: a central `Fact_Sales` table (order, date, customer, and product keys, quantity, revenue, cost, profit, discount) supported by `Dim_Date`, `Dim_Product`, and `Dim_Customer`.

```
                 ┌───────────────┐
                 │   Dim_Date    │
                 └───────┬───────┘
                         │
┌───────────────┐   ┌────┴────────┐   ┌────────────────┐
│  Dim_Customer  ├───┤ Fact_Sales  ├───┤  Dim_Product    │
└───────────────┘   └─────────────┘   └────────────────┘
```

<!-- PLACEHOLDER: Power BI model view / ERD screenshot — assets/images/star-schema-model.png -->
![Star Schema Model](assets/images/star-schema-model.png)

- 1-to-many (1:*) relationships — each dimension filters `Fact_Sales`
- Primary/foreign keys verified, **no orphan foreign keys**, no unnecessary many-to-many relationships
- All relationships active with appropriate filter directions; the Date relationship separately validated

**Why a Star Schema:** keeps transactional and descriptive data clearly separated, simplifies DAX calculations, gives more reliable filtering behaviour, performs better analytically, and is easier to maintain and scale — letting management analyze sales and profitability across time, product, and customer dimensions without duplicating transactional data.

---

## 🛠 Tech Stack

- **Power BI** — data modeling (star schema), DAX measures, dashboard design
- **SQL** — joins across fact/dimension tables
- **Excel / CSV** — source dataset audit, cleaning, and preparation

---

## 📐 KPI Framework

Core KPIs were implemented as reusable **DAX measures**, validated across date, product, and regional filter contexts.

| KPI | Business Definition |
|---|---|
| Total Revenue | Total revenue generated from sales |
| Total Cost | Total cost associated with sold products |
| Total Profit | Revenue minus total cost |
| Profit Margin % | Profit as a percentage of revenue |
| Quantity Sold | Total number of units sold |
| Average Order Value (AOV) | Average revenue generated per unique order |
| MoM Growth % | Revenue growth vs. previous month |
| YoY Growth % | Revenue growth vs. same period last year |
| Profit Growth % | Profit growth vs. previous year |

Supporting measures: **Product Contribution %**, **Regional Contribution %**.

---

## 🔍 Exploratory Data Analysis — Key Findings

### Revenue & Profit Trend (2023–2025)

| Year | Revenue Growth (YoY) | Profit | Profit Growth | Profit Margin |
|---|---|---|---|---|
| 2023 | — | 89.16 lakh BDT | — | 9.08% |
| 2024 | -17.61% | 79.27 lakh BDT | -11.10% | 9.80% |
| 2025 | +20.00% | 88.84 lakh BDT | +12.08% | 9.15% |

> **Key insight:** 2024 → 2025 — Revenue ↑ 20.00%, Profit ↑ only 12.08%, Margin ↓ 0.65 pp. Revenue growth is **not** fully translating into profitability.

<!-- PLACEHOLDER: Revenue vs Profit trend line chart — assets/images/revenue-profit-trend.png -->
![Revenue vs Profit Trend](assets/images/revenue-profit-trend.png)

### Root Cause — Cost Pressure

- 2025 Revenue growth: **20.00%** | Cost growth: **20.80%** → Cost ratio 90.24% → 90.85%
- Discount stayed ~11% across 2023–2025 (broadly stable) — **not** the primary driver of margin compression

### Category Profitability

| Category | Revenue Share | Profit Share | Margin |
|---|---|---|---|
| Electronics | 70.41% | 55.73% | 7.37% |
| Garments | 7.98% | 16.56% | 19.33% |
| Beauty_Health | 2.89% | 5.22% | 16.82% |
| Footwear | 3.69% | 5.61% | 14.15% |
| Grocery_FMCG | 3.76% | 3.52% | 8.72% |

> Electronics drives **scale**, not **margin efficiency**. High revenue ≠ high profitability.

<!-- PLACEHOLDER: Category revenue vs margin chart — assets/images/category-profitability.png -->
![Category Profitability](assets/images/category-profitability.png)

### Regional Profitability

| Region | Revenue Share | Margin | Status |
|---|---|---|---|
| Dhaka | 40.56% | 9.33% | High volume / moderate margin |
| Chittagong | 22.89% | 8.84% | High revenue / lower margin |
| Khulna | 12.88% | 9.32% | Significant revenue, mid margin |
| Mymensingh | 3.77% | 7.95% | Low volume / lowest margin |
| Rajshahi | 5.70% | 10.99% | High-margin opportunity |
| Sylhet | 5.79% | 10.60% | High-margin opportunity |

<!-- PLACEHOLDER: Regional revenue/margin map or bar chart — assets/images/regional-performance.png -->
![Regional Performance](assets/images/regional-performance.png)

### May 2023 — Margin Deterioration Deep Dive

| Metric | Apr 2023 | May 2023 | Change |
|---|---|---|---|
| Revenue | 73.34 lakh | 71.48 lakh | -2.54% |
| Cost | 63.77 lakh | 66.31 lakh | +3.98% |
| Profit | 9.57 lakh | 5.17 lakh | **-45.93%** |
| Profit Margin | 13.05% | 7.24% | **-5.81 pp** |

> Discount actually *decreased* that month — the cause was an **inflexible cost base during a revenue dip**, not discounting.

---

## 📈 Dashboard

Two-page Power BI dashboard: **Executive Overview** and **Profitability Drivers & Customer Behaviour**.

### Page 1 — Executive Overview

<!-- PLACEHOLDER: Executive Overview dashboard page screenshot — assets/images/dashboard-executive-overview.png -->
![Executive Overview](assets/images/dashboard-executive-overview.png)

| KPI | Result |
|---|---|
| Total Revenue | 276.23M BDT |
| Total Profit | 25.73M BDT |
| Profit Margin | 9.31% |
| Sales Growth | 54.20% |
| Profit Growth | 9.80% |

- **Top performer:** Dhaka — leads in both revenue (112M BDT) and profit (10.45M BDT)
- **Fastest growth:** Chittagong — highest sales growth at 82.10%
- **High cost:** expenses tracked close to revenue, limiting profit growth to 9.80%

### Page 2 — Profitability Drivers & Customer Behaviour

<!-- PLACEHOLDER: Profitability Drivers & Customer Behaviour dashboard page screenshot — assets/images/dashboard-profitability-drivers.png -->
![Profitability Drivers & Customer Behaviour](assets/images/dashboard-profitability-drivers.png)

| KPI | Result |
|---|---|
| Average Order Value | 30,388 BDT |
| Average Discount | 10.61% |
| Return Rate | 5.58% |
| Return-related Profit Loss | 4.29M BDT |
| Average Delivery Days | 3 days |
| On-Time Delivery Rate | 80% |

**Customer segmentation:** B2C — 55.17% of profit (14.19M BDT) · B2B — 44.83% of profit (11.53M BDT)
**Shipping mode margin:** Same-Day 10.43% · Standard 9.60% · Express 8.84% · Unknown 6.38%
**Category profit contribution:** Garments (19.33%) and Beauty_Health (16.82%) yield the highest margins; Electronics drives the most revenue (194.5M BDT) but at a low 7.37% margin.

---

## 🔑 Key Business Findings

- 🔴 Sales growth is significantly ahead of profit growth (54.20% vs. 9.80%)
- 🔴 High revenue ≠ high profitability — Electronics highest revenue, lowest margin (7.37%); Garments highest margin (19.33%)
- 🟠 Cost remains a major profitability constraint across all regions
- 🟠 Returns have a measurable financial impact (~4.29M BDT profit loss)
- 🟡 Customer profitability is relatively balanced (B2C 55.17% / B2B 44.83%)
- 🟡 Profitability varies by shipping mode (up to ~1.6 pp spread)

---

## ✅ Management Recommendations

| Priority | Area | Recommended Direction |
|---|---|---|
| 1 — Critical | Cost | Improve cost & COGS efficiency |
| 2 — Critical | Product Mix | Improve Electronics margin + diversify mix |
| 3 — High | Region | Review Chittagong / Khulna / Mymensingh profitability |
| 4 — Opportunity | Region | Evaluate Rajshahi / Sylhet expansion |
| 5 — High | Product | Pricing/cost review for low-margin SKUs |
| 6 — Monitor | Discount | Targeted (not blanket) discount governance |
| 7 — Monitor | Returns | Investigate high-return categories |
| 8 — Monitor | Shipping | Review delivery-mode economics |

---

## 📁 Repository Structure

```
├── assets/
│   └── images/              # dashboard screenshots, charts, diagrams
├── data/
│   ├── raw/                  # original backup dataset
│   └── cleaned/               # analysis-ready star-schema tables
├── dashboard/                # .pbix Power BI file
├── docs/                     # full project report (audit → cleaning → modeling → EDA → dashboard)
└── README.md
```

---

## ▶️ How to Use

1. Clone the repository
2. Open `dashboard/*.pbix` in **Power BI Desktop**
3. Explore the two dashboard pages: **Executive Overview** and **Profitability Drivers & Customer Behaviour**
4. Full write-up available in `docs/` (data audit, cleaning, star-schema modeling, KPI framework, EDA, findings, recommendations)

---

## 🏁 Final Conclusion

The company is achieving revenue growth, but the **quality of that growth** needs improvement. Cost growth, heavy revenue concentration in low-margin Electronics, and uneven regional profitability are the core structural issues. The path forward: shift focus from **"more revenue"** to **"more profitable revenue"** — through cost efficiency, Electronics margin improvement, high-margin category growth, regional profitability optimization, and targeted discount/returns governance.

---

## 👤 Author

<!-- PLACEHOLDER: author photo / GitHub avatar — assets/images/author.png -->
**Talib**
*Data Analyst — Sales & Profitability Dashboard Project*

