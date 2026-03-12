# 🇰🇪 The Rise of Digital Payments in Kenya
### An End-to-End Data Analysis of Mobile Money & Card Transactions (2007–2026)

**Author:** Benard Musyoka Mwinzi &nbsp;|&nbsp; **Date:** March 2026 &nbsp;|&nbsp; **Data Source:** Central Bank of Kenya (CBK)

[![Tableau Dashboard](https://img.shields.io/badge/Tableau-Live%20Dashboard-006633?style=for-the-badge&logo=tableau&logoColor=white)](https://public.tableau.com/app/profile/benard.mwinzi/viz/DigitalPaymentsDashboard/Dashboard)
[![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-F37626?style=for-the-badge&logo=jupyter&logoColor=white)](https://jupyter.org/)


---

## 📌 Table of Contents

1. [Business Problem](#1-business-problem)
2. [Project Objectives](#2-project-objectives)
3. [Datasets](#3-datasets)
4. [Tools & Technologies](#4-tools--technologies)
5. [Project Architecture](#5-project-architecture)
6. [Part 1 — Data Cleaning & Feature Engineering](#6-part-1--data-cleaning--feature-engineering)
7. [Part 2 — Exploratory Data Analysis](#7-part-2--exploratory-data-analysis)
8. [Part 3 — Forecasting](#8-part-3--forecasting)
9. [Part 4 — SQL Analytics](#9-part-4--sql-analytics)
10. [Part 5 — Tableau Dashboard](#10-part-5--tableau-dashboard)
11. [Key Findings & Insights](#11-key-findings--insights)
12. [Repository Structure](#12-repository-structure)

---

## 1. Business Problem

Kenya is one of the world's most closely studied success stories in financial inclusion. The launch of M-Pesa in 2007 fundamentally transformed how millions of Kenyans access financial services — enabling peer-to-peer transfers, bill payments, and savings through a basic mobile phone.

Yet despite this transformation, there had been **no consolidated analytical view** of how the two dominant digital payment channels — mobile money and card transactions — have evolved together over nearly two decades. Specifically, the following questions remained unanswered:

- How fast has Kenya's mobile money ecosystem actually grown, and what is its current scale?
- Are card transactions growing too, or is mobile money cannibalising card usage?
- How did COVID-19 and the CBK fee waiver affect each payment channel differently?
- Is Kenya genuinely moving away from cash, or are ATM withdrawals still dominant?
- Which months drive peak payment volumes, and how consistent is the seasonality?
- Where are both channels headed in the next 24 months?

These are not academic questions. They have direct implications for financial institutions, payment infrastructure providers, regulators, and any business that needs to understand where and how Kenyan consumers transact.

---

## 2. Project Objectives

This project delivers an end-to-end analysis that:

1. **Cleans and consolidates** 18+ years of CBK payments data into a single analysis-ready dataset
2. **Uncovers trends and patterns** across mobile money and card transaction channels
3. **Quantifies the COVID-19 effect** on each channel using a structured comparison framework
4. **Forecasts 24 months** of future transaction volumes using machine learning
5. **Answers business questions** through 10 purpose-built SQL analytical queries
6. **Communicates findings** through an interactive Tableau Public dashboard accessible to both technical and non-technical audiences

---

## 3. Datasets

Both datasets were sourced directly from the **Central Bank of Kenya (CBK) National Payments System Statistics** — the official regulator and sole publisher of this data.

| Dataset | Period | Rows | Key Variables |
|---|---|---|---|
| Mobile Payments | Mar 2007 – Jan 2026 | 227 | Active agents, registered accounts, transaction volume & value |
| Card Transactions | Jul 2009 – Jan 2026 | 199 | ATM transactions by card type (debit, credit, prepaid, charge), POS transactions |

**Combined merged dataset:** 227 rows × 22 columns covering the full 2007–2026 period, with a 199-row overlap period where both datasets are available simultaneously.

---

## 4. Tools & Technologies

| Tool | Purpose |
|---|---|
| **Python (pandas, numpy)** | Data loading, cleaning, feature engineering, merging |
| **Matplotlib, Seaborn** | Exploratory data visualisation (8 analytical charts) |
| **Scikit-learn** | Polynomial regression forecasting model |
| **MySQL** | Relational database storage and analytical querying |
| **Tableau Public** | Interactive dashboard for business communication |
| **Jupyter Notebooks** | Documented, reproducible analysis workflow |

---

## 5. Project Architecture

```
Raw CBK Data (2 CSVs)
        │
        ▼
┌─────────────────────────┐
│  Notebook 1             │
│  Data Cleaning &        │  → 3 clean CSVs (mobile, cards, merged)
│  Feature Engineering    │
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  Notebook 2             │
│  Exploratory Data       │  → 8 analytical charts & summary findings
│  Analysis               │
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  Notebook 3             │
│  Forecasting            │  → 24-month forecasts with confidence intervals
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  MySQL Loader Script    │
│  + SQL Queries          │  → 3 database tables, 10 analytical queries
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  Tableau Public         │
│  Dashboard              │  → 6 interactive sheets, live at Tableau Public
└─────────────────────────┘
```

---

## 6. Part 1 — Data Cleaning & Feature Engineering

**Notebook:** `Digital_Payments_Data_Cleaning.ipynb`

### What Was Done

Both raw CSVs arrived with non-standardised column names, numeric values stored as strings with comma formatting, no date column, and no derived analytical features. The cleaning pipeline addressed each issue systematically.

**Steps applied to both datasets:**
- Renamed all columns to `snake_case` for consistency
- Constructed a proper `date` column from separate year and month integer fields
- Converted all numeric columns from string to float, stripping comma separators
- Validated missing values and documented the one expected null (first-row MoM growth rate)

### Engineered Features

Six derived features were created to enable richer analysis:

**Mobile Payments:**

| Feature | Formula | Analytical Purpose |
|---|---|---|
| `agents_per_million_accounts` | `active_agents / registered_accounts_millions` | Measures agent network density — how many accounts each agent serves |
| `avg_transaction_value_ksh` | `(value_billions × 1B) / (volume_millions × 1M)` | Average KSh value per transaction — tracks whether transaction sizes are growing |
| `mobile_volume_mom_pct` | `pct_change(cash_in_out_volume_millions)` | Month-on-month growth rate to identify acceleration and slowdown periods |

**Card Transactions:**

| Feature | Formula | Analytical Purpose |
|---|---|---|
| `atm_total` | `prepaid + charge + credit + debit ATM` | Total ATM withdrawals across all card types |
| `pos_share_pct` | `pos_total / total_card_transactions × 100` | POS as % of card transactions — the key indicator of cash vs digital shift |
| `card_txn_mom_pct` | `pct_change(total_card_transactions)` | Month-on-month growth rate for card transactions |

**Merge flags:**

| Feature | Logic | Purpose |
|---|---|---|
| `covid_period` | `date between 2020-03-01 and 2020-09-30` | Boolean flag for the CBK fee waiver period |
| `card_data_available` | `total_card_transactions is not null` | Identifies the 28 mobile-only rows before card data starts (pre-Jul 2009) |

### Merge Strategy

The two datasets were merged using an **outer join on the date field**, preserving all 227 rows of mobile data while appending card columns where available. The 28 mobile-only rows (Mar 2007 – Jun 2009) contain nulls in all card columns — handled correctly throughout all subsequent analysis.

### Output

```
✅ Clean files exported:
   cleaned_mobile.csv   → 227 rows × 10 columns
   cleaned_cards.csv    → 199 rows × 13 columns
   cleaned_merged.csv   → 227 rows × 22 columns
```

---

## 7. Part 2 — Exploratory Data Analysis

**Notebook:** `Digital_Payments_EDA_Visualizations.ipynb`

Eight analytical charts were produced, each designed to answer a specific business question.

### Chart 1 — Mobile Money Growth: Accounts & Agents

**Question:** How fast have mobile money accounts and the agent network grown since 2007?

**Finding:** Registered accounts and active agents have grown in near-perfect lockstep throughout the entire period — confirming that agent network expansion is the primary driver of account adoption. The COVID-19 fee waiver in March 2020 produced a visible acceleration in account registrations as more Kenyans adopted mobile money for the first time to avoid handling physical cash.

---

### Chart 2 — Mobile Money Transaction Volume & Value

**Question:** What is the monetary scale of Kenya's mobile money ecosystem?

**Finding:** Monthly transaction volume grew from under 0.5 million in 2007 to over 215 million in 2026 — a **500× increase over 18 years**. The value of these transactions grew even faster in relative terms, reflecting rising average transaction sizes as M-Pesa expanded beyond small peer-to-peer transfers into bill payments, business transactions, and government disbursements.

---

### Chart 3 — Card Transaction Composition: POS vs ATM

**Question:** How is the composition of card usage changing over time?

**Finding:** In 2009, ATM cash withdrawals dominated card usage almost entirely. Over the subsequent 16 years, POS transactions grew dramatically both in absolute volume and as a share of total card transactions. By 2024, POS accounted for over 40% of all card transactions — a structural shift away from using cards purely as cash-access tools toward using them as payment instruments at the point of sale.

---

### Chart 4 — POS Share of Card Transactions Over Time

**Question:** Is Kenya genuinely moving away from cash?

**Finding:** The POS share of card transactions rose from approximately 4–8% in 2009 to 42%+ by 2024 — an upward trend that has been consistent across all economic conditions including the COVID-19 period. This is one of the strongest indicators of financial deepening in the dataset: Kenyans are increasingly using cards to pay for goods and services rather than to withdraw cash.

---

### Chart 5 — COVID-19 Impact (2019–2022 Zoom)

**Question:** How did the pandemic affect each payment channel?

**Finding:** The pandemic produced **sharply divergent effects** on the two channels:

- **Card transactions** fell significantly when lockdowns reduced physical retail activity and consumer confidence collapsed
- **Mobile money transactions** actually increased following the CBK fee waiver of March 2020, which eliminated transaction charges for transfers under KSh 1,000 and raised wallet limits — incentivising a shift to digital peer-to-peer transfers and away from physical cash handling

This divergence is one of the most analytically significant findings of the project: the same external shock pushed one channel down and the other up.

---

### Chart 6 — Seasonality: Average Transactions by Month

**Question:** Which months drive peak payment volumes?

**Finding:** Both channels show a clear and consistent **December peak**, driven by festive spending, salary bonuses, school-related transactions, and end-of-year business settlements. January and February are consistently the slowest months across both channels and across all years — a pattern that has remained stable since 2009.

---

### Chart 7 — Year-over-Year Growth Rates

**Question:** When did growth accelerate, stabilise, or decline?

**Finding:** Mobile money showed explosive early growth (2007–2012) driven by rapid adoption from a near-zero base. Card transactions grew more steadily throughout the period. Both channels show the COVID dip in 2020 followed by strong recovery. Mobile money growth has moderated from its explosive early rates but remains consistently positive — suggesting a maturing but still-expanding ecosystem.

---

### Chart 8 — Correlation Heatmap

**Question:** How are the key payment metrics related to each other?

**Finding:** Mobile money volume, registered accounts, and active agents are all correlated at above 0.99 with each other — confirming that these three metrics effectively move as one system. Card transactions show moderate correlation with mobile metrics, reflecting that both channels are growing but driven by fundamentally different forces (agent network expansion vs. merchant infrastructure investment).

### Summary of EDA Findings

| Finding | Evidence |
|---|---|
| Mobile money has scaled 500× since 2007 | Volume: 0.02M → 215M monthly transactions |
| POS is replacing ATM as the dominant card channel | POS share: ~4% (2009) → 42%+ (2024) |
| COVID-19 diverged the two channels | Cards fell; mobile accelerated due to CBK fee waiver |
| December is consistently the peak month | Both channels show strong and consistent festive-season spikes |
| The mobile ecosystem metrics are tightly linked | Accounts, agents & volume all correlate at above 0.99 |

---

## 8. Part 3 — Forecasting

**Notebook:** `Notebook_03_Forecasting.ipynb`

### Methodology

A supervised machine learning approach was used to forecast 24 months of future transaction volumes for four metrics: mobile transaction volume, registered accounts, total card transactions, and POS transactions.

**Model:** Polynomial Regression (degree = 3) combined with seasonal encoding

**Features used:**
- Time index (months since start)
- Sine and cosine encoding of month-of-year (captures 12-month seasonality)
- COVID binary flag (Mar–Sep 2020)
- Polynomial interactions between features

**Train/test split:** Last 12 months held out as a holdout evaluation set

### Model Performance

| Metric | Mobile Volume | Registered Accounts | Card Transactions | POS Transactions |
|---|---|---|---|---|
| MAPE (holdout) | **2.14%** | 1.87% | 53.14% | 48.32% |
| R² (holdout) | 0.97 | 0.98 | 0.61 | 0.58 |

The mobile money forecasts achieved excellent accuracy (MAPE of 2.14%) due to the strong and consistent trend structure of the series. Card transaction forecasts are less precise due to higher month-to-month volatility and the structural disruption caused by COVID-19, which the polynomial model cannot fully capture.

### Forecast Outputs

All forecasts include ±1.5 × residual standard deviation confidence intervals. Forecast CSVs were exported for integration into the Tableau dashboard.

---

## 9. Part 4 — SQL Analytics

**Script:** `05_analytical_queries.sql`

Ten SQL queries were written against the `kenya_payments` MySQL database to answer specific business questions using window functions, CTEs, and aggregations.

| Query | Type | Business Question |
|---|---|---|
| 1. Monthly snapshot | Basic aggregation | What are the latest mobile and card volumes? |
| 2. YoY growth | Window function (`LAG`) | How does growth compare year on year? |
| 3. COVID comparison | CASE + GROUP BY | What was the average impact of COVID on each channel? |
| 4. POS share trend | Rolling average | Is POS share consistently rising? |
| 5. Seasonality index | GROUP BY month | Which months are above average? |
| 6. Agent efficiency | Ratio calculation | How has agent productivity changed over time? |
| 7. Mobile milestones | Subquery | When did mobile money cross key volume thresholds? |
| 8. Card composition | PIVOT-style | What share does each card type hold at ATMs? |
| 9. Volatility analysis | STDDEV window | Which periods had the most erratic growth? |
| 10. Growth index | CTE + ratio | How do mobile and card growth compare from a common base? |

---

## 10. Part 5 — Tableau Dashboard

**Live Dashboard:** [View on Tableau Public](https://public.tableau.com/app/profile/benard.mwinzi/viz/DigitalPaymentsDashboard/Dashboard)

The interactive dashboard consolidates all analytical findings into a single view accessible to non-technical stakeholders.

### Dashboard Preview

![Kenya Digital Payments Dashboard](Dashboard__1_.png)

### Dashboard Features

**Interactive Controls:**
- Year band filter (2007–2010 | 2011–2015 | 2016–2020 | 2021–2026) — filters all 6 sheets simultaneously
- Channel dropdown filter
- COVID Period annotation and highlight action

**KPI Tiles (live calculated):**
- 90.41M Registered Mobile Accounts
- 475,079 Active Agents
- 28.2M Card Transactions
- 32% POS Share

### The 6 Dashboard Sheets

| Sheet | Chart Type | Key Insight Communicated |
|---|---|---|
| 1. Mobile Money Growth | Dual-axis line | 18-year growth trajectory of accounts and transaction volume |
| 2. Card Composition | Stacked area | The structural shift from ATM to POS in card usage |
| 3. POS Share Trend | Line + trend line | Kenya's long-run move away from cash dependency |
| 4. COVID-19 Impact | Grouped bar | Divergent channel effects of the 2020 fee waiver policy |
| 5. Seasonality Heatmap | Highlight table | December peak and January trough across all years |
| 6. Growth Index | Dual-axis line | Mobile money has grown 12× faster than card transactions since July 2009 |

---

## 11. Key Findings & Insights

### Finding 1 — Mobile Money Has Grown 500× Since 2007
Monthly transaction volume rose from 0.02 million in March 2007 to over 215 million by January 2026. This is not incremental growth — it represents a fundamental transformation of how Kenyans transact. The growth has been driven by three mutually reinforcing forces: agent network expansion, rising smartphone penetration, and deliberate regulatory support from the CBK.

### Finding 2 — Kenya Is Structurally Moving Away From Cash
POS transactions as a share of total card transactions rose from approximately 4% in 2009 to over 42% by 2024. Combined with the explosion in mobile money volume, the data clearly shows that Kenyans are using digital channels for an increasing proportion of everyday payments — not just for cash access.

### Finding 3 — COVID-19 Diverged the Two Channels
The CBK fee waiver of March 2020 created a natural experiment: card transactions fell sharply while mobile money accelerated. Pre-COVID average card transactions were 6.54 million per month. During the COVID period this dropped to 4.92 million — a decline of 25%. Mobile money volume, by contrast, increased slightly from 152.35M to 148.44M before recovering strongly to 174.32M post-COVID.

### Finding 4 — The Agent Network Is the Backbone of Mobile Money
The 0.99+ correlation between active agents, registered accounts, and transaction volume confirms that physical agent expansion directly enables digital adoption. Every 1% increase in active agents corresponds to a near-equivalent increase in registered accounts and transaction volume. This has major implications for rural financial inclusion strategy.

### Finding 5 — Mobile Money Is Growing 12× Faster Than Card Transactions
Rebasing both series to July 2009 = 100, the Growth Index shows that by 2026 mobile money had grown to an index of approximately 1,200+ while card transactions reached approximately 400–500. Mobile money is not just larger in absolute terms — it is growing structurally faster, suggesting continued dominance of the payments landscape for the foreseeable future.

### Finding 6 — December Is the Consistent Peak Month
Across every year from 2007 to 2026, December is the highest-volume month for both channels. January and February are consistently the slowest. This seasonal pattern is remarkably stable and has clear implications for payment infrastructure capacity planning.

---

## 12. Repository Structure

```
kenya-digital-payments/
│
├── data/
│   ├── raw/
│   │   ├── Mobile_Payments.csv
│   │   └── Number_of_Transactions.csv
│   ├── clean/
│   │   ├── cleaned_mobile.csv
│   │   ├── cleaned_cards.csv
│   │   └── cleaned_merged.csv
│   └── outputs/
│       ├── forecasts/
│       │   ├── mobile_money_volume_forecast.csv
│       │   ├── registered_accounts_forecast.csv
│       │   ├── total_card_transactions_forecast.csv
│       │   └── pos_transactions_forecast.csv
│       └── charts/
│           ├── 01_mobile_growth_accounts_agents.png
│           ├── 02_mobile_volume_value_trends.png
│           ├── 03_card_composition_pos_vs_atm.png
│           ├── 04_pos_share_trend.png
│           ├── 05_covid_impact.png
│           ├── 06_seasonality.png
│           ├── 07_yoy_growth_rates.png
│           └── 08_correlation_heatmap.png
│
├── notebooks/
│   ├── Digital_Payments_Data_Cleaning.ipynb
│   ├── Digital_Payments_EDA_Visualizations.ipynb
│   └── Notebook_03_Forecasting.ipynb
│
├── sql/
│   ├── 04_mysql_loader.py
│   └── 05_analytical_queries.sql
│
├── dashboard/
│   └── Dashboard_Preview.png
│
└── README.md
```

---

## Skills Demonstrated

| Skill Area | Specific Techniques Used |
|---|---|
| **Data Wrangling** | Multi-dataset merge, type conversion, null handling, snake_case standardisation |
| **Feature Engineering** | Ratio features, growth rates, binary flags, date decomposition |
| **Exploratory Analysis** | Time series analysis, correlation matrices, seasonality decomposition, YoY growth |
| **Statistical Modelling** | Polynomial regression, seasonal encoding (sin/cos), holdout evaluation, MAPE/R² |
| **SQL** | Window functions (LAG, STDDEV), CTEs, CASE statements, GROUP BY aggregation |
| **Data Visualisation** | Dual-axis charts, stacked areas, heatmaps, growth indices, annotated time series |
| **Dashboard Design** | KPI tiles, interactive filters, cross-sheet actions, dark theme, CBK branding |
| **Business Communication** | Finding-first structure, plain-English insights, stakeholder-ready dashboard |
| **Domain Knowledge** | Kenya payments landscape, M-Pesa ecosystem, CBK regulatory context, COVID policy impact |

---

## Data Source & Attribution

All data sourced from the **Central Bank of Kenya (CBK) — National Payments System Statistics**.
CBK is the official regulator and sole publisher of Kenya's national payments data.
Website: [www.centralbank.go.ke](https://www.centralbank.go.ke)

---

*Benard Musyoka Mwinzi — Data Analyst*
*Portfolio: [datascienceportfol.io/Benard](https://www.datascienceportfol.io/Benard)*
*Tableau Public: [public.tableau.com/profile/benard.mwinzi](https://public.tableau.com/app/profile/benard.mwinzi)*
