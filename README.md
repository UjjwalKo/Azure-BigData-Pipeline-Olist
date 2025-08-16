# Azure BigData Pipeline of Olist 
This project is an big data pipeline built on Microsoft Azure that processes the Olist Brazilian e-commerce dataset. It uses Azure Data Factory to orchestrate data ingestion from multiple sources like HTTP, SQL, and NoSQL (MongoDB), stores raw and processed data in Azure Data Lake Storage, and leverages Azure Databricks with PySpark for scalable data transformation following a Medallion architecture. The transformed data is then made available for analytics through Azure Synapse, with optional Power BI visualization. This pipeline showcases real-world data engineering practices and tools used to build scalable, production-ready big data solutions on Azure.

![Relationship Between Tables](HRhd2Y0.png)
