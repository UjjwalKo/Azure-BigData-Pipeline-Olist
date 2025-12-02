# Azure BigData Pipeline - Olist Project Documentation

## Overview
This project demonstrates end-to-end implementation of a scalable big data pipeline for the Olist Brazilian e-commerce dataset using Microsoft Azure services. The pipeline implement ingestion from multiple relational/NoSQL sources, cloud data lake staging, scalable transformation, and load to ADLS gen 2, all orchestrated with production-grade tools.

---

## Table of Contents
1. [Project Purpose & Learning Goals](#project-purpose--learning-goals)
2. [Solution Architecture](#solution-architecture)
3. [Data Flow & Components](#data-flow--components)
4. [Detailed Scripts & Notebooks Explanation](#detailed-scripts--notebooks-explanation)
5. [Azure Services & Technologies Used](#azure-services--technologies-used)
6. [Assumptions about Data](#assumptions-about-data)
7. [Entity Relationships & Visuals](#entity-relationships--visuals)
8. [Key Learnings & Challenges](#key-learnings--challenges)

---

## 1. Project Purpose & Learning Goals
- **Purpose:** Implement a data pipeline using Azure for a real-world dataset.
- **Learning Focus:**
    - Azure Storage (ADLS Gen2), Data Factory, Databricks, Synapse.
    - Connecting/ingesting data from SQL (MySQL), NoSQL (MongoDB), and HTTP sources.
    - Building Medallion Architecture (Bronze/Silver layers) for scalable transformation.
    - Data orchestration and securing credentials/automation.

---

## 2. Solution Architecture
- **Ingestion:**
    - Use of scripts/notebooks to load data from MySQL and MongoDB (see `DataIngestionToMySQL.ipynb` and `DataIngestionToMangoDB.ipynb`).
    - Raw data from various sources is uploaded to Azure Data Lake Storage (ADLS) Gen2, in `bronze` containers.
- **Transformation:**
    - Azure Databricks PySpark jobs read from ADLS, perform transformations, create Silver datasets.
    - Table joins, data cleaning, type handling, enrichment from NoSQL, and writing back to ADLS.
- **Analytics Output:**
    - Refined data is exposed for analytics via Azure Synapse.

**Reference:** `README.md` and [Entity Relationship Diagram](HRhd2Y0.png)

---

## 3. Data Flow & Components
- **Source Data Files:**
  - All datasets are in the `/data/` folder (listed in `input.json`).
    - `olist_customers_dataset.csv`
    - `olist_geolocation_dataset.csv`
    - `olist_order_items_dataset.csv`
    - `olist_order_payments_dataset.csv`
    - `olist_order_reviews_dataset.csv`
    - `olist_orders_dataset.csv`
    - `olist_products_dataset.csv`
    - `olist_sellers_dataset.csv`
    - `product_category_name_translation.csv`
- **Ingestion Adapters:**
  - **MySQL:** Bulk upload via pandas/MySQL connector (see notebook for type mapping and batching).
  - **MongoDB:** Category reference data loaded and inserted in bulk.
- **ADLS Staging:**
  - `/bronze/` (raw, ingested data)
  - `/silver/` (transformed, high-quality data)
- **Transformations in Databricks:**
  - Reading CSVs from ADLS, inferring schema.
  - Reformatting dates, dropping duplicates, joining tables, calculating derived fields (delivery times).
  - Enriching with NoSQL (MongoDB) product category data.
  - Writing unified fact/dimension tables in Parquet format to `silver`.

---

## 4. Detailed Scripts & Notebooks Explanation

### 4.1 DataIngestionToMySQL.ipynb
- **Purpose:** Load `olist_order_payments_dataset.csv` into a MySQL database table securely.
- **Highlights:**
    - Uses pandas and `mysql-connector-python`.
    - Dynamically maps datatypes, handles data in batches for large files.
    - Automates table creation and data insertion based on CSV schema.
    - Parameters and credentials are read from environment variables for security.

### 4.2 DataIngestionToMangoDB.ipynb
- **Purpose:** Uploads `product_category_name_translation.csv` into a MongoDB collection.
- **Highlights:**
    - Uses pandas and `pymongo`.
    - Handles errors for missing files and connection issues.
    - Inserts entire CSV as a document batch.
    - Credentials managed by dotenv and environment variables.

### 4.3 azure-databricks-olist.ipynb
- **Purpose:** Orchestrates data transformation using Azure Databricks and PySpark.
- **Highlights:**
    - Configures secure connection to ADLS Gen2 using service credentials and application identity.
    - Loads multiple datasets from `/bronze` container as Spark DataFrames.
    - Integrates additional dimension reference from MongoDB (NoSQL).
    - Cleans and reformats date fields, calculates delivery/delay durations.
    - Joins multiple fact/dimension tables.
    - Drops duplicate columns from wide joins.
    - Writes out processed, analytics-ready data to `/silver` container in Parquet format.

---

## 5. Azure Services & Technologies Used
- **Azure Data Lake Storage Gen2:**
  - Secure, scalable raw/processed storage and staging for cloud-scale analytics.
- **Azure Databricks:**
  - Managed Apache Spark (PySpark) workspace for transformation and machine learning.
- **Azure Data Factory:**
  - Orchestration of data flows (notebooks/scripts can plug into pipeline executions).
- **Azure Synapse:**
  - Unified analytics, data modeling, and connection to Power BI for visualization.
- **Other Technologies:**
  - MySQL, MongoDB, pandas, PySpark, python-dotenv, Parquet.

---

- Sensitive connection info is never hard-coded: use `.env` files and environment variables for DB credentials, API keys, etc.

---

---

## 7. Key Learnings & Challenges
- Practices for loading large datasets into MySQL and MongoDB from Python.
- Secure connection handling for both SQL and NoSQL DBs as well as for cloud storage.
- Setting up and authenticating ADLS Gen2 and Databricks Spark jobs robustly using service principal.
- Implementation of Medallion architecture for scalable, modular analytics.
- Data enrichment from multiple sources and complex DataFrame joins.
- Automating large-batch operations and handling schema evolution.
- End-to-end data movement (source to analytics-ready tables) using only cloud-native and open source tooling.

---

## References
- Raw/processed data: `/data/` and ADLS containers (`bronze/`, `silver/`, `gold/`).
