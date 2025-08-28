---
title: "Realtime Data Ingestion with IBM StreamSets and watsonx.data"
date: 2025-08-27
slug: "realtime-data-ingestion-ibm-streamsets-watsonx-data"
categories: ["Data & AI", "Data Ingestion", "Tutorials"]
tags: ["IBM StreamSets", "watsonx.data", "Apache Iceberg", "Lakehouse", "IBM Cloud Object Storage"]
summary: "Learn how to build a realtime data ingestion pipeline using IBM StreamSets and watsonx.data. Step-by-step tutorial with architecture diagram, code snippets, and explanations for beginners."
draft: true
series: ["watsonx.data Series"]
series_order: 2
---

{{< badge >}}
Tutorial
{{< /badge >}}

## Introduction

In today’s data-driven world, businesses need **realtime access** to their data for decision-making, analytics, and AI workloads. This post explains how you can set up a realtime pipeline using **IBM StreamSets** and **watsonx.data**.  

We’ll walk through:

1. Reading invoices from **IBM Cloud Object Storage**  
2. Processing them with **StreamSets** (filtering, cleansing, quality checks, transformations)  
3. Writing the results into **watsonx.data** with **Apache Iceberg** tables  
4. Querying the data with **Presto** for fast analytics  

If you are new to these technologies, don’t worry—we’ll explain along the way.

---

## What is IBM StreamSets?

IBM StreamSets is a powerful **data integration tool** that allows you to build realtime pipelines visually. You can connect to many data sources, apply transformations, and push the cleaned data to your target systems.

Key features:
- **Drag-and-drop pipelines** with low code  
- **Realtime and batch ingestion**  
- Built-in **data quality checks**  
- Connectors for **cloud, databases, and lakehouses**

---

## What is watsonx.data?

IBM **watsonx.data** is an **open data lakehouse** that helps you store, query, and govern large volumes of structured and unstructured data.  

It integrates with open formats like **Apache Iceberg** and engines like **Presto** and **Spark**.  

Why Iceberg?  
- Supports **table partitions** for faster queries  
- Handles **schema evolution** (you can change table structure without breaking)  
- Provides **ACID transactions** (ensures data reliability)

---

## Architecture Overview

Here’s how the pipeline looks:

{{<mermaid>}}
flowchart TD
    A[Invoices in IBM Cloud Object Storage] --> B[IBM StreamSets Pipeline]
    B --> C["Data Filtering, Cleansing, Quality Checks, Transformations"]
    C --> D["watsonx.data Connector (Presto)"]
    D --> E["Apache Iceberg Catalog (COS Bucket as Storage)"]
    E --> F["Partitioned Iceberg Table in watsonx.data"]
{{</mermaid>}}

---

## Step 1: Read Invoices from IBM Cloud Object Storage

Invoices are stored as files in an **IBM Cloud Object Storage (COS)** bucket. StreamSets has a connector that lets you read from COS easily.  

**Example StreamSets configuration:**

```json
{
  "stage": "AmazonS3ObjectStorageOrigin",
  "bucket": "invoices-bucket",
  "filePattern": "*.json",
  "credentials": {
    "accessKey": "YOUR_ACCESS_KEY",
    "secretKey": "YOUR_SECRET_KEY"
  }
}
```

---

## Step 2: Process Data in StreamSets

Within StreamSets, add processors for:
- **Filtering** → Remove irrelevant records  
- **Cleansing** → Fix formatting issues (e.g., dates, amounts)  
- **Quality checks** → Drop invalid invoices  
- **Transformations** → Map fields to match your watsonx.data schema  

**Example field mapping (JSON to SQL table):**

```json
{
  "invoice_id": "${record:value('/id')}",
  "amount": "${record:value('/total')}",
  "currency": "${record:value('/currency')}",
  "timestamp": "${record:value('/date')}"
}
```

---

## Step 3: Write to watsonx.data

After processing, StreamSets writes the clean data into **watsonx.data** using the **Presto connector**. Presto is a distributed SQL engine that works with watsonx.data’s Iceberg catalog.

**Connection Example:**

```json
{
  "connector": "Presto",
  "url": "jdbc:presto://watsonxdata-host:443/iceberg/default",
  "user": "admin",
  "password": "********",
  "targetTable": "invoices_iceberg"
}
```

---

## Step 4: Optimize with Iceberg Partitions

Iceberg allows you to **partition tables** so queries run faster. For invoices, partitioning by `year` and `month` is a good choice.

**SQL Example:**

```sql
CREATE TABLE invoices_iceberg (
    invoice_id STRING,
    amount DECIMAL(10,2),
    currency STRING,
    timestamp TIMESTAMP
)
WITH (
    partitioning = ARRAY['year(timestamp)', 'month(timestamp)'],
    format = 'PARQUET'
);
```

---

## Step 5: Validate Data in watsonx.data

Once the data lands in the Iceberg table, you can check row counts:

```sql
SELECT COUNT(*) FROM invoices_iceberg;
```

Or run quick analytics:

```sql
SELECT currency, SUM(amount)
FROM invoices_iceberg
WHERE year(timestamp) = 2025
GROUP BY currency;
```

---

## Benefits of This Setup

✅ **Realtime ingestion** — Data is available almost instantly  
✅ **Cleaner, high-quality data** — Built-in validation  
✅ **Scalable queries** — Iceberg + Presto handle big data efficiently  
✅ **Open & flexible** — Works with open formats, no lock-in  
✅ **Ready for AI & ML** — watsonx.data integrates with **watsonx.ai**  

---

## Conclusion

With just a few steps, you can set up a realtime pipeline from **StreamSets → watsonx.data → Iceberg → Presto**.  

This gives you a **modern, open, and fast data platform** ready for analytics, dashboards, or AI workloads.  

If you want to explore further, try:  
- Adding **watsonx.ai** to run AI models on the ingested data  
- Connecting BI tools like **Cognos Analytics** or **Tableau**  
- Automating **data governance** with **IBM Knowledge Catalog**  

---

*Thanks for reading! If you found this tutorial useful, share it with your team and try building your own pipeline.* 🚀
