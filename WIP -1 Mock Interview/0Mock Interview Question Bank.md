
# 🧠 MOCK INTERVIEW QUESTION BANK – Data Engineer (Mid-Level)

---

## 1️⃣ SQL & Query Optimization

### 🟢 Core Questions (Must-know)
- **Write a SQL query to find users who made at least 3 purchases in the last 7 days.**
	- *Tests:* filtering, aggregation, date functions.
	- *Answer direction:* `WHERE order_date >= CURRENT_DATE - INTERVAL '7' DAY` + `GROUP BY user_id HAVING COUNT(*) >= 3`.
- **Find the second highest salary in each department.**
	- *Tests:* window functions (`DENSE_RANK()`, `ROW_NUMBER()`).
- **Explain the difference between `RANK()` and `DENSE_RANK()` in SQL.**
- **What happens internally when you execute a JOIN between two large tables?**
	- *Tests:* join algorithms, cost estimation, shuffles in distributed engines.

### 🟡 Intermediate Questions
- **Given a huge table, how would you debug a slow-running query in BigQuery/Databricks?**
	- *Answer direction:* check scanned bytes, filter pushdown, join order, partition pruning, query plan.
- **Explain how indexes improve query performance. When can they hurt performance?**
- **What is a window frame? Give an example of a moving average over 7 days.**
- **How do you handle NULLs in SQL joins and aggregations?**
	- *Tests:* correctness & edge cases.

### 🔵 Advanced / Optimization Scenarios
- **A query that aggregates by region and product is very slow in BigQuery. What would you check?**
	- *Answer direction:* clustering columns, materialized view, partition filter, slots usage.
- **You have two datasets: 10GB transactions and 10MB customer dimension. How do you join efficiently in Spark SQL?**
	- *Answer:* broadcast join the small table.
- **Explain how the BigQuery optimizer chooses partition pruning and clustering keys.**
- **What are common causes of data skew in Spark SQL, and how can you mitigate them?**

---

## 2️⃣ Python & PySpark

### 🟢 Core
- **Explain the difference between `map()`, `filter()`, and `reduce()` in Python.**
- *Follow-up:* Show how you’d compute word counts using these.
- **How do you handle exceptions and logging in Python ETL scripts?**
- **Write a function to remove duplicates from a list while maintaining order.**
- **Explain the difference between `@staticmethod` and `@classmethod`.**

### 🟡 Intermediate
- **Explain the difference between `groupByKey()` and `reduceByKey()` in PySpark. Which is better and why?**
- **What are narrow vs. wide transformations in Spark? Give examples.**
- **What causes a shuffle in Spark? How can you minimize it?**
- **How would you test a PySpark transformation function?**
	- *Answer direction:* use local Spark session + Pytest with small dataframes.
- **What’s the difference between a Python UDF and a pandas UDF in Spark? When to use each?**

### 🔵 Advanced
- **You have a PySpark job that keeps running out of memory. How do you debug and fix it?**
	- *Answer direction:* check partition size, caching, skew, UDF serialization.
- **Explain how Spark’s Catalyst optimizer works in brief.**
- **Describe how AQE (Adaptive Query Execution) improves performance in Databricks.**
- **How would you implement an SCD Type 2 dimension table using PySpark and Delta Lake?**

---

## 3️⃣ Cloud Platforms (GCP Focus)

### 🟢 Core
- **Explain the differences between BigQuery and Dataflow.**
- **How would you load daily CSV files from GCS into BigQuery efficiently?**
- **What’s the purpose of Composer in GCP?**
- **What are service accounts and how do you manage permissions in GCP?**

### 🟡 Intermediate
- **BigQuery job costs are skyrocketing — what steps do you take to reduce cost?**
- **Explain partitioning vs clustering in BigQuery.**
- **Describe how Dataflow handles late data and watermarks.**
- **How do you monitor failed jobs in Composer?**
- **Compare GCS vs. S3 vs. ADLS.**

### 🔵 Advanced
- **Design a pipeline that streams events from Pub/Sub → BigQuery in near real-time.**
- **How do you ensure end-to-end security and governance across GCP data services?**
- **What’s the difference between BigQuery storage API and query API? When to use each?**
- **Explain the trade-offs between Dataflow and Dataproc for batch workloads.**

---

## 4️⃣ Databricks & Spark Core

### 🟢 Core
- **Describe the role of the driver and executors in a Spark cluster.**
- **What are narrow and wide transformations?**
- **What causes stage boundaries in Spark?**
- **Explain lazy evaluation with an example.**

### 🟡 Intermediate
- **How do you debug slow Spark jobs using the Spark UI?**
- **What is broadcast join and when should you use it?**
- **How does caching work in Spark? When can it hurt performance?**
- **Describe the Delta Lake transaction log structure.**
- **Explain how you would set up CI/CD for Databricks notebooks.**

