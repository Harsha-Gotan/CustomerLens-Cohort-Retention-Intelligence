# CustomerLens: Cohort & Retention Intelligence

> **End-to-end customer analytics project** analysing 685,639 real e-commerce transactions to uncover retention patterns, segment customers by lifetime value, and identify churn risk — built with Python and Tableau.

---

## 📌 Live Dashboard

🔗 **[View on Tableau Public](#)** ← https://public.tableau.com/app/profile/harsha.gotan/viz/CustomerLensCohortRetentionIntelligence/CustomerSegmentationChurnAnalysis?publish=yes

---

## 📁 Repository Structure

```
CustomerLens/
│
├── data/
│   └── README.md                          ← Raw data download instructions
│
├── notebooks/
│   └── CustomerLens_Analysis.ipynb        ← Full Python workflow
│
├── output/
│   ├── CustomerLens_Cleaned.csv           ← Cleaned & enriched dataset
│   ├── CustomerLens_Customer_Level.csv    ← RFM, CLTV, Churn per customer
│   └── CustomerLens_Transaction_Level.csv ← Cohort-level transaction data
│
├── dashboard/
│   └── CustomerLens.twbx                  ← Packaged Tableau workbook
│
├── assets/
│   ├── dashboard1_preview.png             ← Screenshot of Dashboard 1
│   └── dashboard2_preview.png             ← Screenshot of Dashboard 2
│
└── README.md
```

---
## Dashboard Preview

<img width="1597" height="897" alt="Screenshot 2026-06-17 230408" src="https://github.com/user-attachments/assets/94f20db3-b2af-4d8a-8bff-66792ac2495b" />

<img width="1602" height="897" alt="Screenshot 2026-06-17 230328" src="https://github.com/user-attachments/assets/7f64a105-2303-4480-ae99-158f1ed8376d" />

---

## 🧩 Business Problem

In e-commerce, acquiring a customer is only half the battle. The real challenge is understanding:

- **Who keeps coming back** - and why?
- **When do customers leave** - and at what point in their journey?
- **Who drives the most revenue** - and are we at risk of losing them?
- **Which customer segments deserve immediate attention** - before it is too late?

Without answers to these questions, businesses make acquisition decisions blindly, waste retention budgets on the wrong customers, and fail to act on early churn signals.

**CustomerLens** addresses this by building a complete customer intelligence system - from raw transaction data to actionable segmentation - using a real UK retail dataset spanning 2 full years.

---

## 🎯 Project Objectives

1. Build a **cohort retention heatmap** to track customer behaviour from first purchase across 24 months
2. Identify which acquisition cohorts generate the most **long-term revenue**
3. Segment all customers using **RFM methodology** into actionable tiers
4. Calculate **Customer Lifetime Value (CLTV)** and break it down by demographics
5. Quantify **churn** and identify the revenue currently at risk
6. Deliver two **interactive Tableau dashboards** that a business stakeholder can use directly

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **Python 3** | Data loading, cleaning, feature engineering, RFM scoring |
| **Pandas** | Data manipulation and aggregation |
| **NumPy** | Numerical operations and synthetic data generation |
| **Jupyter Notebook** | Development environment and reproducible workflow |
| **Tableau Public** | Interactive dashboard development and publishing |
| **GitHub** | Version control and portfolio hosting |

---

## 📦 Dataset

**Source:** UCI Online Retail II Dataset  
**Available at:** [kaggle.com/datasets/mashlyn/online-retail-ii-uci](https://kaggle.com/datasets/mashlyn/online-retail-ii-uci)

| Attribute | Detail |
|---|---|
| Scope | UK transactions only |
| Period | January 2010 - December 2011 |
| Raw rows | 1,048,575 |
| After cleaning | 685,639 |
| Unique customers | 5,252 |
| Unique transactions | 31,656 |
| Avg purchases per customer | ~6 |

**Original columns:**

| Column | Description |
|---|---|
| Invoice | Transaction ID - prefixed 'C' for cancellations |
| StockCode | Product identifier |
| Description | Product name |
| Quantity | Units purchased |
| InvoiceDate | Date and time of transaction |
| Price | Unit price in GBP |
| Customer ID | Unique customer identifier |
| Country | Customer country |

**Enriched columns added:**

| Column | Description |
|---|---|
| Gender | Synthetically generated per customer (Male / Female) |
| Age | Synthetically generated from realistic age distribution |
| Age_Band | Derived from Age — 18-25, 26-35, 36-45, 46-55, 56+ |
| Revenue | Quantity × Price per transaction |

> **Note on Demographics:** The UCI dataset does not include demographic data. Gender and Age were synthetically generated per unique Customer ID using a fixed random seed (42) for full reproducibility. Transaction behaviour, dates, and revenue figures are entirely real.

---

## ⚙️ Python Workflow

The full workflow lives in `notebooks/CustomerLens_Analysis.ipynb` and runs in five stages:

### Stage 1 — Data Loading & Filtering
- Loaded full 83MB CSV with `low_memory=False`
- Filtered to UK only and 2010–2011 date range
- Removed missing Customer IDs, cancellations (Invoice starting with 'C'), negative quantities and zero prices
- Result: 685,639 clean rows

### Stage 2 — Data Cleaning
- Converted InvoiceDate and Birthdate to datetime using `dayfirst=True`
- Filled missing Gender values with 'Unknown'
- Added Revenue column (Quantity × Price)

### Stage 3 — Demographic Enrichment
- Generated synthetic Gender (Male/Female) and Age (18–70) per unique Customer ID
- Derived Age_Band categories
- Merged demographics back into main dataframe

### Stage 4 — Feature Engineering

| Feature | Formula | Purpose |
|---|---|---|
| Cohort_Month | Min(InvoiceDate) per Customer ID → Period('M') | Assigns each customer to their acquisition cohort |
| Invoice_Month | InvoiceDate → Period('M') | Month of each transaction |
| Cohort_Index | Invoice_Month - Cohort_Month | Months elapsed since first purchase (0–23) |
| Recency | Reference date - Last purchase date (days) | How recently a customer purchased |
| Frequency | Count of unique invoices per customer | How often a customer purchases |
| Monetary | Sum of Revenue per customer | Total spend per customer |
| CLTV | Monetary × Frequency | Customer Lifetime Value proxy |
| Churn_Flag | 1 if Recency > 90 days, else 0 | Binary churn indicator |

### Stage 5 — RFM Scoring & Segmentation
- Scored each customer 1-4 on Recency, Frequency and Monetary using quartile-based `pd.qcut()`
- Applied business logic to assign one of 7 segment labels
- Exported two CSVs: Customer Level (5,252 rows) and Transaction Level (685,639 rows)

---

## 📊 Dashboard Walkthrough

### Dashboard 1 — Cohort & Retention Intelligence
*Analysing customer acquisition cohorts and revenue retention patterns across 24 months | Jan 2010 - Dec 2011*

**KPI Banner**
| KPI | Value |
|---|---|
| Total Customers | 5,252 |
| Avg Purchases / Customer | 6.027 |
| Churn Rate | 50.8% |
| Avg CLTV | £90,852 |

**Visual 1 — Cohort Retention Heatmap**  
A 24×24 matrix where rows represent acquisition cohort months and columns represent months since first purchase (Cohort Index 0-23). Colour intensity shows how many customers from each cohort are still active at each point in time. Darker red = higher retention. The classic triangle shape confirms healthy repeat purchase behaviour in early cohort indexes, tapering as expected over time.

**Visual 2 — Cohort Revenue Heatmap**  
Same grid structure as the retention heatmap but values represent total revenue generated by each cohort at each cohort index. This answers not just "did customers return?" but "how much were those returning customers worth?" — a distinction most portfolios miss.

**Visual 3 — Avg Order Value by Cohort Month**  
A bar chart showing the average transaction value for customers acquired in each month. Reveals that the September 2010 cohort had the highest average order value (£28), suggesting a high-value acquisition period worth analysing further.

---

### Dashboard 2 — Customer Segmentation & Churn Analysis
*Segmenting 5,252 customers by RFM behaviour, lifetime value, and churn patterns across demographics | Jan 2010 - Dec 2011*

**KPI Banner**
| KPI | Value |
|---|---|
| Churn Rate | 50.8% |
| Avg CLTV | £90,852 |
| Champion % | 30.9% |
| Revenue at Risk | £2,889,272 |

**Visual 4 — RFM Bubble Chart**  
A scatter plot with Recency on the X axis, Frequency on the Y axis, bubble size representing Monetary value, and colour representing RFM Segment. Champions cluster in the top-left (low recency, high frequency). The "Cannot Lose Them" segment is visible further right - historically frequent buyers who have gone quiet.

**Visual 5 — Churn Timeline**  
A dual-axis chart with bars showing churned customer count by month and a line overlay showing the trend. A sharp spike in December 2010 followed by a dip reveals post-holiday churn patterns - a classic retail phenomenon.

**Visual 6 — CLTV by Age Band and Gender**  
A grouped bar chart showing average CLTV split by age band and gender. The 26–35 Female segment significantly outperforms all other groups, providing a clear targeting insight for retention campaigns.

**Visual 7 — RFM Segment Treemap**  
A treemap showing the proportional distribution of all 7 RFM segments by customer count. Champions occupy the largest block at 31%, with Potential Loyalists second at 19%.

---

## 📈 Key Findings

| Finding | Detail |
|---|---|
| 🔴 High churn rate | 50.8% of customers have not purchased in 90+ days |
| 💰 Revenue at risk | £2,889,272 tied to churned customer base |
| 🏆 Strong champion base | 31% of customers are Champions driving majority of revenue |
| ⚠️ Cannot Lose Them segment | 623 high-frequency customers going quiet - urgent win-back needed |
| 💎 Highest value demographic | 26-35 Female segment has highest average CLTV |
| 📅 Seasonal churn spike | December 2010 saw sharpest churn spike - post-holiday drop-off |
| 📊 Best retention cohort | January 2010 cohort showed strongest long-term retention |
| 🛒 Avg 6 purchases | Healthy repeat behaviour confirms dataset suitability for cohort analysis |

---

## 💡 Business Recommendations

**1. Immediate Win-Back Campaign — Cannot Lose Them Segment**  
623 customers with proven high purchase frequency have gone quiet. A personalised re-engagement campaign with targeted offers could recover a significant portion of the £2.89M revenue at risk. Priority: High.

**2. Champion Loyalty Programme**  
31% of customers are Champions — invest in a formal loyalty or rewards programme to maintain this segment. Losing even 10% of Champions would have a disproportionate revenue impact.

**3. Replicate January 2010 Acquisition Strategy**  
The Jan 2010 cohort showed the strongest long-term retention. Analysing what acquisition channels or promotions drove that cohort could inform future marketing spend.

**4. Target 26–35 Female Segment for Upsell**  
This demographic generates nearly 4x the CLTV of comparable male customers. Targeted product recommendations and premium tier offers could further increase their already high lifetime value.

**5. Post-Holiday Retention Programme**  
The December 2010 churn spike suggests customers acquired during holiday promotions do not return. A structured January re-engagement sequence could convert seasonal buyers into repeat customers.

**6. Accelerate New Customer Conversion**  
310 customers are in the New Customer segment with only one purchase. A second-purchase incentive within 30 days of first purchase could significantly improve cohort retention at Index 1 — the most critical drop-off point.

---

## 🔢 RFM Segment Summary

| Segment | Count | % | Business Priority |
|---|---|---|---|
| Champion | 1,623 | 31% | Retain and reward |
| Potential Loyalist | 996 | 19% | Nurture into Champions |
| Loyal | 697 | 13% | Keep engaged |
| Lost | 688 | 13% | Low-cost re-engagement |
| Cannot Lose Them | 623 | 12% | Immediate win-back |
| At Risk | 315 | 6% | Targeted offers now |
| New Customer | 310 | 6% | Second purchase incentive |

---

## 🚀 How to Run This Project

### Steps
1. Download the raw dataset from [Kaggle](https://kaggle.com/datasets/mashlyn/online-retail-ii-uci)
2. Place `online_retail_II.csv` in the `data/` folder
3. Open `notebooks/CustomerLens_Analysis.ipynb`
4. Run all cells — outputs will be saved to `output/`
5. Open `dashboard/CustomerLens.twbx` in Tableau Desktop
6. Or view the live version on [Tableau Public](#)

---

## 📝 Methodology Notes

- **Churn Definition:** A customer is defined as churned if their last purchase was more than 90 days before the dataset end date (December 2011). This threshold is standard in retail analytics and can be adjusted based on business context.
- **CLTV Formula:** CLTV = Monetary × Frequency. This is a simplified historical CLTV proxy. A predictive CLTV model would incorporate average customer lifespan and discount rate.
- **RFM Scoring:** Quartile-based scoring (1–4) using `pd.qcut()`. Frequency uses rank-based cutting to handle ties.
- **Synthetic Demographics:** Gender and Age are synthetically generated for portfolio demonstration purposes. All transaction behaviour is real.
- **Reference Date:** Recency is calculated relative to December 5, 2011 (one day after the last transaction in the dataset).

