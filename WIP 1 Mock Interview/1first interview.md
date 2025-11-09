
events(user_id: STRING, event_ts: TIMESTAMP, page: STRING, event_type: STRING)
session_id (stable per user session),
session_start, session_end,
events_count, unique_pages, duration_seconds.
spark = SparkSession.builder.getOrCreate()
df5 = df4.withColumn("session_seq", spark_sum("is_new_session").over(w2))
cumulative sum over is_new_session assigns a session sequence per user deterministically.
session_id concatenation is simple; for production prefer hashing (to keep fixed length) or using UUIDs derived deterministically.
duplicates.

# 📝 Round 1 — SQL + Python/PySpark (90 minutes total)

**Format:** 45 minutes SQL + 45 minutes Python/PySpark  
**Goal:** Correctness, expressive SQL (window functions/CTEs), ability to read/explain plans & optimize; and writing scalable, testable PySpark code that handles real issues (skew, late data, dedupe).

---

## SQL Task (45 minutes)
### Problem (30 min coding + 15 min optimization discussion)

You have a table `transactions`:

| column          | type      | notes                |
|-----------------|-----------|----------------------|
| order_id        | STRING    | external order id    |
| transaction_id  | STRING    | event id             |
| user_id         | STRING    |                      |
| amount          | NUMERIC   |                      |
| currency        | STRING    |                      |
| transaction_ts  | TIMESTAMP |                      |
| status          | STRING    | 'SUCCESS' / 'FAILED' |
| country         | STRING    |                      |

#### Goals
- **Deduplicate transactions:** Keep only the latest record per `transaction_id`. Consider duplicates may have different status. (Show SQL.)
- **From deduplicated data, compute daily revenue per country and a 7-day moving average of daily revenue (for the last 90 days).** Optimize for a BigQuery/Databricks-style engine and explain how you'd improve runtime/cost.

#### Model SQL Answer (engine-agnostic — uses standard ANSI + window functions)
```sql
-- Step 1: Deduplicate: keep the latest row per transaction_id
WITH dedup_tx AS (
  SELECT
    transaction_id,
    order_id,
    user_id,
    amount,
    currency,
    transaction_ts,
    status,
    country,
    ROW_NUMBER() OVER (PARTITION BY transaction_id ORDER BY transaction_ts DESC) AS rn
  FROM transactions
), latest_tx AS (
  SELECT * EXCEPT (rn)
  FROM dedup_tx
  WHERE rn = 1
), daily_rev AS (
  SELECT
    DATE(transaction_ts) AS dt,
    country,
    SUM(CASE WHEN status = 'SUCCESS' THEN amount ELSE 0 END) AS revenue
  FROM latest_tx
  WHERE transaction_ts >= CURRENT_DATE() - INTERVAL '120' DAY  -- keep extra buffer for 7-day MA
  GROUP BY dt, country
)
SELECT
  dt,
  country,
  revenue,
  -- 7-day moving average including current day
  ROUND(AVG(revenue) OVER (PARTITION BY country ORDER BY dt ROWS BETWEEN 6 PRECEDING AND CURRENT ROW), 2) AS ma_7d
FROM daily_rev
WHERE dt >= CURRENT_DATE() - INTERVAL '90' DAY
ORDER BY country, dt;
```

#### Explanation & Reasoning
- **Dedup via `ROW_NUMBER()`** ensures we explicitly pick the latest `transaction_ts`. If business rule is “prefer SUCCESS over FAILED if timestamps equal”, add `ORDER BY transaction_ts DESC, status = 'SUCCESS' DESC`.
- **Compute revenue only from `latest_tx`** to avoid double-counting.
- **Use a window `AVG()`** with `ROWS BETWEEN 6 PRECEDING` for an exact 7-day moving average.
- **Filtering for a recent date range** reduces scanned data.

#### Optimization Conversation (what to say, 15 min)
**Interviewer expectation:** Show engine knowledge + cost/performance action items.

