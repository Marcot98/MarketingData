# MarketingData

Marketing Data Engineering Challenge
Overview

This project implements an end-to-end data pipeline for marketing ads spend data.
It covers ingestion, cleaning, loading into a SQL Server database, and modeling to compute marketing KPIs such as CTR, CPC, CPM, CPA, CVR, and ROAS.

Deliverables include:

n8n workflow for ingestion

SQL model to transform and calculate KPIs

Results screenshot showing KPI outputs

Loom video walkthrough

1. n8n Ingestion Workflow

The workflow Download, validate and Insert Ads_spend.json performs the following steps:

Schedule Trigger – runs every 25 minutes to fetch new data.

HTTP Request – downloads the latest CSV file from Google Drive.

Extract from File – parses the CSV into rows.

Validate data type – ensures numeric fields (spend, clicks, impressions, conversions) are properly cast.

Filter – drops records with missing required dimensions (platform, account, campaign, country, device).

Clean before Insert – deduplicates by keeping the best/latest version of each record.

Execute SQL Query – inserts into marketing.ads_spend using UPSERT logic (keeps the highest values for metrics).

Log Table Insert – records metadata (rows processed, file name) into etl_run_log.

🔗 To load the workflow in n8n:

Go to Workflows > Import from File

Upload Download, validate and Insert Ads_spend.json

2. SQL Model

The file DT_SQL_ADS.sql contains the SQL logic to transform the ingested data.
It includes:

Base fact table: marketing.ads_spend

Derived views with KPI calculations:

CTR = clicks / impressions

CPC = spend / clicks

CPM = (spend / impressions) * 1000

CPA = spend / conversions

CVR = conversions / clicks

ROAS = (revenue / spend) (or proxy if revenue not provided)

Comparisons over last 30 days vs previous 30 days

To reproduce locally: 
-- Create database
CREATE DATABASE marketing;

-- Switch to DB
USE marketing;

-- Run the full script
:r DT_SQL_ADS.sql

3. Results

After running the SQL transformations, the results are visible in the view
v_fact_ads_kpi_30d_compare, which compares current 30-day KPIs vs the previous 30 days.

📸 Example output:

4. Loom Video

A 5-minute Loom video is provided explaining:

Workflow design in n8n

SQL model structure

Key decisions for handling duplicates and ensuring data persistence

Interpretation of KPI outputs

URL Video: https://www.loom.com/share/7c90840de31649198d17724dc5f5a612?sid=716ef009-69bc-4642-aadf-977e0b417d89


Setup Instructions

Clone this repository

Import the n8n workflow (Download, validate and Insert Ads_spend.json)

Run the SQL script (DT_SQL_ADS.sql) in SQL Server

Query the view v_fact_ads_kpi_30d_compare to validate KPIs

With this, all deliverables are covered: ingestion workflow, SQL/dbt model, screenshot of results, and Loom video.