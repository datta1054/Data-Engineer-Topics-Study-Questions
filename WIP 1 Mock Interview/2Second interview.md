

# 🧩 ROUND 1 — SQL + Python / PySpark Technical Coding

**Duration:** 60–75 minutes  
**Focus:** Querying, data transformations, debugging, optimization, practical reasoning.  
**Environment:** Databricks notebook or shared SQL console.

---

## Q1. SQL — Advanced Aggregation and Window Functions

**Scenario:**
You have a table `transactions(user_id, txn_date, amount, city)`.

**Task:**
Find the top 2 cities by total spending for each day, and for each city, the difference in total spend compared to the previous day.

**Expected Query Logic:**
```sql
WITH daily_spend AS (
  SELECT
    city,
    txn_date,
    SUM(amount) AS total_spend
  FROM transactions
  GROUP BY city, txn_date
),
ranked AS (
  SELECT
    *,
    RANK() OVER (PARTITION BY txn_date ORDER BY total_spend DESC) AS rnk,
    total_spend - LAG(total_spend) OVER (PARTITION BY city ORDER BY txn_date) AS day_diff
  FROM daily_spend
)
SELECT * FROM ranked WHERE rnk <= 2;
```

### Key Concepts Tested
- Aggregations with GROUP BY
- Window functions: RANK, LAG
- Correct use of partitioning and ordering
- Edge handling for missing prior rows

### Evaluation Criteria
| Aspect               | What Interviewer Evaluates                       |
|----------------------|-------------------------------------------------|
| SQL correctness      | Syntax, grouping logic                          |
| Analytical reasoning | Can they explain difference between rank/dense_rank |
| Optimization         | Do they understand cost (partition pruning, filtering early) |
| Clarity              | Clean naming, readable structure                |

### Strong Answer Indicators
- Uses CTEs logically.
- Mentions that if the dataset is large, we’d cluster/partition by date for efficiency (BigQuery/Delta).
- Explains that LAG produces NULL for first day → can coalesce to 0.

---

## Q2. SQL Optimization Case

**Prompt:**
A query on a 2TB BigQuery table is taking 20 minutes. What steps would you take to tune it?

**Expected Discussion:**
- Check query plan → look for full scans, no partition filters.
- Add WHERE event_date >= '2025-01-01' → reduces scan bytes.
- Apply partitioning (event_date) and clustering (user_id or country).
- Materialize frequent joins → use materialized views.
- Avoid SELECT *, prefer explicit columns.
- If joining with small dimension → broadcast join.

**Key Concepts:** Partition pruning, clustering, shuffling, caching, join types.

**Evaluation Criteria:**
- Awareness of BigQuery architecture (slot-based execution)
- Focus on practical tuning, not theory
- Mention cost optimization (bytes scanned = cost driver)

---

## Q3. Python — Data Transformation

**Prompt:**
Write a Python function that takes a list of transactions:

```python
transactions = [
  {"user": "A", "amount": 100, "city": "NY"},
  {"user": "B", "amount": 150, "city": "NY"},
  {"user": "A", "amount": 200, "city": "LA"},
]
```

Return total amount per city.

**Ideal Solution:**
```python
from collections import defaultdict

Mock Interviews	Simulate 3-round sets every 2 weeks
    city_sum = defaultdict(int)
    for txn in transactions:
        city_sum[txn["city"]] += txn["amount"]
    return dict(city_sum)
```

**Follow-up:**
How would you do this using pandas or PySpark?

**PySpark Equivalent:**
```python
df.groupBy("city").agg(F.sum("amount").alias("total"))
```

**Evaluation Criteria:**
- Understanding of data structures (dict, defaultdict)
- Code readability & testability
- Can switch mental model → pandas → Spark

---

## Q4. PySpark Optimization Scenario

**Prompt:**
Your PySpark job that aggregates 1B rows runs out of memory. What steps do you take?