**Key optimizations to propose (say them verbally and justify):**
- **Partitioning:** Partition transactions by `DATE(transaction_ts)` — query filters will prune partitions. (Big win.)
- **Clustering / Sort keys:** Cluster on `country`, `transaction_id` (or `user_id`) if queries often filter by country — reduces I/O for those queries.
- **Materialized view:** If daily revenue is queried repeatedly, create a materialized view or scheduled ETL job that pre-aggregates daily revenue.
- **Limit scanned bytes:** Avoid `SELECT *` in production; add column projection.
- **Dedup locality:** If duplicates are rare and we have ingestion idempotence, dedupe at ingestion (best) instead of full-table windowing at query time.
- **Use approximate functions** for heavy aggregates if exactness is not required (e.g., `APPROX_COUNT_DISTINCT`).
- **Explain plans:** Run `EXPLAIN`/`EXPLAIN ANALYZE` to find large scans, sorts or shuffles. If an expensive sort appears, add partitioning or pre-aggregation.

**Follow-ups & Model Responses**
- **Q:** What if transactions is 5B rows?
  - **A:** Partition + cluster, pre-aggregate nightly into daily buckets (bronze→silver→gold), push dedupe to streaming ingestion (CDC) to reduce full-table operations. Also consider incremental processing with an `is_latest` flag maintained by streaming upserts (Delta MERGE).
- **Q:** Why `ROW_NUMBER()` and not `MAX(transaction_ts)` join?
  - **A:** `ROW_NUMBER()` preserves all other columns from the chosen row easily; `MAX()` returns only timestamp which then requires a join, potentially costing extra shuffle. Either can work, but `ROW_NUMBER()` is expressive and often efficient with proper partitioning.

#### Evaluation Rubric (SQL; total 40 points)
- Correctness & edge cases: 16 pts (dedupe correctly, correct revenue calc)
- Use of advanced SQL (window functions, CTEs): 8 pts
- Explanation of optimization & cost: 8 pts
- Clarity & handling of scale: 8 pts
- **Pass threshold for this task:** ≥ 28/40

---

## Python / PySpark Task (45 minutes)
### Problem (35 min coding + 10 min Q&A)

You have event logs in a streaming/batch source:

`events(user_id: STRING, event_ts: TIMESTAMP, page: STRING, event_type: STRING)`

**Task:** Build sessionization at user level with a 30-minute inactivity timeout. For each session, compute:
- `session_id` (stable per user session)
- `session_start`, `session_end`
- `events_count`, `unique_pages`, `duration_seconds`

Write a batch PySpark solution (assume 100M rows/day). Explain how you'd adapt to Structured Streaming and handle late/out-of-order events.

#### Model PySpark Batch Solution (pseudocode — ready to paste into a notebook)
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import lag, unix_timestamp, col, when, sum as spark_sum, min as spark_min, max as spark_max, count, approx_count_distinct
from pyspark.sql.window import Window



# assume df has columns: user_id, event_ts (timestamp), page, event_type
df = spark.table("events") \
    .select("user_id", "event_ts", "page", "event_type") \
    .where("event_ts >= date_sub(current_date(), 2)")  # limit data for example

# sort per user
w = Window.partitionBy("user_id").orderBy("event_ts")

# previous timestamp
df2 = df.withColumn("prev_ts", lag("event_ts").over(w))

# gap in seconds
df3 = df2.withColumn("gap_secs", (unix_timestamp(col("event_ts")) - unix_timestamp(col("prev_ts"))))

# mark new session where prev_ts is null or gap > 30*60
df4 = df3.withColumn("is_new_session",
                     when(col("prev_ts").isNull() | (col("gap_secs") > 30*60), 1).otherwise(0))

# cumulative sum to create session sequence number per user
w2 = Window.partitionBy("user_id").orderBy("event_ts").rowsBetween(Window.unboundedPreceding, 0)
df5 = df4.withColumn("session_seq", spark_sum("is_new_session").over(w2))

# create a stable session id (string)
df6 = df5.withColumn("session_id", col("user_id").cast("string") + "_s_" + col("session_seq").cast("string"))

# aggregate per session
session_agg = df6.groupBy("user_id", "session_id", "session_seq") \
    .agg(
        spark_min("event_ts").alias("session_start"),
        spark_max("event_ts").alias("session_end"),
        count("*").alias("events_count"),
        approx_count_distinct("page").alias("unique_pages")
    ).withColumn("duration_seconds", (unix_timestamp(col("session_end")) - unix_timestamp(col("session_start"))))

