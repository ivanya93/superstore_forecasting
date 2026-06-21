# Superstore Project — Skills Roadmap
**Business Statistics & Insights | Executive Master in Business Analytics & AI**

The goal is to use the Superstore Sales dataset (9,994 transactions, 2014–2017) not just to complete the assignment, but to produce work that directly maps to the five skill areas in the target role. Each section below defines the analysis, the method, the business framing, and the free tool to use.

---

## Dataset Quick Reference

| Dimension | Detail |
|---|---|
| Rows | 9,994 orders |
| Date range | Jan 2014 – Dec 2017 |
| Segments | Consumer, Corporate, Home Office |
| Categories | Furniture, Office Supplies, Technology |
| Regions | East, West, Central, South |
| Key metrics | Sales, Profit, Quantity, Discount |

---

## Skill 1 — Marketing Mix Profitability & Budget Allocation

**Business question:** Where is value created and destroyed across products, segments, and discount levels?

### Analyses to run

**1.1 Profit Margin by Category & Sub-Category (Channel Profitability proxy)**
- Compute `Profit Margin = Profit / Sales` at sub-category level
- Identify the "budget sinkholes": Tables (-8.6%), Bookcases, Supplies all run negative margins
- Bar chart ranked by margin — this is your headline chart for leadership

**1.2 Discount Elasticity & Profitability Cliff**
- Create discount buckets: 0%, 1–20%, 21–40%, >40%
- For each bucket, compute mean profit margin and mean profit per order
- The data will show a cliff — at >40% discount, the average order generates a loss
- This is the core of a "budget reallocation" argument: stop discounting in low-margin categories

**1.3 Customer Acquisition Cost (CAC) Proxy**
- In the absence of ad spend data, use discount cost as a CAC proxy:
  `Discount Cost = (Sales / (1 - Discount)) * Discount`
- Compute total discount cost per Segment
- Compare against total profit generated per segment → implicit CAC vs. LTV ratio

**1.4 Customer Lifetime Value (CLV) Estimation**
- For each customer: total revenue, total orders, first/last order date, avg. order value
- Simple CLV = `Avg Order Value × Purchase Frequency × Customer Lifespan (years)`
- Segment by CLV quartile → who are the top 20% of customers driving 80% of profit?

**1.5 Incrementality framing (narrative)**
- Design a hypothetical A/B experiment: "What would happen if we removed discounts >20% on Tables?"
- Show the expected profit uplift using current margin data — this demonstrates incrementality thinking even without a live test

### Assignment mapping
- Covers Task 3 (Statistical Inference): correlation test between discount and profit
- Covers Task 4 (Recommendations): redesign discount policy, reallocate budget to high-CLV segments

### Free tool
- **Python (pandas, scipy, matplotlib/plotly)** for analysis
- **Looker Studio** (free, connected to BigQuery) for the margin waterfall and discount cliff charts — live queries, shareable link, no export needed

---

## Skill 2 — Forecasting & Predictive Analytics

**Business question:** What are sales and profit trends, and what can we expect next?

### Analyses to run

**2.1 Time Series Decomposition**
- Aggregate monthly sales from Jan 2014 to Dec 2017
- Decompose into trend, seasonality, residual using `statsmodels` STL decomposition
- Key finding to surface: Q4 spike (Nov–Dec) and Q1 dip — clear seasonal pattern for planning

**2.2 Sales Forecast (Prophet)**
- Use Facebook Prophet (free, pip install prophet) to forecast monthly sales for 2018
- Fit on 2014–2017 data, project 12 months forward
- Include uncertainty intervals — this demonstrates you understand forecast uncertainty, not just point estimates

**2.3 Category-level forecasting**
- Run separate forecasts for Technology, Furniture, Office Supplies
- Technology shows the strongest growth trend — quantify expected 2018 revenue by category

**2.4 Profit Prediction Model (optional, Vertex AI)**
- Features: Category, Sub-Category, Segment, Region, Discount, Quantity, Ship Mode
- Target: Profit (or Profit Margin)
- Train a Random Forest in Python or Vertex AI AutoML (GCP free trial credits)
- Feature importance will show Discount as the #1 driver of profit variance — strong business narrative