### 🔵 Advanced
- **Your Spark job takes 3 hours; after enabling AQE, it drops to 1 hour. What changed internally?**
- **Explain Delta Lake’s ACID mechanism in detail.**
- **What is Z-Ordering in Delta Lake and why is it useful?**
- **How do you manage schema evolution in a Delta table over time?**
- **Design a Databricks workflow to run dependent jobs with alerting and retries.**

---

## 5️⃣ Data Modeling & Warehousing

### 🟢 Core
- **Explain OLTP vs OLAP systems.**
- **Define star and snowflake schemas.**
- **What is the grain of a fact table and why is it important?**
- **What are surrogate keys and why use them?**

### 🟡 Intermediate
- **Explain SCD Type 1 vs Type 2 with examples.**
- **Design a data model for a ridesharing app (driver, rider, trip, payment).**
- **How do you model many-to-many relationships in a warehouse?**
- **When would you denormalize data in a warehouse?**

### 🔵 Advanced
- **Explain the advantages of Data Vault modeling.**
- **How would you handle schema evolution in a data warehouse?**
- **Design a star schema for sales reporting with monthly aggregates and promotion dimensions.**

---

## 6️⃣ Data Architecture & Pipelines

### 🟢 Core
- **What is the difference between ETL and ELT?**
- **What is idempotency, and why is it important in data pipelines?**
- **How would you orchestrate a pipeline with dependencies in Airflow?**
- **What’s the role of checkpoints in streaming pipelines?**

### 🟡 Intermediate
- **How do you ensure data quality and observability in pipelines?**
- **What’s the difference between batch, micro-batch, and streaming?**
- **How do you handle late-arriving data in streaming pipelines?**
- **What are some strategies for cost optimization in cloud-based pipelines?**
- **How would you implement CDC (Change Data Capture)?**

### 🔵 Advanced
- **Design a multi-zone data lake (bronze/silver/gold) architecture.**
- **How do you implement lineage tracking for your data pipelines?**
- **Describe how you’d scale a pipeline that’s currently processing 1M → 50M daily records.**
- **Explain event-driven data pipelines and give real-world examples.**

---

## 7️⃣ System Design for Data Engineers

### 🟢 Core
- **Design a scalable daily batch ingestion from APIs to warehouse.**
- **Explain how you’d partition large datasets to improve query performance.**
- **How would you design for schema evolution and backward compatibility?**

### 🟡 Intermediate
- **Design a real-time analytics system for user clicks (from event to dashboard).**
- **How do you handle retries and duplicate events in a distributed pipeline?**
- **Design a data lakehouse architecture using GCP or Databricks components.**

### 🔵 Advanced
- **How would you build a unified data platform serving both analytics and ML?**
- **Design a system to detect anomalies in financial transactions in near real-time.**
- **How would you migrate an on-prem Hadoop data lake to GCP with minimal downtime?**

---

## 8️⃣ DSA & Coding for Data Engineers

### 🟢 Core
- **Reverse a string without using built-in reverse functions.**
- **Count frequency of each element in a list.**
- **Find the first non-repeating character in a string.**
- **Remove duplicates from a list.**

### 🟡 Intermediate
- **Given an array of timestamps, group them into sessions where gaps > 30 mins start a new session.**
- **Implement a join between two lists of dicts (like SQL inner join).**
- **Given log entries (user, timestamp), find active users per day.**
- **Simulate a sliding window average of numbers using deque.**

---

## 9️⃣ Behavioral & Project Discussion

### 🟢 Core
- **Tell me about a data pipeline you designed end-to-end. What were the key challenges?**
- **Describe a time you optimized a slow or costly data job.**
- **Give an example of when you identified a data quality issue and resolved it.**
- **How do you prioritize work when multiple stakeholders want changes?**

### 🟡 Intermediate
- **Describe a project where you improved system scalability.**
- **Tell me about a time you disagreed with a design decision — how did you handle it?**
- **What metrics do you use to measure success of a data pipeline?**
- **Describe a situation where you automated a manual data process.**

### 🔵 Advanced
- **Tell me about a failure or outage — how did you debug, communicate, and prevent recurrence?**
- **How do you balance data reliability vs. delivery speed when under pressure?**
- **Describe how you handled a breaking schema change in production.**

---

## ✅ HOW TO PRACTICE EFFECTIVELY

| Type                | Frequency         | Method                                         |
|---------------------|------------------|------------------------------------------------|
| SQL Coding Rounds   | 3–4 times/week   | LeetCode (medium), DataLemur, Mode Analytics   |
| Python/PySpark      | 3 mini ETL/week  | Practice transformations & joins in PySpark     |
| System Design       | 2/week           | Whiteboard design → verbal walk-through         |
| Behavioral          | 1–2 sessions/wk  | STAR framework with real projects               |
| Mock Interviews     | Every 2 weeks    | Peer or coach-based, timed 45–60 min sessions   |