# final
session_agg.show(truncate=False)
```

#### Explanation & Important Points
- Use `lag()` to compute time gap per user; when gap > 30 minutes we start a new session.
- Cumulative sum over `is_new_session` assigns a session sequence per user deterministically.
- Aggregation uses `spark_min`/`spark_max` to compute session bounds.
- Use `approx_count_distinct` for `unique_pages` for large-scale; switch to exact `countDistinct` only if needed.
- `session_id` concatenation is simple; for production prefer hashing (to keep fixed length) or using UUIDs derived deterministically.

#### Adapting to Structured Streaming & Out-of-Order Events
- Use event-time processing with watermarking:
  - Example: `.withWatermark("event_ts", "1 hour")` to allow late events within 1 hour.
- Use stateful aggregations keyed by user + session; manage TTL for state with `spark.conf` settings.
- Out-of-order events: watermark defines acceptable lateness; beyond watermark these events are dropped or written to a dead-letter path for manual reconciliation.
- For very low latency and high throughput, Flink provides stronger event-time semantics with efficient state backends (RocksDB).

#### Handling Skew & Memory Pressure
- If some users have enormous event volumes (hot users), their partitions become heavy. Mitigate by salting the key: append a hash shard to `user_id` and later recombine. Alternatively, process heavy users separately.
- Use `repartition(num_partitions, "user_id")` to set parallelism roughly to total_cores.
- Persist intermediate DF only if reused; unpersist when done.

#### Unit Testing & Validation
Create small synthetic DataFrames with edge cases:
- single event per user
- exact 30-minute gap
- out-of-order timestamps
- duplicates
Assert number of sessions per user, session durations, and aggregated counts.

**Follow-ups & Model Answers**
- **Q:** How to make `session_id` deterministic across reprocessing?
  - **A:** Use `hash(user_id || session_seq || date)` or compute session start timestamp and hash (`user_id`, `session_start_ts`) — stable as long as dedupe and ordering rules are stable.
- **Q:** If events arrive 2 days late?
  - **A:** Increase watermark window or maintain a replay path — but large lateness increases state retention cost. Prefer event enrichment with flags (`is_late`) and have a backfill process for late windows.

#### Evaluation Rubric (PySpark; total 40 points)
- Correctness of algorithm & code: 16 pts (session boundaries correct, aggregations)
- Scalability & production considerations: 12 pts (watermarks, skew, partitioning)
- Code quality & testability: 6 pts
- Explanation & tradeoffs (streaming vs batch): 6 pts
- **Pass threshold:** ≥ 28/40

# 🏗️ Round 2 — System Design (60 minutes)

**Format:** 60 minutes whiteboard / verbal; candidate should draw architecture and justify.  
**Goal:** Evaluate ability to clarify requirements, design for scale/latency/reliability/security, and reason about trade-offs.

## Problem Statement
**Design a near-real-time fraud detection service for card transactions for a global payment company.**

### Requirements / Constraints
- Ingest **50,000 transactions per second (TPS)** peak.
- Detection latency: **≤ 5 seconds** from ingestion to decision (block or allow/score).
- Accuracy: minimize false positives; flagged transactions must be available for manual review.
- Retain raw events for **1 year** (for audits).
- Support retraining nightly with features computed offline.
- PCI & PII handling: must be encrypted and tokenized.
- Multi-region availability (APAC / US / EU), failover toleration.
- Budget conscious: design for cost-efficiency at scale.
- **Timebox:** 10 minutes clarify + 40 minutes design + 10 minutes Q&A + trade-offs.

### Clarifying Questions the Candidate Should Ask (Sample)
- Is the 50k TPS global or per region? (Assume global)
- What % of transactions require blocking vs manual review? (Affects downstream DB sizing)
- Allowed decision types: Allow / Block / ManualReview / Score only?
- Are there existing message bus or cloud vendor constraints (Kafka, Pub/Sub, etc.)?
> (You should ask at least one or two of these to show requirement gathering.)

### High-Level Architecture (candidate should draw boxes & arrows)
**Producers / Edge validation**
- Merchant gateway produces transaction events → lightweight validation (schema checks, tokenization) before sending to ingestion bus.
- Use TLS + client auth.

**Ingestion layer: Kafka (or Pub/Sub)**
- Kafka cluster with partitions keyed by card_token or card_hash. Target partition count = throughput / desired per-partition throughput.
- Schema Registry (Avro/Protobuf) to enforce schema and evolution.

**Stream processing & feature computation**
- Low-latency scoring path (≤5s):
  - Stateless enrichments + fetch online features from Online Feature Store (Redis/Cassandra/Bigtable) → call Model Serving (TF Serving/Seldon) or use embedded model in stream job to score.
  - Use stream processors like Flink or Spark Structured Streaming (Flink typically lower-latency for per-event state).
  - Stateful streaming for windowed features (ex: rolling sum last 1 min) using Flink (RocksDB state backend) or Spark with careful state TTL and checkpointing.

**Online Feature Store**
- Low-latency KV store (Redis/Bigtable) with precomputed per-customer or per-card features (last_tx_amount, avg_amount_24h, velocity metrics).
- Served synchronously from scoring flow.

**Model Serving / Scoring**
- Option A: Model embedded in streaming job (low network overhead).
- Option B: Model server (TF Serving, Triton) with low-latency (<5ms) responses and autoscaling.
- Consider caching frequent model responses for similar inputs.

**Decision sink**
- If score > threshold → Block (emit to gateway), else Allow.
- Manual review queue for medium scores (write to DB + internal app).
- All decisions and features stored to raw event storage and decision store (OLTP DB) for audit.

**Storage**
- Raw events → compressed Parquet/Delta in object store (GCS/S3) with partitioning (date/region).
- Curated features & aggregates → Delta Lake / BigQuery for analytics & retraining.
- Long-term cold storage (nearline/archival) for cost savings.

**Model training & offline pipeline**
- Nightly batch job on Databricks: read curated data, train model, evaluate, push to model registry.
- Canary deploy / shadow testing in real-time: run new model in shadow mode before promoting.

**Observability & Ops**
- Metrics: ingestion TPS, end-to-end latency, scoring latency, model drift metrics, false positive rate, queue lengths, consumer lag.
- Tracing: distributed tracing (OpenTelemetry) to link event → decision.
- Alerts: threshold breaches, high lag, model performance degradation.
- Logging: audit logs (append-only) stored with retention policy.

**Security & Compliance**
- Tokenize card numbers at gateway, store only tokens.
- Encrypt data at rest & in transit, use KMS for keys.
- Role-based access and audit trails.
- PCI scope minimization: keep raw PANs out of cloud.

### Important Design Details, Capacity & Arithmetic (show you can do numbers)
- Assume average event size after compression = 1 KB. (Be explicit.)
- TPS = 50,000 → bytes/sec = 50,000 * 1 KB = 50,000 KB/s = 50 MB/s.
- Per day = 50 MB/s * 86,400 s = 4,320,000 MB ≈ 4,320 GB ≈ 4.32 TB/day.
- Per year ≈ 4.32 TB/day * 365 ≈ 1,576.8 TB ≈ 1.54 PB.
- Implication: one-year raw retention ~ 1.5 PB → choose compression, cold storage classes, partitioning, and lifecycle policies. If uncompressed or larger event size, numbers scale linearly.

**Kafka sizing**
- Peak ingress: 50k TPS, assume average partition throughput target e.g., 5k TPS/partition → requires ~10 partitions/minimum; for parallelism and headroom use 100s of partitions across brokers.
- Replication factor 3 for durability; plan for broker disk capacity & throughput.

**Latency**
- End-to-end budget (5 sec): ingestion (100–200 ms) + enrichment & feature fetch (50–500 ms depending cache) + model scoring (<50–200 ms) + write decision (<100 ms) + overhead. Use co-located caches and in-process scoring to meet 5 s.

### Key Trade-offs & Alternatives (what interviewer expects)
- **Embed model in stream job vs external model server**
  - Embed: lower network hops, simpler pipeline, but harder to update model frequently & larger job restart blast radius.
  - External serving: easier CI/CD for models, can scale independently, slight network overhead.
- **Kafka vs Pub/Sub**
  - Kafka: strong latency and retention control, self-hosted management cost.
  - Managed Pub/Sub: less ops overhead, but partition semantics differ.
- **Flink vs Spark Structured Streaming**
  - Flink: stronger event-time semantics & low-latency; better for sub-second stateful ops.
  - Spark: easier for teams already on Spark; newer versions have better support (AQE) but higher micro-batch latency.
- **Online Feature Store choice**
  - Redis: ultra-low latency but memory expensive.
  - Bigtable/Cassandra: good tradeoff for scale & cost, slightly higher latency.

### Failure Modes & Mitigations
- **Consumer lag / backpressure:** autoscale consumers; temporarily drop non-critical enrichment; have a degraded mode that uses only simple features.
- **Hot keys (one card producing large volume):** shard key with salted partitions; route heavy keys to dedicated consumers.
- **State store corruption:** use periodic checkpointing, backup and cross-region replication; manual failover plan.
- **Model drift:** continuous monitoring of model metrics + automated retraining triggers.

**Security & compliance**
- Tokenize PANs at ingress; remove raw PAN from processing if possible.
- Use envelope encryption with KMS rotated keys.
- Maintain audit trail & immutable logs for compliance review.

### Follow-up Interview Questions + Model Responses (typical)
- **Q:** How do you guarantee exactly-once?
  - **A:** Use Kafka transactions and idempotent sinks when possible; use dedupe keys (event id + transaction id) and idempotent upserts in sinks (e.g., Delta MERGE) to achieve effectively once semantics.
- **Q:** How to tune Kafka partitions?
  - **A:** Partition by card_token for locality; size partitions to keep per-partition throughput manageable (a few thousand tps). Monitor and rebalance; use partition count proportional to number of consumers * cores.
- **Q:** Cost optimizations?
  - **A:** Use compression, select cold storage for older raw data, compact files to optimal sizes, use spot instances for batch training.

#### System Design Evaluation Rubric (total 100)
- Clarified requirements & constraints: 10
- High-level architecture completeness: 25
- Scalability & capacity planning (incl. arithmetic): 20
- Reliability & fault tolerance strategies: 15
- Security & compliance handling: 10
- Trade-offs explanation & alternatives: 10
- Clarity & communication (diagrams, stepwise): 10
- **Pass threshold:** ≥ 75/100. Strong candidates score 85+.

---

# 🤝 Round 3 — Behavioral + Deep Technical Deep-Dive (45–60 minutes)

**Format:** 30–40 mins behavioral (STAR) + 20 mins deep technical follow-up on 1–2 projects.  
**Goal:** Assess ownership, communication, impact, and ability to explain technical choices clearly to non-technical stakeholders.

## Behavioral Questions with Model STAR Answers (you should rehearse 6–8 of these)

### 1) “Tell me about an end-to-end pipeline you built. What was your role and impact?”
**Model STAR answer — Example**

- **Situation:** Our nightly ETL for product analytics was running 3+ hours and costing the company ~$X/day. Business stakeholders needed faster daily reports.
- **Task:** I was the owner to redesign the nightly pipeline on Databricks to reduce runtime and cost while ensuring data quality and backward-compatibility.
- **Action:**
    - Profiling existing job using Spark UI to find shuffle hotspots.
    - Repartitioned data by event_date and user_id; replaced expensive groupByKey with reduceByKey equivalents.
    - Introduced broadcast joins for small dimension tables and incremental processing using watermarks and change detection.
    - Implemented Delta Lake MERGE for idempotent upserts and scheduled compaction to avoid small files.
    - Added unit tests and pre-deploy data validation checks (schema gating) and CI/CD via Databricks Repos + Terraform.
- **Result:** Reduced runtime from 3 hours → 40 minutes (≈87% faster), reduced cluster cost by ~60% per run, and reduced SLA incidents from 3/month to 0 over next quarter. Stakeholders got daily dashboards available 6 hours earlier.

**What to emphasize:** Numbers (before/after), your exact role, technical choices and why, how you communicated with stakeholders, follow-up monitoring.

### 2) “Describe a failure/outage you handled. How did you debug & prevent recurrence?”
**Model STAR**

- **Situation:** A daily job failed silently due to an upstream schema change, causing dashboards to show stale data.
- **Task:** Restore pipeline quickly and ensure it doesn’t recur.
- **Action:**
    - Ran quick impact analysis and backfilled missing day from raw logs.
    - Root cause: lack of schema validation on ingestion.
    - Implemented schema gating in ingestion and in preflight checks in Airflow (Composer); built a failing test in our CI for schema mismatches.
    - Added alerting on DAG run anomalies and a dataset freshness dashboard.
- **Result:** Backfill completed in 2 hours; no recurrence after fixes; introduced guarantees for future schema changes (contract tests + staged rollout).

### 3) “Tell me about a time you negotiated tradeoffs with stakeholders.”
Keep it concise: describe conflicting priorities (data freshness vs cost), what options you proposed, why you chose one, and how you measured success.

## Deep Technical Deep-Dive (pick 1–2 projects you actually did)
Interviewer will pick a project and ask you to explain & defend your choices. Prepare a 3-minute elevator summary + 10–15 minute in-depth technical discussion.

**Template structure to present:**
- One-line summary (business problem + scale).
- System diagram (ingest → raw → staging → curated → marts).
- Technical highlights (schema, data formats, partitioning, transformations).
- Failure modes & how you handled them (retries, idempotency).
- Observability & results (metrics improved).
- Code snippets / SQL / pseudocode for critical parts (MERGE for SCD2, Spark logic for sessionization).
- What you would do differently next time.

**Example of a deep question + model answer snippet**
> Interviewer: “Show me the MERGE logic you used for SCD Type 2 in Delta to keep history.”
**Model answer (pseudocode SQL):**
```sql
MERGE INTO analytics.customer_dim t
USING updates s
ON t.customer_id = s.customer_id AND t.is_current = true
WHEN MATCHED AND (
    t.email <> s.email OR t.name <> s.name OR t.addr <> s.addr
) THEN
  UPDATE SET is_current = false, effective_to = s.effective_from