### Assignment mapping
- Directly covers Team 6 (Sales Trends & Business Growth)
- Task 3 (Statistical Inference): confidence intervals for future sales

### Free tool
- **Python + Prophet** (free) for time series forecasting
- **Vertex AI AutoML** (GCP $300 free trial) for the regression model if you want to reference GCP in your project
- **Looker Studio** for the forecast visualization — connect directly to BigQuery, build the confidence band chart as a time series with blend

---

## Skill 3 — Customer Segmentation & Clustering

**Business question:** Do the three predefined segments (Consumer, Corporate, Home Office) actually describe meaningfully different customer behaviors?

### Analyses to run

**3.1 RFM Analysis (Recency, Frequency, Monetary)**
- For each customer:
  - **Recency**: days since last order (relative to dataset end: Dec 2017)
  - **Frequency**: number of unique orders
  - **Monetary**: total profit generated
- Score each dimension 1–5, create RFM composite score
- This surfaces natural tiers: Champions, Loyal, At-Risk, Lost

**3.2 K-Means Clustering on RFM scores**
- Scale features, run k-means for k=2 to 8
- Use elbow method + silhouette score to select optimal k (likely 4 clusters)
- Compare discovered clusters against the predefined Segment labels
- Key finding: Corporate customers are not monolithic — they split into high-value frequent buyers vs. low-value discount seekers

**3.3 Behavioral profiling per cluster**
- For each cluster: avg order value, preferred category, avg discount, region distribution, ship mode preference
- This is the "actionable customer pattern" — each cluster gets a distinct engagement recommendation

**3.4 Segment profitability comparison**
- One-way ANOVA: does average profit per order differ significantly across Segment?
- Post-hoc Tukey HSD to identify which pairs differ
- Consumer segment likely has highest volume but lowest profit per order

### Assignment mapping
- Covers Team 4 (Customer Segments) directly
- Task 3 (Statistical Inference): ANOVA + post-hoc tests
- Task 4 (Recommendations): which segments to prioritize for marketing investment

### Free tool
- **Python scikit-learn** for k-means, silhouette scoring
- **Plotly** for interactive cluster scatter plots (RFM 3D scatter) — export as HTML or embed in notebook
- **Looker Studio** for the segment profitability dashboard — query the RFM table from BigQuery directly

---

## Skill 4 — Reporting & Business Insights

**Business question:** How do you turn statistical findings into a story a CFO would act on?

### Dashboard structure (Looker Studio — free, connected to BigQuery)

Build a single Looker Studio report with 5 pages. Each page queries a BigQuery table or view directly — no CSV uploads needed once the data is in BigQuery.

| Page | Chart type | BigQuery source | Key insight |
|---|---|---|---|
| Executive Summary | Scorecard tiles + sparklines | `mart_profitability` | Total sales $2.3M, profit $286K, 12.5% margin |
| Profitability Map | Treemap by Sub-Category | `mart_profitability` | Tables and Bookcases are destroying value |
| Discount Cliff | Scatter + trend line | `int_discount_analysis` | Profit collapses beyond 20% discount |
| Customer Segments | Bubble chart on RFM scores | `int_customer_rfm` | Top 20% of customers = 65%+ of profit |
| Sales Forecast | Time series + confidence band | `mart_forecast` | Q4 2018 projected peak, Technology leads growth |

**Why Looker Studio over Tableau here:** the BigQuery connector is native — you write a SQL query, Looker Studio renders it live. No exporting, no version mismatch, and the report URL is shareable as a portfolio link just like Tableau Public.

### Storytelling structure (for the written report)
Follow the Pyramid Principle: lead with the recommendation, then support with evidence.

- **Headline**: "Three discount policy changes would recover $X in annual profit"
- **Supporting evidence**: margin by sub-category, discount elasticity, CLV by segment
- **Call to action**: specific, quantified, time-bound

### CEO's three actions (final slide requirement)
1. Cap discounts at 20% for all Furniture sub-categories — saves ~$X in margin leakage annually
2. Redirect marketing investment toward Technology in the West region — highest CLV, strongest growth
3. Launch a retention programme for "At-Risk" Corporate customers identified in RFM analysis — they represent ~$X in recoverable revenue