**Expected Answer:**
- Check for wide transformations causing shuffle (e.g., joins/groupBy).
- Use salting or repartitioning to avoid data skew.
- Apply broadcast join for small lookup tables.
- Cache only when reused multiple times.
- Increase executor memory or shuffle partitions appropriately.

**Concepts Tested:** Shuffles, partitioning, data skew, Spark UI interpretation.

**Evaluation Criteria:**
- Depth of Spark internals understanding.
- Concrete debugging approach (not just “increase memory”).
- Familiarity with job DAG / stages.

---

## Q5. Debugging Code

**Prompt:**
The following PySpark code fails:
```python
df.groupBy("city").sum("amount").filter(F.col("sum(amount)") > 1000)
```

**Issue?**

**Expected Answer:**
The aggregation renames column → can’t reference "sum(amount)".

**Fix:**
```python
df.groupBy("city").agg(F.sum("amount").alias("total")) \
  .filter(F.col("total") > 1000)
```

**Evaluation:** checks Spark API familiarity, debugging clarity.

---

## ✅ Round 1 Evaluation Summary

| Skill           | Weight | Strong Candidate Demonstrates                |
|-----------------|--------|---------------------------------------------|
| SQL             | 40%    | Correct joins, window fns, clean structure  |
| Python/PySpark  | 40%    | Practical data logic + Spark tuning         |
| Communication   | 20%    | Thinks aloud, clear structure, explains trade-offs |

---

# 🧩 ROUND 2 — Data System Design

**Duration:** 60–75 min  
**Focus:** Design scalable, reliable data platforms. Explain trade-offs, tech choices, and cloud usage.  
**Format:** Whiteboard / Miro / verbal reasoning.

## Q1. Design a Real-time Analytics Platform for Website Clickstream

**Requirements:**
- Ingest user click events from web apps in real time
- Store raw + processed data
- Generate hourly aggregates for dashboards

**Expected High-level Design:**

**Ingestion:**
- Pub/Sub (or Kafka) → Event ingestion.

**Processing:**
- Dataflow / Spark Structured Streaming → parse, enrich, deduplicate.

**Storage Layers:**
- Bronze (Raw): GCS or Delta Lake
- Silver (Clean): Parquet, partitioned by date/hour
- Gold (Aggregates): BigQuery or Delta table for BI

**Serving:**
- Looker Studio / Power BI dashboard.

**Monitoring:**
- Composer/Airflow orchestration, alerts via Stackdriver.

### Key Concepts Tested
- Batch vs Streaming, schema evolution, idempotency, fault tolerance, partitioning.

### Evaluation Criteria
| Aspect        | Good Candidate Mentions                |
|--------------|----------------------------------------|
| Ingestion    | Pub/Sub or Kafka, scalable message handling |
| Processing   | Dataflow/Spark Streaming, checkpointing |
| Storage      | Bronze–Silver–Gold design               |
| Schema evolution | JSON schema registry, versioning     |
| Observability| Monitoring, retries, DLQ handling       |

---

## Q2. Design a Scalable Data Warehouse for Multi-Country Sales

**Expected Approach:**
- Use star schema: FactSales + DimProduct + DimCustomer + DimRegion
- Partition fact by sale_date
- Slowly Changing Dimensions (SCD Type 2) for customers/regions
- Materialized views for regional aggregates

**Optimization Discussion:**
- BigQuery clustering by region_id
- Incremental loads (ELT)
- CDC for daily refresh

**Evaluation:** Dimensional modeling depth, warehouse reasoning, cost awareness.

---

## Q3. Design a Data Lakehouse in Databricks

**Architecture Layers:**
- Bronze: raw ingestion from GCS
- Silver: curated tables (clean + schema validated)
- Gold: business metrics tables for analysts

**Tech Stack:**
- Delta Lake (for ACID & time travel)
- Unity Catalog (for governance)
- Jobs + Workflows (for scheduling)
- MLflow for experiment tracking (optional)

**Concepts Tested:** Delta Lake internals, transaction logs, schema evolution, Z-ordering.

