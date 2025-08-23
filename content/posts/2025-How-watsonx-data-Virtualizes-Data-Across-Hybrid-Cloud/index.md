---
title: "Data Fabric in Action: How watsonx.data Virtualizes Data Across Hybrid Cloud"
date: 2025-08-22
draft: false
summary: "Learn how IBM watsonx.data enables data virtualization and federated queries across PostgreSQL, DB2, S3, and more to build a modern data fabric."
tags: ["watsonx.data", "IBM Data Lakehouse", "Data Fabric", "Data Virtualization", "Apache Iceberg", "Presto Spark", "DataStage ETL", "Federated Queries"]
categories: ["Data & AI", "IBM watsonx", "Data Fabric"]
series: ["watsonx.data Series"]
series_order: 1
---

{{< badge >}}
Tutorial
{{< /badge >}}

# Data Fabric in Action: How watsonx.data Virtualizes Data Across Hybrid Cloud

## Introduction: Why Data Silos Are Slowing You Down
In today’s world, data is the new oil. Companies rely on it to make decisions, build products, and serve customers better. But here’s the problem: **most organizations don’t have “one” dataset—they have hundreds, sometimes thousands, scattered across different systems**.  

Think about it:  
- Customer profiles may live in **PostgreSQL** (an open-source database).  
- Financial transactions might be stored in **IBM DB2**, an enterprise-grade system.  
- IoT devices stream sensor readings into **Amazon S3 buckets**.  
- Teams experiment with data science models in Hadoop, or even newer cloud warehouses.  

Each of these systems is like a **separate island of data**. When data is stuck on islands, companies face:  
- **Slow reporting.** Analysts wait days for a complete view.  
- **High costs.** Data is copied multiple times across systems.  
- **Risk.** Moving sensitive data around increases compliance and security concerns.  

This challenge is known as **data silos**. Breaking those silos—without creating new problems—is where **IBM watsonx.data Lakehouse** shines.  

---

## What is a Data Fabric? (Explained Simply)
A **data fabric** is a fancy term, but the concept is simple. Imagine your company’s data is a bunch of books scattered across many libraries. Traditionally, to read them, you’d gather all those books into one big central library. That’s what data warehouses and lakes tried to do.  

But what if you could **stay in your chair and instantly read any book from any library**—without moving it? That’s a **data fabric**.  

In the data world, a fabric:  
- **Connects multiple systems** (databases, cloud storage, apps).  
- Lets you **query data where it lives** instead of copying it.  
- Applies **security, governance, and metadata** consistently across sources.  

This means a data analyst, business manager, or AI model can access **PostgreSQL, DB2, and S3** data in one place, without waiting for IT to build pipelines.  

---

## watsonx.data: IBM’s Data Lakehouse with a Twist
At the heart of IBM’s data fabric is **watsonx.data**, often described as an **IBM data lakehouse**. But what does that mean?  

### Lakehouse in Simple Terms
Traditionally:  
- A **data warehouse** = structured, organized, great for reporting, but rigid and expensive.  
- A **data lake** = flexible, stores raw data, but often messy and slow to query.  

A **lakehouse** combines both:  
- The **flexibility of a lake** (store any kind of data, often in cheap open formats like Parquet).  
- The **performance of a warehouse** (fast queries, indexing, optimization).  

### Why watsonx.data is Different
Unlike closed systems, watsonx.data is:  
- **Open.** It supports **Apache Iceberg lakehouse tables**, Parquet, and open SQL engines.  
- **Hybrid.** Deploy it on cloud, on-premises, or both.  
- **Federated.** Run queries across PostgreSQL, DB2, S3, and more—without duplication.  
- **AI-Ready.** Seamlessly connect to **watsonx.ai** to train and deploy AI models.  

In short, watsonx.data is **not just another place to store data**—it’s the **control plane** for how you access, govern, and analyze it.  

{{< article link="/posts/2025-realtime-ingestion-watsonxdata/" showSummary=true compactSummary=true >}}

