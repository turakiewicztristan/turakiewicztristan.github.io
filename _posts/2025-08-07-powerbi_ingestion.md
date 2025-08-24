---
title: "Ingestion PowerBI"
date: 2025-08-07
last_modified_at: 2025-08-07
categories:
  - Projects
tags:
  - Airflow
  - PowerBI
  - Data Engineering
  - Ingestion
---

# 📊 Ingesting Power BI Metrics with Airflow, GCS, and BigQuery

## 🎯 Context
In this project, I needed to analyze **Power BI usage metrics** (number of users, views, etc.) to monitor dashboard performance.  

To achieve this, I built an automated pipeline that:  
- calls the **Power BI API** to fetch raw metrics,  
- stores them securely in **Google Cloud Storage (GCS)**, partitioned by date,  
- and loads them into **BigQuery** for further analysis and integration with other datasets.  

---

## 🛠️ Tech Stack
- **Airflow** – Orchestration and scheduling of the pipeline  
- **Power BI API** – Source of usage metrics (users, views, etc.)  
- **Google Cloud Storage (GCS)** – Partitioned raw storage by date (historization and resiliency)  
- **BigQuery (GCP)** – Data warehouse for querying and analysis  
- **Python** – API calls, transformations, and operators  

---

## ⚙️ Pipeline Architecture

1. **Extraction**  
   - Fetch data from the **Power BI API** using an Airflow PythonOperator.  
   - Save results in JSON format to GCS, partitioned by date (`gs://<BUCKET>/powerbi_metrics/dt=YYYY-MM-DD/`).  

2. **Storage & Resiliency**  
   - Raw files are kept in GCS to allow **rebuilding tables in case of issues**.  

3. **Loading**  
   - Data is ingested from GCS to **BigQuery**.  
   - Tables are partitioned by date for cost optimization and query performance.  

4. **Analysis**  
   - Query the data in **BigQuery**.  
   - Combine with other datasets to enrich insights.  

---

## 📂 Example Airflow DAG

⚠️ Sensitive variables (tokens, project IDs, bucket names) are replaced with **placeholders** (`<PROJECT_ID>`, `<DATASET>`, `<TABLE>`, `<ACCESS_TOKEN>`).

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.providers.google.cloud.transfers.gcs_to_bigquery import GCSToBigQueryOperator
from airflow.utils.dates import days_ago
import requests
import json
from datetime import datetime

def extract_powerbi_metrics(**kwargs):
    today = datetime.today().strftime("%Y-%m-%d")
    url = "https://api.powerbi.com/v1.0/myorg/admin/activityevents"
    
    headers = {"Authorization": "Bearer <ACCESS_TOKEN>"}
    
    response = requests.get(url, headers=headers)
    data = response.json()
    
    # Save to local (upload to GCS in real pipeline)
    with open("/tmp/data.json", "w") as f:
        json.dump(data, f)

default_args = {"owner": "me", "start_date": days_ago(1)}

with DAG(
    dag_id="pipeline_powerbi_ingestion",
    default_args=default_args,
    schedule_interval="@daily",
    catchup=False,
) as dag:

    extract = PythonOperator(
        task_id="extract_powerbi_metrics",
        python_callable=extract_powerbi_metrics,
    )

    load_to_bq = GCSToBigQueryOperator(
        task_id="load_to_bq",
        bucket="<MY_BUCKET>",
        source_objects=["powerbi_metrics/dt={{ ds }}/data.json"],
        destination_project_dataset_table="<PROJECT_ID>.<DATASET>.<TABLE>",
        source_format="NEWLINE_DELIMITED_JSON",
        autodetect=True,
        write_disposition="WRITE_APPEND",
    )

    extract >> load_to_bq



```mermaid
flowchart LR
    A[Power BI API] --> B[Airflow DAG]
    B --> C[GCS<br>(partitioned by date)]
    C --> D[BigQuery<br>(partitioned tables)]
    D --> E[Analysis / Dashboards]
