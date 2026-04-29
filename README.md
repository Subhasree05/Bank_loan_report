# 🏦 Bank Loan Analysis Dashboard
### End-to-End Data Analytics Project | Finance Domain | SQL + Power BI

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![SQL Server](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)
![Finance](https://img.shields.io/badge/Domain-Finance-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

---

## 📌 Project Overview

This project is a **comprehensive end-to-end Bank Loan Analysis** built using **Microsoft SQL Server** and **Power BI**. It simulates a real-world finance domain use case where a bank needs to monitor and evaluate its lending activities, loan performance, and borrower health metrics through interactive dashboards.

The goal is to help stakeholders make **data-driven decisions** by tracking key KPIs such as total loan applications, funded amounts, repayment status, interest rates, and debt-to-income ratios — all broken down by various dimensions like geography, loan purpose, employment length, and home ownership.

---

## 🎯 Business Problem

Financial institutions deal with massive volumes of loan data daily. Decision-makers need a quick, reliable way to:

- Monitor **loan portfolio health** in real time
- Distinguish **Good Loans** from **Bad Loans**
- Identify **regional and temporal trends** in lending
- Understand **borrower profiles** to assess credit risk
- Track **Month-over-Month (MoM)** and **Month-to-Date (MTD)** performance

This dashboard addresses all of the above in a single, unified reporting solution.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **MS SQL Server** | Data storage, querying, and validation |
| **SQL (T-SQL)** | Data extraction, KPI computation, aggregation |
| **Power BI Desktop** | Data modeling, DAX measures, and dashboard design |
| **Power Query** | Data transformation and cleaning |
| **DAX** | Custom measures, calculated columns, time intelligence |

---

## 📊 Dashboard Pages

### 1. 📈 Summary Dashboard
A high-level executive view with the most critical KPIs at a glance.

**Key Metrics Tracked:**
- **Total Loan Applications** — with MTD and MoM change
- **Total Funded Amount** — cumulative lending volume
- **Total Amount Received** — actual repayments collected
- **Average Interest Rate** — portfolio-wide average, MTD tracked
- **Average Debt-to-Income (DTI) Ratio** — borrower financial health indicator

**Good Loan vs Bad Loan Analysis:**

| Category | Metrics |
|----------|---------|
| ✅ Good Loans | Applications %, Funded Amount, Amount Received |
| ❌ Bad Loans | Applications %, Funded Amount, Amount Received |

**Loan Status Grid View** — a tabular breakdown by loan status (Fully Paid, Current, Charged Off) showing all key KPIs per category.

---

### 2. 🗺️ Overview Dashboard
Visual trends and breakdowns across multiple dimensions.

| Visual | Insight |
|--------|---------|
| 📅 Monthly Trend (Line Chart) | Seasonal patterns in loan issuance over time |
| 🌍 Regional Analysis (Filled Map) | State-wise lending distribution across the US |
| 🍩 Loan Term Analysis (Donut Chart) | 36-month vs 60-month loan split |
| 👷 Employee Length Analysis (Bar Chart) | Loan volume by borrower employment history |
| 🎯 Loan Purpose Breakdown (Bar Chart) | Debt consolidation, credit card, home improvement, etc. |
| 🏠 Home Ownership Analysis (Treemap) | Distribution across Rent, Own, Mortgage |

---

### 3. 📋 Details Dashboard
A granular, drill-through view for loan-level data. Provides a comprehensive grid with all relevant fields per loan record — ideal for analysts who need to investigate individual accounts.

---

## 🗄️ SQL Queries — KPI Validation

All Power BI metrics were **independently validated using SQL** before being published. Below are the key query categories written:

```sql
-- Total Loan Applications
SELECT COUNT(id) AS Total_Loan_Applications FROM bank_loan_data;

-- MTD Loan Applications
SELECT COUNT(id) AS MTD_Total_Loan_Applications 
FROM bank_loan_data
WHERE MONTH(issue_date) = 12 AND YEAR(issue_date) = 2021;

-- Total Funded Amount
SELECT SUM(loan_amount) AS Total_Funded_Amount FROM bank_loan_data;

-- Average Interest Rate
SELECT ROUND(AVG(int_rate) * 100, 4) AS Avg_Interest_Rate FROM bank_loan_data;

-- Average DTI
SELECT ROUND(AVG(dti) * 100, 4) AS Avg_DTI FROM bank_loan_data;

-- Good Loan Percentage
SELECT
  (COUNT(CASE WHEN loan_status IN ('Fully Paid', 'Current') THEN id END) * 100.0)
  / COUNT(id) AS Good_Loan_Percentage
FROM bank_loan_data;

-- Loan Status Summary Grid
SELECT
  loan_status,
  COUNT(id) AS Total_Applications,
  SUM(loan_amount) AS Total_Funded_Amount,
  SUM(total_payment) AS Total_Received_Amount,
  AVG(int_rate * 100) AS Avg_Interest_Rate,
  AVG(dti * 100) AS Avg_DTI
FROM bank_loan_data
GROUP BY loan_status;
```

> ✅ All Power BI visuals were cross-verified against SQL outputs to ensure 100% accuracy.

---
mermaid
flowchart TD
    A[📄 Raw CSV Data] --> B[🗄️ MS SQL Server\nDatabase Creation & Import]
    B --> C[🔍 SQL Queries\nKPI Validation & Business Logic]
    C --> D[📊 Power BI\nConnect via DirectQuery / Import]
    D --> E[⚙️ Power Query\nData Cleaning & Transformation]
    E --> F[🔗 Data Modeling\nDate Table & Relationships]
    F --> G[📐 DAX Measures\nKPIs, MTD, MoM, YTD]
    G --> H[🎨 Dashboard Design\nSummary → Overview → Details]
    H --> I[🎛️ Navigation & Interactivity\nSlicers, Bookmarks, Buttons]

---

## 📐 Data Model

The data model follows a **Star Schema** design:

- **Fact Table:** `bank_loan_data` — core loan records with all financial fields
- **Dimension Table:** `Date Table` — created in Power BI using DAX for time intelligence
- Relationships established on `issue_date` for accurate MTD/MoM calculations

---

## 📏 Key DAX Measures

```dax
-- Total Loan Applications
Total Loan Applications = COUNT(bank_loan_data[id])

-- MTD Loan Applications
MTD Loan Applications = TOTALMTD(COUNT(bank_loan_data[id]), 'Date Table'[Date])

-- MoM Change %
MoM Loan Applications = 
  ([MTD Loan Applications] - [PMTD Loan Applications]) 
  / [PMTD Loan Applications]

-- Good Loan %
Good Loan % = 
  DIVIDE(
    CALCULATE(COUNT(bank_loan_data[id]),
      bank_loan_data[Good vs Bad Loan] = "Good Loan"),
    COUNT(bank_loan_data[id])
  )

-- Average Interest Rate
Avg Interest Rate = AVERAGE(bank_loan_data[int_rate])
```

---

## 🧹 Data Cleaning Steps

The following transformations were applied in **Power Query**:

- Removed null and duplicate records
- Standardized date formats for `issue_date`, `last_payment_date`, `earliest_cr_line`
- Corrected data types (text → numeric, text → date)
- Derived a custom `Good vs Bad Loan` column based on `loan_status`
- Created a standalone **Date Table** in Power BI for proper time intelligence

---

## 📈 Key Insights Uncovered

- 🔵 **~86%** of all loans were classified as **Good Loans** (Fully Paid or Current)
- 🔴 **~14%** were **Bad Loans** (Charged Off), representing significant credit risk
- 📅 Loan applications peaked in **Q4**, indicating seasonal borrowing trends
- 🌎 States like **CA, NY, and TX** recorded the highest loan volumes
- 💳 **Debt Consolidation** was the single largest loan purpose category
- ⏳ Borrowers with **10+ years** of employment had the highest application rates
- 🏠 **Mortgage holders** represented the dominant home ownership segment

---

## ✨ Features & Highlights

- 🎨 **Custom dashboard background** designed for professional aesthetics
- 🔁 **Page navigation buttons** for seamless switching between Summary, Overview, and Details views
- 🎛️ **Interactive slicers** — filter by State, Loan Grade, Loan Purpose, and more
- 📊 **Field Parameters** — dynamic axis switching on trend charts
- 🔍 **Drill-through** — from summary KPIs down to individual loan records
- ✅ **SQL-validated KPIs** — every number in Power BI is cross-checked against SQL

---

## 🚀 How to Run This Project

1. **Clone or download** this repository
2. **Set up MS SQL Server** and create a new database
3. **Import** the `financial_loan.csv` file into SQL Server
4. **Run** the SQL scripts from the `/SQL Queries` folder to validate KPIs
5. **Open** the `.pbix` file in Power BI Desktop
6. **Update the connection string** to point to your local SQL Server instance
7. **Refresh the data** and explore the dashboards

---

## 📚 Domain Knowledge

**Loan Terminology used in this project:**

| Term | Definition |
|------|-----------|
| **DTI (Debt-to-Income)** | Ratio of monthly debt payments to monthly gross income |
| **Charged Off** | Loan declared a loss after prolonged non-payment |
| **Fully Paid** | Loan where borrower has met all repayment obligations |
| **Current** | Loan that is within payment terms and on schedule |
| **Loan Grade** | Credit risk rating assigned by the lender (A–G) |
| **Interest Rate** | Annual percentage charged on the outstanding loan amount |
| **MTD** | Month-to-Date — cumulative value from the start of the current month |
| **MoM** | Month-over-Month — percentage change vs previous month |

---

## 🙋‍♂️ About This Project

This project was built by following the **Bank Loan Analysis** tutorial by [Data Tutorials](https://www.youtube.com/@DataTutorials1) on YouTube. It covers an end-to-end data analytics workflow — from raw data ingestion in SQL Server to a fully interactive Power BI dashboard — simulating a real-world finance domain scenario.

---

## 📬 Connect With Me

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/your-profile)
[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/your-username)

---

> ⭐ If you found this project helpful or insightful, feel free to **star** this repository!