### Free tool
- **Looker Studio** (free, shareable link — put it in your portfolio)
- Python **plotly** for in-notebook interactive charts
- The written report: structure as a consulting memo, not an academic paper

---

## Skill 5 — Process Improvement & Analytics Governance

**Business question:** How would a real analytics team manage this data pipeline?

### What to implement (and reference in your report)

**5.1 Data quality framework (Task 1 of the assignment)**
Document a formal data quality checklist:
- Completeness check: no nulls in key fields
- Consistency check: `Ship Date >= Order Date` always
- Outlier policy: flag orders where `|Profit Margin| > 100%` as anomalies
- Data dictionary: define every field, its type, and acceptable range

**5.2 DBT-style transformation layer (reference architecture)**
Even if you don't run DBT, design the transformation logic as if you would:
- `stg_orders`: raw CSV with type casting and null handling
- `int_customer_rfm`: RFM calculation per customer
- `mart_profitability`: margin metrics at category/segment/region grain

Include a diagram showing the DAG. This signals DBT literacy without needing a live Snowflake instance.

**5.3 Free Snowflake / BigQuery option**
- **Snowflake 30-day free trial**: load the CSV, run SQL for all aggregations, screenshot the query editor
- **BigQuery sandbox** (GCP free tier, no credit card for sandbox): 1TB/month free queries
  - Upload `Sample - Superstore.csv` to BigQuery
  - Run all analytical queries in SQL — this is real GCP experience you can reference in interviews

**5.4 Reproducibility**
- All analysis in a clean Jupyter notebook with markdown cells explaining each step
- Use `requirements.txt` (already exists in the repo)
- Structure notebook as: Data Prep → EDA → Statistical Tests → Clustering → Forecasting → Insights

### Assignment mapping
- Covers Task 1 (Data Preparation) — elevate it from "cleaning" to "governance"
- Demonstrates analytics engineering mindset, not just data science

---

## Recommended Tech Stack (Free Options)

| Role | Tool | Cost | Notes |
|---|---|---|---|
| Analysis & modelling | Python (pandas, scikit-learn, statsmodels, Prophet) | Free | Already set up in your repo |
| SQL & data warehouse | BigQuery Sandbox | Free | 1TB/month, no card required |
| Visualization & dashboards | **Looker Studio** | Free | Native BigQuery connector, shareable portfolio link |
| ML / AutoML | Vertex AI | Free trial ($300 GCP credits) | Use for profit prediction model |
| Transformation reference | dbt Core | Free (open source) | Reference architecture in report |
| Experiment design | Python (scipy A/B test simulation) | Free | For incrementality section |

**Workflow**: Python (clean + model) → BigQuery (store + query) → Looker Studio (visualize + present)

**Avoid for now**: Snowflake (30-day trial expires), Fivetran/Hightouch/Twilio Segment (no live data pipelines needed for this dataset), Tableau (requires Looker Studio now)

---

## Project Execution Order

1. **Week 1** — Data Preparation + DBT architecture diagram + BigQuery setup
2. **Week 2** — Descriptive stats + discount elasticity + CLV/CAC analysis
3. **Week 3** — Statistical inference (ANOVA, confidence intervals, chi-square) + RFM clustering
4. **Week 4** — Forecasting (Prophet) + Looker Studio dashboard
5. **Week 5** — Written report (consulting memo style) + CEO slide

---

## How This Maps to the Role

| Job Requirement | Project Deliverable |
|---|---|
| Multi-touch attribution / channel profitability | Discount elasticity analysis, CLV by segment |
| CLV modelling | RFM-based CLV per customer, segmented by cluster |
| CAC analysis | Discount cost as CAC proxy vs. segment profit |
| Incrementality testing | Hypothetical A/B experiment design on discount removal |
| Forecasting & statistical analysis | Prophet monthly forecast + regression model |
| Customer segmentation | K-means on RFM, comparison to predefined segments |
| Present findings to senior leadership | Tableau Public dashboard + consulting memo report |
| Analytics governance | Data quality framework + dbt reference architecture |
| GCP / BigQuery | BigQuery sandbox for SQL analysis |
| Looker Studio | 5-sheet dashboard on Looker Studio |