WHEN NOT MATCHED THEN
  INSERT (customer_id, name, email, addr, effective_from, effective_to, is_current)
  VALUES (s.customer_id, s.name, s.email, s.addr, s.effective_from, NULL, true)
```
Explain idempotency, ordering, and index/partition choices.

#### Behavioral Evaluation Rubric (total 50)
- Clear storytelling and structure (STAR): 12
- Ownership & measurable impact: 12
- Technical depth & correctness in deep-dive: 12
- Communication & stakeholder handling: 8
- Reflection & learning: 6
- **Pass threshold:** ≥ 38/50

---

## Interviewer Scoring Summary & How to Run Mocks with Yourself or a Peer

- **Round 1 SQL:** /40
- **Round 1 PySpark:** /40
- **Round 2 System Design:** /100
- **Round 3 Behavioral:** /50

**Composite pass expectation (for mid-level product company interviews):**
- SQL + PySpark combined ≥ 60/80 (≥75%)
- System Design ≥ 70/100 (solid architecture + tradeoffs)
- Behavioral ≥ 38/50

**Top-tier candidate:** strong scores in all three, clear metrics and ownership, and excellent tradeoff reasoning.

---

## Quick Practice Tips to Get Maximum Improvement from This Set

- Timebox yourself exactly like the interview (use a timer). Practice both writing code and speaking aloud while you do it.
- Explain as you code — narrate choices, complexity, edge cases.
- Do one mock per week with a peer: one plays candidate, one plays interviewer (follow rubrics above).
- Record your explanations for the system design and behavioral answers; review to remove filler words and tighten technical clarity.
- Prepare 3 project one-pagers (one-liner + architecture diagram + 5 metrics). Use those in deep dives.

---

If you want, I can now:
- Turn the SQL + PySpark exercises into downloadable runnable notebooks (PySpark notebook + sample data) so you can run them on Databricks or locally.
- Or generate 8–12 timed mock interview sessions (question + expected answer + immediate feedback checklist) you can run against yourself.