<!-- > 🔗 *Also read: [Streaming Meets Lakehouse: StreamSets + watsonx.data for Real-Time Insights](#)*   -->

---

## watsonx.data Tutorial: Query PostgreSQL + DB2 + S3 in One Shot
Let’s walk through an example.  

### The Challenge
A retail company wants a **360° view of customers**:  
- **Profiles** in PostgreSQL  
- **Transactions** in DB2  
- **Web clickstream logs** in S3  

Traditionally, IT would spend weeks building **ETL pipelines** (Extract, Transform, Load) to copy this data into a warehouse. That means: high cost, long delays, and outdated reports.  

Here is sequence diagram showing a BI query hitting watsonx.data, which consults governance, orchestrates Presto/Spark pushdown to PostgreSQL/DB2/S3, merges results, and returns governed output.

{{<mermaid>}}
sequenceDiagram
  participant User as Analyst or BI
  participant WX as watsonx data
  participant ENG as Presto or Spark
  participant P as PostgreSQL
  participant DB2 as IBM DB2
  participant S3 as Amazon S3
  participant IKC as IBM Knowledge Catalog

  User->>WX: Submit SQL join across PostgreSQL DB2 S3
  WX->>IKC: Check policies and metadata
  IKC-->>WX: Return allowed access and rules
  WX->>ENG: Plan and optimize federated query
  ENG->>P: Push filters and aggregations
  ENG->>DB2: Push filters and aggregations
  ENG->>S3: Read parquet or iceberg partitions
  P-->>ENG: Partial results
  DB2-->>ENG: Partial results
  S3-->>ENG: Partitioned results
  ENG->>WX: Merge and finalize
  WX-->>User: Return governed dataset
{{</mermaid >}}


### The watsonx.data Way
With watsonx.data, you don’t move the data—you **query it in place**:  

```sql
SELECT c.customer_id, c.name, t.txn_amount, s.page_view
FROM postgresql.customers c
JOIN db2.transactions t ON c.customer_id = t.customer_id
JOIN s3.clickstream s ON c.customer_id = s.customer_id
WHERE t.txn_date > CURRENT_DATE - 30;
```  

✅ One query.  
✅ Three systems.  
✅ No replication.  

The result: **real-time insights** across multiple systems—something impossible with traditional pipelines.  

{{<mermaid >}}
flowchart LR
  subgraph Sources["Data Sources"]
    P["PostgreSQL"]
    D["IBM DB2"]
    S3["Amazon S3"]
    M["MySQL or MongoDB"]
  end

  subgraph Fabric["Data Fabric and Lakehouse"]
    WX["watsonx.data<br/>(Open, federated lakehouse)"]
    ICE["Apache Iceberg / Parquet<br/>(Managed tables)"]
    WKC["IBM Knowledge Catalog<br/>(Lineage, Policies, Glossary)"]
    PRE["Presto / Spark engines<br/>(Query federation & pushdown)"]
  end

  subgraph ETL["Optional Ingestion"]
    DS["IBM DataStage<br/> (ETL)"]
    SS["IBM StreamSets<br/> (Realtime Ingestion)"]
  end

  subgraph Consumers["Consumers"]
    BI["Analysts and BI Tools"]
    DSci["Data Scientists using watsonx ai"]
    APP["Apps and APIs"]
  end

  %% Virtualized access
  P --> WX
  D --> WX
  S3 --> WX
  M --> WX

  %% Managed tables path
  WX --> ICE
  ICE --> WX

  %% Engines and governance
  WX --> PRE
  WKC --> WX
  WKC --> ICE

  %% Optional ingestion into managed tables
  P --> DS --> ICE
  S3 --> SS --> ICE
  D --> DS
  M --> SS

  %% Consumers
  BI --> WX
  DSci --> WX
  APP --> WX
{{</mermaid >}}

Architecture showing PostgreSQL, DB2, S3, and other sources queried virtually by watsonx.data, governed by IBM Knowledge Catalog, optionally landing in Apache Iceberg/Parquet, with Presto/Spark engines serving BI, data science (watsonx.ai), and apps

<!-- > 🔗 *Also read: [Query Federation at Scale: Why watsonx.data is the Future of Analytics](#)*   -->

---

## Breaking Down the Concepts (For Non-Technical Readers)
- **Data Virtualization:** Like using a universal remote that controls all your TVs, speakers, and lights. One interface, multiple systems.  
- **Federated Queries:** Instead of asking three people separately and combining answers, you ask one person who gathers all answers instantly.  
- **Presto Spark:** An open-source query engine that runs queries in parallel across systems—like splitting a big job among 100 workers instead of 1.  
- **Apache Iceberg Lakehouse:** A modern way of organizing data in tables. Think of it as adding an “index” and “chapters” to your messy data lake, making it easy to search and update.  

---

## Business Value: Why This Matters Beyond IT
The benefits of watsonx.data go far beyond the IT department:  
- **Speed.** Analysts get reports in minutes, not days.  
- **Cost.** Stop paying for duplicate storage and endless pipelines.  
- **AI-ready.** Data scientists can train models directly on fresh data.  
- **Compliance.** Sensitive data stays in PostgreSQL or DB2, governed at source.  

Companies adopting **IBM’s lakehouse + fabric** often cut costs by **30–50%** while accelerating decision-making.  

---

## Industry Use Cases

### Telecom: Open Source + CRM
Customer records sit in **PostgreSQL**, billing data in **DB2**, and usage logs in **S3**. With watsonx.data, a telecom can analyze churn risk in near real time without migration.  

### Government: Smart Cities
City planners combine **PostgreSQL open data (population stats)** with **IoT traffic feeds in S3** to optimize transport systems.  

### Finance: Compliance
Banks run fraud detection queries across **DB2 transactions**, **PostgreSQL profiles**, and **Iceberg audit logs** in S3. No sensitive data leaves its system.  

<!-- > 🔗 *Also read: [Financial Sector Use Case: Risk & Compliance with watsonx.data](#)*   -->

---

## Best Practices: Mixing Open Source and Enterprise Data
1. **Start small.** Connect 2–3 systems first (e.g., PostgreSQL + DB2 + S3).  
2. **Use ETL wisely.** Use **DataStage ETL** for heavy data transformations, but lean on watsonx.data for analytics.  
3. **Standardize formats.** Adopt **Apache Iceberg lakehouse** for scalable, reliable storage.  
4. **Govern everything.** Use **IBM Knowledge Catalog** to ensure metadata, policies, and lineage are clear.  
5. **Optimize queries.** Enable **pushdown** so filtering happens in PostgreSQL or DB2 directly, reducing load.  

---

## watsonx.data + Presto Spark: Why It Scales
At the core of watsonx.data’s power is **Presto Spark**.  

Here’s why it matters:  
- **Parallelization.** Breaks a large query into smaller tasks, runs them at the same time.  
- **Pushdown.** Pushes filtering and joins to the database itself—less data moves.  
- **Caching.** Frequently accessed results are stored temporarily for faster response.  

This means watsonx.data can run federated queries across **petabytes of data** from multiple open-source and enterprise systems without choking your network.  

---

## Conclusion: watsonx.data as the Bridge Between Open Source and Enterprise
Most organizations live in a hybrid world: **PostgreSQL, MySQL, DB2, S3, Hadoop, and more**. The problem isn’t lack of data—it’s lack of **connected, trusted access**.  

watsonx.data solves this by:  
- Virtualizing data across systems.  
- Running federated queries at scale with **Presto Spark**.  
- Supporting modern open formats like **Apache Iceberg lakehouse**.  
- Enabling AI pipelines that bridge **open-source agility with enterprise compliance**.  

👉 Whether you’re an engineer, a data scientist, or a business leader, watsonx.data offers a **smarter, faster, safer** way to put your data to work.  

If you’re new, start small: connect PostgreSQL and DB2 in watsonx.data, run a federated query, and see how fast insights flow. From there, scale to your full hybrid cloud.  