**Evaluation:**
- Understands Delta advantages over parquet (ACID + time travel).
- Mentions Z-order for query performance.
- Mentions Unity Catalog for lineage.

---

## Q4. Cross-cloud Strategy Question

**Prompt:**
“If we migrate this system to AWS, how does the architecture change?”

**Expected Discussion:**

| GCP        | AWS Equivalent         |
|------------|-----------------------|
| Pub/Sub    | Kinesis               |
| Dataflow   | Glue Streaming / EMR  |
| GCS        | S3                    |
| BigQuery   | Redshift / Athena     |
| Composer   | MWAA                  |

**Bonus:** Mentions that design patterns remain same (decoupled, schema-on-read, governance layer).

---

## ✅ Round 2 Evaluation Summary

| Skill         | Weight | Strong Candidate Demonstrates                |
|---------------|--------|---------------------------------------------|
| System Design | 50%    | Modular, scalable, fault-tolerant design    |
| Cloud Services| 30%    | Knows cross-cloud mappings & trade-offs     |
| Communication | 20%    | Uses diagrams, structured flow              |

---

# 🧩 ROUND 3 — Behavioral + Deep Technical Dive

**Duration:** 45–60 min  
**Format:** Conversational, project storytelling + situational problem-solving.

## Q1. Tell me about a Data Pipeline You Designed End-to-End

**Expected Structure (STAR):**
- **Situation:** Business needed product-level sales dashboard daily.
- **Task:** Build automated ETL from transactional DB → BigQuery.
- **Action:**
    - Used Dataflow to extract data daily.
    - Implemented CDC to handle late-arriving updates.
    - Designed partitioned BigQuery tables.
    - Added alerting on job failures.
- **Result:** Reduced refresh time from 3 hrs to 20 mins, improved reliability 99%.

**Evaluation:** clarity, ownership, communication with stakeholders.

---

## Q2. Describe a Time You Optimized a Costly Job

- Identified BigQuery query scanning entire 2TB table.
- Added date filter + partition pruning → 80% cost reduction.
- Created materialized view for reused aggregation.
- Mentions proactive monitoring of slot utilization.

**Evaluation:** analytical thinking, measurable impact.

---

## Q3. Stakeholder Scenario

**Prompt:**
“PM wants a daily dashboard update by 8 AM, but upstream data arrives inconsistently until 9:30. How do you handle it?”

**Good Response:**
- Communicate SLA mismatch.
- Implement backfill or late-trigger pipeline.
- Provide temporary fallback using last complete data.
- Log SLA breach transparently.

**Evaluation:** prioritization, communication, accountability.

---

## Q4. Deep Dive: Delta Lake Internals

**Prompt:**
“How does Delta Lake ensure ACID transactions on distributed storage?”

**Strong Answer:**
- Transaction log _delta_log/ with JSON commits.
- Each commit records file additions/deletions (append-only log).
- Readers use snapshot isolation → read consistent version.
- Writers use optimistic concurrency → fail on conflicting writes.

**Evaluation:** conceptual depth, confidence, clarity.

---

## ✅ Round 3 Evaluation Summary

| Skill           | Weight | Strong Candidate Demonstrates                |
|-----------------|--------|---------------------------------------------|
| Project Clarity | 30%    | Explains impact, metrics                    |
| Problem-solving | 40%    | Handles ambiguity logically                 |
| Behavioral Fit  | 30%    | Ownership, communication, collaboration     |

---

# 🧭 Final Guidance — Preparation Path

| Focus Area    | Action Plan                                                      |
|-------------- |------------------------------------------------------------------|
| SQL           | 2–3 complex queries/day on DataLemur/LeetCode + review query plans|
| PySpark       | Implement transformations and optimize with Spark UI analysis     |
| System Design | Draw data architectures weekly; explain trade-offs aloud          |
| Behavioral    | Practice STAR stories with quantifiable results                  |
| Mock Interviews | Simulate 3-round sets every 2 weeks                            |