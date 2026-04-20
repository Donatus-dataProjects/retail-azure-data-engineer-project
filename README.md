# 🛒 Retail Analytics — Azure Data Engineering Project

![Azure](https://img.shields.io/badge/Azure-0078D4?style=flat&logo=microsoft-azure&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=flat&logo=databricks&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=flat&logo=apache-spark&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-003366?style=flat&logo=delta&logoColor=white)
![Power BI](https://img.shields.io/badge/PowerBI-F2C811?style=flat&logo=power-bi&logoColor=black)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=github-actions&logoColor=white)

> End-to-end data pipeline built on Azure and Databricks using the **Medallion Architecture** (Bronze → Silver → Gold → Power BI).

# **Architecture**
<img width="1004" height="913" alt="architecturepng" src="https://github.com/user-attachments/assets/c4f1e675-2288-4e5f-be97-e5a60d00bfcb" />

---

## 📌 Project Overview

End-to-end data pipeline built on Azure and Databricks using the Medallion Architecture (Bronze → Silver → Gold). This project transforms raw, fragmented retail data into a high performance **Gold Layer** that serves as the Single Source of Truth, enabling the business to move from reactive reporting to proactive, data driven decision-making.

---

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Azure Data Lake Storage Gen2 | Cloud storage for all data layers |
| Azure SQL Database | Initial staging for **products**, **stores**, and **transactions** data |
| Azure Databricks (PySpark) | Transformations and pipeline |
| Delta Lake | ACID-compliant table format for Silver and Gold |
| GitHub | Version control and CI/CD |
| Power BI | Business intelligence and dashboards |

---

## 📂 Full Data Journey

### Source 1 Azure SQL → Bronze

Raw data stored in **Azure SQL Database**. Databricks read from SQL using the Azure Databricks Access Connector and saved the data as Parquet files into the Bronze layer of ADLS Gen2.

**Tables ingested via this method:**

| File | Description |
|------|-------------|
| `dbo.products.parquet` | Product catalogue with pricing |
| `dbo.stores.parquet` | Store locations and names |
| `dbo.transactions.parquet` | Sales transaction records |

---

### Source 2 — GitHub HTTP → Bronze

The customer dataset was hosted in a **GitHub repository**. ADF pulled it directly using a **COPY activity** with **HTTP source**  and saved it into the Bronze layer no manual download required.

**Table ingested via this method:**

| File | Description |
|------|-------------|
| `customers.parquet` | Customer profiles and registration dates |

---

## 🥉🥈🥇 Pipeline — Medallion Architecture Layers

| Layer | Description |
|-------|-------------|
| Bronze | Raw data ingested from source, no transformations |
| Silver | Cleaned, joined and cast data |
| Gold | Aggregated and ready for reporting |

---

### 🟠 Layer 01 — Bronze (Raw)

> Raw data stored exactly as received from source systems. No transformation, applied full auditability and reprocessing capability.

- `customers.parquet` — customer profiles
- `products.parquet` — product catalogue
- `stores.parquet` — store locations
- `transactions.parquet` — sales transactions

---

### ⚪ Layer 02 — Silver (Cleaned & Joined)

> Cleaned, deduplicated, type cast and joined data. All four tables merged into a single enriched dataset ready for analytical use.

- `dropDuplicates()` applied on all ID columns
- Column types cast: `int`, `double`, `date`
- Tables joined on `customer_id`, `product_id`, `store_id`
- `total_amount` column calculated: `quantity × price`
- Saved as **Delta format** to ADLS Silver layer

```python
from pyspark.sql.functions import col

df_silver = df_transaction_clean \
    .join(df_customer_clean, "customer_id", "left") \
    .join(df_product_clean, "product_id", "left") \
    .join(df_store_clean, "store_id", "left") \
    .withColumn("total_amount", col("quantity") * col("price"))

df_silver.write.mode("overwrite").format("delta") \
    .save("abfss://retail@retailstorageaccountdon.dfs.core.windows.net/silver/")
```

---

### 🟡 Layer 03 — Gold (Business Ready)

> Business-ready aggregated data. Single Source of Truth for Power BI. No SQL joins needed, analysts get everything in one enriched table instantly.

| Column | Description |
|--------|-------------|
| `transaction_date` | Date the transaction took place, used for time-based analysis and trends |
| `product_id` | Unique identifier for each product |
| `store_id` | Unique identifier for each store |
| `customer_id` | Unique identifier for each customer |
| `product_name` | Raw product name from source system |
| `category` | Raw product category from source system |
| `store_name` | Raw store name from source system |
| `location` | Raw store location from source system |
| `customer_full_name` | Full name of the customer enables VIP and loyalty analysis |
| `customer_city` | City where the customer is based enables geographic insights |
| `display_product_name` | Clean formatted product name ready for Power BI visuals |
| `display_category` | Clean formatted category name ready for Power BI visuals |
| `display_store_name` | Clean formatted store name ready for Power BI visuals |
| `display_store_location` | Clean formatted store location ready for Power BI visuals |
| `total_quantity_sold` | Total units sold per product/store/date combination |
| `total_sales_amount` | Total revenue aggregated per product/store/date and customer |
| `transaction_volume` | Total number of transactions — measures sales activity |

---

## 📊 Dashboard

Product Performance Dashboard built in Power BI connected to the Gold layer in ADLS.

**Why this is impactful for the Analyst:**

1. **Personalised Insights** By adding the Full Name and City, an analyst can now see which specific customers are "VIPs" in a certain city.
2. **Customer Tenure** By adding the Registration Date, you can calculate how long a customer has been with the brand vs. how much they are currently spending (Loyalty analysis).
3. **No More Joins** The analyst now has the Customer Name, Product Name, and Store Location all in one table. They can build a full dashboard in Power BI without writing a single line of SQL to join tables.

---

## 🗄️ Data Storage Locations

> All data lives in **Azure Data Lake Storage Gen2**. GitHub stores only the pipeline code no data files are committed to the repository.

| Layer | Format | ADLS Path |
|-------|--------|-----------|
| Bronze | Parquet | `abfss://retail@retailstorageaccountdon.dfs.core.windows.net/bronze/` |
| Silver | Delta | `abfss://retail@retailstorageaccountdon.dfs.core.windows.net/silver/` |
| Gold | Delta | `abfss://retail@retailstorageaccountdon.dfs.core.windows.net/gold/` |

---

## 📁 Repository Structure

```
retail-azure-data-engineer-project/
│
├── 📓 notebooks/
│   └── Silver&Gold_Layer.ipynb       ← cleaning, joining and gold aggregation
│
├── 🏗️ architecture/
│   └── architecture_diagram.html     ← pipeline diagram
│
├── ⚙️ .github/
│   └── workflows/
│       └── databricks notebook   ← CI/CD
│
├── 🔒 .gitignore
└── 📖 README.md
```

---

## 🚀 How to Run

### Prerequisites
- Azure account with ADLS Gen2 storage
- Azure Databricks workspace
- Azure SQL Database
- Power BI Desktop

### Steps

**1. Clone the repository**
```bash
git clone https://github.com/Donatus-dataProjects/retail-azure-data-engineer-project.git
```

**2. Open the notebook in Databricks**
```
Silver&Gold_Layer.ipynb
```

**3. Fill in your credentials using the widgets at the top of the notebook**
```python
dbutils.widgets.text("app_id",          "", "Azure App ID")
dbutils.widgets.text("dir_id",          "", "Azure Directory ID")
dbutils.widgets.text("secret",          "", "Azure Client Secret")
dbutils.widgets.text("storage_account", "", "Storage Account")
```

**4. Connect Power BI to your Gold layer**

I downloaded the Databricks Power BI connector and used its access token to connect directly to the Gold layer in ADLS to build the Power Bi report.

---

## 🏆 Portfolio Skills Demonstrated

| Skill | How It Was Applied |
|-------|--------------------|
| Multiple ingestion methods | COPY ACTIVITY + HTTP source +  SQL connector + ADLS Gen2 |
| Cloud storage | Azure Data Lake Storage Gen2 with Service Principal auth |
| Big data processing | PySpark on Azure Databricks cast, join, deduplicate |
| Medallion Architecture | Bronze → Silver → Gold → Power BI pipeline |
| Data modelling | Joins, type casting, deduplication, aggregations |
| Business Intelligence | Power BI dashboard on Gold layer |
| Version control & CI/CD | GitHub Actions pipeline — automated runs |
| Security best practices | `dbutils.widgets` for credentials — no hardcoded secrets |

---

## 💼 Executive Summary
### Retail Data Intelligence Platform

**Strategic Objective:**
To transform raw, fragmented retail data into a high performance Gold Layer that serves as the **Single Source of Truth.** This report enables the business to move from reactive reporting to proactive, data driven decision-making.

### 1. Engineering Excellence: The Foundation

I have implemented a modern Medallion Architecture using Azure Databricks and Unity Catalog to ensure data integrity:

- **Security & Governance:** By moving away from legacy mounts to Service Principal-based External Locations, I was able to secure sensitive retail data while maintaining full auditability.
- **Copied Raw data** (Bronze) wass automatically cleaned and standardized in the Silver layer before being enriched for business using Gold layer. This eliminates manual preparation and the risk of human error.

### 2. Analytical Impact: Business Value

The Gold Layer is now optimized to provide **ready-to-use** insights that drive revenue:

- **Operational Precision:** With data enriched with Customer Full Names, Product Categories, and Store Locations, stakeholders can now see not just how much was sold, but who bought it and where the highest growth is happening.
- **Financial Readiness:** Data is rounded to two decimal points, making it instantly compatible with financial audits and Power BI dashboards.

### 3. Strategic Conclusion

This platform reduces the **Time-to-Insight** from days to seconds. By providing a single, enriched table, I have enabled the business to identify **Hero Products** and **Underperforming Stores** in real-time, ensuring stakeholders can pivot the strategy faster.

---
**Power BI Dashboard**
<img width="1311" height="724" alt="dash" src="https://github.com/user-attachments/assets/b5a70b66-c66a-4f8a-8d22-ab42ebb8daea" />


## 👤 Author

**Donatus Victor** Azure Data Engineer

- 🐙 GitHub: [@Donatus-dataProjects](https://github.com/Donatus-dataProjects)
- 💼 LinkedIn: [Donatus Victor](https://www.linkedin.com/in/donatusvictor)

---

## 📄 License

Feel free to use and adapt this project.
