# 🛒 Retail Analytics — Azure Data Engineering Project

![Azure](https://img.shields.io/badge/Azure-0078D4?style=flat&logo=microsoft-azure&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=flat&logo=databricks&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=flat&logo=apache-spark&logoColor=white)
![Power BI](https://img.shields.io/badge/PowerBI-F2C811?style=flat&logo=power-bi&logoColor=black)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-003366?style=flat&logo=delta&logoColor=white)
## Project Overview
End-to-end data pipeline built on Azure and Databricks
using the Medallion Architecture (Bronze → Silver → Gold).
## Architecture
Raw Data → Azure Data Lake Storage (ADLS Gen2)
         → Databricks (PySpark transformations)
         → Delta Lake (Bronze / Silver / Gold layers)
         → Power BI (Product Performance Dashboard)
## Tech Stack
- Azure Data Lake Storage Gen2
- Azure Databricks (PySpark)
- Delta Lake
- Power BI
## Pipeline
| Layer  | Description                          |
|--------|--------------------------------------|
| Bronze | Raw data ingested from source        |
| Silver | Cleaned, joined and cast data        |
| Gold   | Aggregated and ready for reporting   |
## Dashboard
Product Performance Dashboard built in Power BI
connected to the Gold layer in ADLS.
Why is this impactful for the Analyst:
1.	Personalised Insights: By adding the Full Name and City, an analyst can now see which specific customers are "VIPs" in a certain city.
2.	Customer Tenure: By adding the Registration Date, you can calculate how long a customer has been with the brand vs. how much they are currently spending (Loyalty analysis).
3.	No More Joins: The analyst now has the Customer Name, Product Name, and Store Location all in one table. They can build a full dashboard in Power BI without writing a single line of SQL to join tables.
To calculate the time between registration and purchase, we use the datediff function. This is a brilliant addition for your report because it reveals customer loyalty and speed of conversion.
Executive Summary: Retail Data Intelligence Platform
Strategic Objective:
To transform raw, fragmented retail data into a high-performance Gold Layer that serves as the **Single Source of Truth.** This platform enables the business to move from reactive reporting to proactive, data-driven decision-making.
1. Engineering Excellence: The Foundation
I have implemented a modern Medallion Architecture using Azure Databricks and Unity Catalog to ensure data integrity:
• Security & Governance: By moving away from legacy mounts to Service Principal-based External Locations, we have secured our sensitive retail data while maintaining full auditability.
•	Automated Pipeline: Raw data (Bronze) is automatically cleaned and standardized (Silver) before being enriched for business use (Gold). This eliminates manual preparation and the risk of human error.

2. Analytical Impact: Business Value
The Gold Layer is now optimized to provide **ready-to-use** insights that drive revenue:
•	Operational Precision: With data enriched with Customer Full Names, Product Categories, and Store Locations, stakeholders can now see not just how much was sold, but who bought it and where the highest growth is happening.
•	Financial Readiness: Data is rounded to two decimal points and grouped by Time Dimensions (Month/Year), making it instantly compatible with financial audits and Power BI dashboards.

3. Strategic Conclusion
This platform reduces the **Time-to-Insight** from days to seconds. By providing a single, enriched table, we have enabled the business to identify Hero Products and Underperforming Stores in real-time, ensuring we can pivot our strategy faster than the competition.



## Author
**Donatus** — Azure Data Engineer
- GitHub: [@Donatus-dataProjects](https://github.com/Donatus-dataProjects)
- LinkedIn: [(https://www.linkedin.com/in/donatusvictor)]

## License
MIT License — feel free to use and adapt this project
