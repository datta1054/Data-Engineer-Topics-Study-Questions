
# � DATA ENGINEERING INTERVIEW CONCEPT MAP & PREPARATION GUIDE

## 1️⃣ SQL & Data Query Optimization
**Depth:** Core → Advanced  
**Priority:** ⭐⭐⭐⭐ (Must-Know)

### Core
- SQL fundamentals (SELECT, WHERE, GROUP BY, ORDER BY)
- Aggregate functions and HAVING
- INNER vs OUTER joins  
_Approach: Revisit SQLZoo / LeetCode SQL basics._

### Intermediate
- Window functions (ROW_NUMBER, RANK, LAG/LEAD, NTILE)
- Analytical queries (running totals, moving averages)
- CTEs and subqueries for complex logic  
_Approach: Solve 2–3 advanced SQL LeetCode problems daily (e.g., “Nth highest salary”, “User retention rate”)._

### Advanced
- Query performance tuning:
	- Understand query plans (EXPLAIN / EXPLAIN ANALYZE)
	- Indexes: clustered vs non-clustered, when not to use
	- Join order, predicate pushdown, avoiding cross joins
	- BigQuery/Databricks optimization: partition pruning, broadcast joins, caching
	- Data skew & shuffle optimization in SparkSQL  
_Approach: Profile queries on Databricks/BigQuery; analyze explain plans for real queries._

---

## 2️⃣ Python for Data Engineering
**Depth:** Core → Advanced  
**Priority:** ⭐⭐⭐⭐ (Must-Know)

### Core
- Python data structures (list, dict, set, tuple)
- Loops, list comprehensions, lambda, map/filter/reduce
- Error handling, logging, config management

### Intermediate
- Pandas fundamentals: indexing, apply(), groupby(), merges, handling missing data
- PySpark transformations: map, filter, reduceByKey, joins, groupByKey vs aggregateByKey
- Functional programming concepts (immutability, lazy evaluation)
- Performance optimization: vectorization, avoiding loops, broadcast variables in Spark  
_Approach: Build a small data cleaning/aggregation project using both pandas and PySpark._

### Advanced
- Writing modular, testable code (pytest basics)
- Python packaging and virtual environments
- Memory profiling (e.g., using tracemalloc, line_profiler)  
_Approach: Refactor one of your existing notebooks into a package with tests._

---

## 3️⃣ Cloud Platforms
**Depth:** Core → Intermediate  
**Priority:** ⭐⭐⭐⭐ (Must-Know for GCP, ⭐⭐ for others)

### GCP (Primary Focus)
- BigQuery: partitioning, clustering, table types, query cost optimization
- Dataflow: Apache Beam concepts, windowing, triggers, DoFns
- Dataproc: Spark on GCP, job orchestration
- Composer (Airflow): DAGs, dependencies, retries, XCom
- IAM: roles, service accounts, least privilege
- Monitoring: Stackdriver, alerts, budgets  
_Approach: Review real pipelines; practice cost & performance tuning scenarios._

### AWS (Awareness Level)
- S3 lifecycle policies, Redshift architecture, Glue crawlers, Lambda triggers  
_Approach: Learn equivalence mapping from GCP → AWS._

### Azure (Awareness Level)
- ADLS, Synapse pipelines, ADF vs Data Factory, Azure Databricks integration  
_Approach: Focus on similarities for portability discussions._

### Cross-Cloud Design Patterns
- Data lake architecture, cost/performance trade-offs
- Multi-cloud interoperability, Terraform basics  
_Approach: Prepare talking points for “how would you migrate a pipeline from GCP to AWS?”_

---

## 4️⃣ Databricks & Spark Core
**Depth:** Core → Advanced  
**Priority:** ⭐⭐⭐⭐⭐ (Critical for your profile)

### Core
- Spark architecture: driver, executors, cluster manager
- Transformations (map, filter, join, groupBy) vs actions (collect, count, save)
- Lazy evaluation, DAGs, wide vs narrow transformations

### Intermediate
- Performance tuning:
	- Partitioning, caching, broadcast joins
	- Shuffle reduction, AQE (Adaptive Query Execution)
- Delta Lake fundamentals: ACID tables, time travel, Z-order, vacuuming
- Unity Catalog: access control, lineage, catalog hierarchy  
_Approach: Re-implement one of your pipelines focusing on shuffle & AQE optimization._

### Advanced
- CI/CD with Databricks Repos, notebooks → jobs → workflows
- Multi-environment deployment (dev/stage/prod)
- Cluster sizing, cost optimization  
_Approach: Practice designing a data pipeline deployment strategy end-to-end._

---

## 5️⃣ Data Modeling & Warehousing
**Depth:** Core → Advanced  
**Priority:** ⭐⭐⭐⭐ (Must-Know)

### Core
- OLTP vs OLAP
- Normalization vs denormalization
- Star vs Snowflake schema

### Intermediate
- Fact & Dimension tables, surrogate keys
- Slowly Changing Dimensions (SCD Type 1, 2)
- Schema evolution in Delta/BigQuery

### Advanced
- Data Vault modeling
- Kimball vs Inmon philosophies
- Designing data marts for analytics use cases  
_Approach: Design 1–2 end-to-end warehouse schemas for business cases (sales, marketing, IoT)._

---

## 6️⃣ Data Architecture & Pipelines
**Depth:** Intermediate → Advanced  
**Priority:** ⭐⭐⭐⭐⭐ (Core interview area)

### Core
- ETL vs ELT, orchestration with Airflow/Composer
- Data ingestion (batch, streaming, CDC)
- Idempotency, retries, checkpointing

### Intermediate
- Observability: logging, metrics, lineage
- Cost optimization, right-sizing clusters, storage tiering
- Data quality checks (Great Expectations / dbt tests)

### Advanced
- Event-driven architectures (Pub/Sub, Kafka)
- Schema evolution handling
- Governance (Unity Catalog, IAM, data masking)  
_Approach: Create a pipeline design doc for one project showing all these layers._

---

## 7️⃣ System Design for Data Engineers
**Depth:** Intermediate → Advanced  
**Priority:** ⭐⭐⭐⭐⭐ (High-value round)

### Intermediate
- Data lake vs warehouse vs lakehouse
- Ingestion patterns: batch, streaming, micro-batch
- Partitioning & file format strategies (Parquet, ORC, Avro)

### Advanced
- Designing scalable platforms (ingest → store → transform → serve)
- Real-time analytics (Kafka + Spark Streaming + Delta + BI)
- Schema registry, metadata management, data contracts  
_Approach: Practice “whiteboard design” problems: e.g. “Design a clickstream analytics platform” or “Design a pipeline for real-time fraud detection”._

---

## 8️⃣ DSA (for Data Engineers)
**Depth:** Core  
**Priority:** ⭐⭐⭐ (Must-Know basics)

### Core
- Arrays, dictionaries, strings manipulations
- Sorting, searching, prefix sums
- Hashmaps & sets usage patterns

### Applied Focus
- SQL problem solving
- Data aggregation transformations (grouping, joining, filtering)  
_Approach: Do 2–3 Python + SQL LeetCode problems daily. Focus on clarity, not complexity._

---

## 9️⃣ Behavioral & Project Explanation
**Depth:** Core → Intermediate  
**Priority:** ⭐⭐⭐⭐ (Often a differentiator)

### Core
- STAR method (Situation, Task, Action, Result)
- Discuss 2–3 projects clearly:
	- Problem
	- Architecture (tools, design choices)
	- Impact (latency, cost, scalability)

### Intermediate
- Stakeholder communication, tradeoff decisions
- Ownership stories: debugging, optimization, or cost-saving wins  
_Approach: Record yourself explaining a project in 3 minutes. Refine clarity and technical depth._

# ⚙️ Preparation Strategy by Section

| Area | Daily/Weekly Action Plan |
|------|---------------------------|
| SQL | 30–45 mins daily LeetCode + query plan analysis |
| Python/PySpark | 3 mini coding tasks/week; review Spark UI for jobs |
| GCP & Cloud | 1 service deep-dive/week (BigQuery, Dataflow, Composer) |
| Databricks | Optimize one real job; review lineage, cluster config |
| Modeling/Architecture | Design 1 data system/week on whiteboard |
| System Design | Watch + practice design Q&A (YouTube + mock interviews) |
| Behavioral | Maintain project story cards + STAR templates |




---

## Deep Dive — Complete, Interview-Ready Expansion of Every Concept

### 1) SQL & Data Query Optimization
**Priority:** ★★★★☆ (Must-know)

**Why it matters:** SQL is the universal language for analytics. Interviewers assess correctness, expressiveness (window functions, CTEs), and your ability to make queries perform at scale (read/compute cost, latency).

#### Overview / Mental Model
SQL = declarative specification of what you want; the DB/engine decides how. Interviews test both: can you express complex analytics concisely, and can you reason about execution plans/optimizations?

For large tables, focus on I/O (bytes scanned), shuffle/redistribution (in distributed engines), and cardinality (how many rows each operator sees).

#### Core (Must-Know)
- SELECT/WHERE/GROUP BY/ORDER BY basics, joins (INNER/LEFT/RIGHT/FULL), basic aggregates (SUM/COUNT/AVG), simple subqueries, CTE usage.
- Understand when to use CTE vs temp table (CTE is logical; some engines materialize it).
- Basic window functions: `ROW_NUMBER()`, `RANK()`, `LAG()/LEAD()` and simple frames.
- **Practice:** 20–30 classic SQL problems (LeetCode/HackerRank/SQLZoo): joins, group-by puzzles, simple window questions.

#### Intermediate (Must-Know)
- Window functions with frames (`ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`), `FIRST_VALUE`, `LAST_VALUE`, `NTILE`, `OVER(PARTITION BY ...)`.
- Analytical SQL: running totals, moving averages, cohort/retention analysis, median/percentiles (approx or exact).
- `GROUPING SETS`, `ROLLUP`, `CUBE` (for multi-dimensional aggregations).
- `EXISTS` vs `IN` vs correlated subqueries — rewrite correlated subqueries as joins where appropriate.
- Common table expressions (recursive CTEs) for hierarchical queries.
- `CASE` expressions for bucketization.
- Basic use of JSON/ARRAY functions for semi-structured data.
- **Practice:** Build cohort analysis queries, retention curves, sessionization (group events by session window using SQL).

#### Advanced (Must-Know for Mid-Level)
- Execution plan interpretation: find scan costs, identify heavy operators (large sorts, shuffles, look for “distributed shuffle” / “exchange” steps).
- Join algorithms: nested loop, hash join, sort-merge; when each is used; when broadcast/hash join is beneficial.
- Indexing concepts: B-tree vs bitmap, composite indexes, covering indexes, index selectivity and histograms — when an index helps vs when it harms (updates, wide tables).
- Partitioning & clustering: partition pruning (date-based partitions), clustering columns to reduce scanned bytes (BigQuery/Redshift/Databricks).
- Materialized views and pre-aggregations — when and how to maintain them.
- Predicate pushdown: design queries to enable filter pushdown (push filters before heavy joins).
- Avoiding data skew & shuffle explosion: detect skew (one partition very large), fix via salting or broadcast.
- Cost-based optimization concepts: statistics/cardinalities matter; stale stats → bad plans.
- Engine-specific optimizations: BigQuery partitioning/clustering, query caching; SparkSQL: `broadcast()` joins, `.explain()`, `spark.sql.adaptive.enabled`.
- **Practice:** Take a slow query from a dataset, run `EXPLAIN`/`EXPLAIN ANALYZE` (or engine UI), identify hot spots, and iteratively improve. Document the before/after plan and metrics.

#### Practical Heuristics & Interview Talking Points
- Always show you checked data size and cardinality. “This table has 1B rows; broadcasting it would be bad unless aggregated first.”
- Explain why you’d choose partitioning column (query patterns, cardinality).
- When asked to optimize, propose both query rewrite and infra changes (indexes, partitioning, slot reservations).

#### Common Interview Tasks / Sample Questions
- “Explain why this query is slow (EXPLAIN text shown). Where do you focus?” — Talk about disk reads, shuffle, high-cardinality group-by, missing filters.
- “Write a query to compute rolling 7-day active users and explain how you’d optimize it for daily runs.”
- “How would you deduplicate 100M rows keeping the latest record per key?”

---

### 2) Python for Data Engineering
**Priority:** ★★★★☆ (Must-know)

#### Overview / Mental Model
Python is the glue: ingestion, transformations, orchestration scripts, unit tests. You must write readable, efficient, testable code and know when to move from single-node (pandas) to distributed (PySpark).

#### Core (Must-Know)
- Python fundamentals (lists, dicts, sets, tuples), comprehension, functions, exceptions, context managers (`with`), logging.
- OOP basics and modules; using virtual environments and `requirements.txt`/`pyproject.toml`.
- Basic debugging, reading stack traces.
- **Practice:** small functions: data normalization, parsing CSV rows, validating schema.

#### Intermediate (Must-Know)
- Pandas: indexing, merge, groupby, apply vs vectorized ops, pivot/melt, handling nulls, dtype optimization (categorical), memory usage, chunked processing with `read_csv(..., chunksize=...)`.
- PySpark DataFrame API: transformations vs actions; filter, select, withColumn, groupBy, joins; avoid `collect()` on large tables; `.persist()` / `.cache()`.
- UDFs: Python UDFs vs vectorized pandas UDFs; overhead of serialization; prefer built-in SQL functions.
- Functional programming: immutability patterns, pure functions, map, filter, reduce.
- Error handling & retries in production scripts (exponential backoff, idempotency).
- Testing: pytest basics, mocking external IO, writing small testable units for ETL logic.
- **Practice:** convert an existing pandas pipeline to a PySpark job; benchmark memory/time.

#### Advanced (Nice-to-Have but Valuable)
- Package and release code (`setuptools` / `poetry`), writing CLI with `argparse` or `click`.
- Profiling / performance tuning: `cProfile`, `line_profiler`, `memory_profiler`, `tracemalloc`. Profile to find hotspots (string ops, copy-on-write).
- Type annotations (`mypy`) and contract-like validation (`pydantic`).
- CI for Python jobs (unit tests, linters, code-style enforcement).

#### Practical Tips & Pitfalls
- Avoid for loops over rows in pandas; prefer vectorized ops or `groupby.apply` carefully.
- Avoid Python UDFs in Spark when SQL functions can do the job — UDFs serialize and kill optimization.
- Use `broadcast()` for small table joins in Spark; for PySpark DataFrame API: `broadcast(small_df)`.
- Test transformation functions locally with tiny datasets before deploying.

#### Interview Tasks / Sample Questions
- “Write a Python function to dedupe rows by keys keeping the latest timestamp.” (Show O(N) approach with dict or pandas `drop_duplicates`.)
- “When would you use a pandas UDF vs normal UDF in Spark? What’s the performance implication?”

---

### 3) Cloud Platforms — GCP (Deep), AWS & Azure (Comparative)
**Priority:** ★★★★☆ (GCP must-know; AWS/Azure awareness)

#### GCP — Core Services & Interview Focus (Must-Know)
- BigQuery: columnar, serverless, scanning-cost model. Key concepts:
	- Partitioning (ingestion-time vs column partitions) — reduces bytes scanned.
	- Clustering — colocates similar values to reduce read.
	- Table types: partitioned, clustered, materialized views, external tables.
	- Cost control: minimize scanned bytes; use SELECT * carefully.
- Dataflow (Apache Beam):
	- Programming model: PCollections, ParDo, DoFn, windowing and triggers, watermarks.
	- Streaming semantics: event time vs processing time, late data, allowed lateness.
- Dataproc: managed Spark/Hadoop — use for full-control Spark clusters.
- Composer (Cloud Composer): managed Airflow — DAGs, operators, XCom, sensors, environment separation.
- GCS: object storage, lifecycle policies, uniform bucket-level vs fine-grained IAM.
- IAM & Security: service accounts, workload identity, least privilege.
- Networking & Private IPs: VPC, VPC-SC (Service Controls), private endpoints for BigQuery? (discuss VPC-SC design).
- Monitoring/Observability: Cloud Monitoring / Logging (Stackdriver); set alerts for job failures, SLA misses.
- **Interview emphasis:** tie tools to use-cases (e.g., Dataflow for streaming with event-time processing; BigQuery for ad-hoc analytics and massive joins if cost is acceptable).

#### AWS & Azure (Comparative, Nice-to-Have)
- **AWS equivalents:**
	- GCS ↔ S3, BigQuery ↔ Redshift/Redshift Spectrum/ Athena, Dataflow ↔ Glue/EMR + Kinesis, Composer ↔ Managed Airflow / MWAA.
- **Azure equivalents:**
	- ADLS Gen2 ↔ GCS/S3, Synapse ↔ BigQuery/Redshift, ADF (Data Factory) for orchestration, Azure Databricks similar to Databricks on AWS/GCP.

#### Cross-Cloud Portability Patterns (Must-Know)
- Use open formats (Parquet/Avro/ORC), schema evolution carefully (Avro/Protobuf for streaming).
- Decouple compute vs storage (object store + ephemeral compute).
- IaC: Terraform modules for cloud-agnostic infra.
- Use standard orchestration (Airflow) and connector abstraction.

#### Practical Design & Troubleshooting Checklist
- For cost surprise: check partitioning/clustering; queries scanning entire table; repeated export/import; unnecessary materialized views.
- For performance: adjust BigQuery slot reservations or Dataproc cluster sizing; examine Dataflow parallelism and autoscaling.
- Security: rotate service account keys, prefer Workload Identity / IAM roles.

#### Interview Prompts
- “Design a near-real-time clickstream ingestion using GCP, with low-latency dashboards.”
- “How do you control BigQuery costs for an analytics team?”

---

### 4) Databricks & Spark Core
**Priority:** ★★★★★ (Critical — your core area)

#### Spark Internals & Architecture (Deep)
- Logical → Physical → Execution: Catalysts optimizer builds logical plan → optimizations produce physical plan → Tungsten/Execution engine executes tasks.
- Driver vs Executor: driver coordinates tasks; executors run tasks. Executors hold partitions in memory/files on disk.
- Stages & Tasks: Wide transformations (shuffle required) create stage boundaries; narrow transformations do not.
- Shuffle mechanics: map side writes files; reduce side reads; network/disk heavy; shuffles are common performance pain points.
- Serialization: prefer Kryo where possible for speed & size.

#### Performance Tuning Concepts (Must-Know)
- Partitions: set meaningful partition counts (neither too many tiny nor too few huge partitions). Use `repartition()` to increase parallelism, `coalesce()` to decrease without shuffle.
- Caching & persistence: cache intermediate results when reused — but free memory when not needed.
- Broadcast joins: `broadcast(small_df)` to avoid shuffle for a small table.
- AQE (Adaptive Query Execution): auto-adjust join strategies and shuffle partitions at runtime.
- Shuffle partitions: tune `spark.sql.shuffle.partitions` appropriate to cluster cores.
- Avoid `groupByKey`: use `reduceByKey` / `aggregateByKey` to reduce network traffic.
- Skew mitigation: salting, splitting large keys into sub-keys.

#### Delta Lake & Lakehouse (Must-Know)
- ACID transactions via transaction log (commit/txn log).
- MERGE for upserts (SCD, dedupe) — atomic and idempotent.
- Time travel (query older versions) & VACUUM (cleanup).
- Compaction (`OPTIMIZE`) and Z-ordering to co-locate related rows for faster selective reads.
- Small-file problem: cause of many small files; fix with compaction jobs, right-sized output files (target 128MB–1GB).
- Schema enforcement & evolution: `mergeSchema` options and how to handle incompatible changes.

#### Databricks Platform Specifics
- Unity Catalog: governance, fine-grained access, catalogs/schemas/tables separation.
- Jobs & Workflows: Jobs API, task dependencies, job clusters vs all-purpose clusters.
- CI/CD: Repos + Git, Databricks CLI, Terraform provider, workspace/workflow deployments.
- Cluster sizing & cost: choose instance types, autoscaling vs static clusters, spot instances where acceptable.

#### Practical Debugging Using Spark UI
- Examine DAG visualizations: stages with straggler tasks (long-running), skewed tasks count, shuffle read/write sizes, GC time.
- Look at executor logs for serialization errors, OutOfMemory (OOM), or TaskKilled messages.

#### Interview Tasks & Sample Prompts
- “Explain how Spark executes a join between two large tables. How would you optimize it?”
- “Design an SCD Type 2 ingestion using Delta Lake and Databricks. Show code snippets or pseudocode.”
- “A job runs fine locally but OOMs on the cluster — what do you check?”

---

### 5) Data Modeling & Warehousing
**Priority:** ★★★★☆ (Must-know)

#### Core Modeling Concepts
- OLTP vs OLAP: OLTP optimized for writes/transactions (normalization), OLAP optimized for reads/analytics (denormalization).
- Star schema: fact table (event metrics) + dimension tables (denormalized descriptive attributes). Grain must be well-defined.
- Snowflake schema: normalized dimensions (less redundancy).
- Fact table design: grain, foreign keys, measures (facts), surrogate keys.
- Slowly Changing Dimensions (SCD):
	- Type 1: overwrite.
	- Type 2: historical rows with effective dates, current flag.
	- Type 3: limited history (columns for previous value).
- Surrogate vs natural keys: use surrogate keys for stability and performance.

#### Advanced Modeling Patterns
- Data Vault: hub (business keys), link (relationships), satellite (attributes, history). Pros: auditability and flexibility; cons: complexity for querying.
- Denormalization tradeoffs: faster reads vs increased storage and complexity for updates.
- Wide table vs melt: sometimes wide denormalized tables speed up analytics; for many dimensions, consider nested structures or star schema.

#### Implementation & Schema Evolution
- Use Parquet/Delta for columnar, efficient analytics.
- Plan schema evolution: add-only fields preferred; breaking changes need migration strategies (backfill, compatibility layers).
- Materialized views & pre-aggregations for expensive roll-ups.

#### Example Design - E-commerce
- Fact: orders with grain = one order line per order_item_id. Dimensions: customer, product, time, store.
- For SCD Type 2 customer_dim: maintain effective_from, effective_to, is_current.

#### Interview Prompts
- “Design a reporting schema for daily sales with product hierarchy and promotions.” (Walk through grain, dims, SCD choices.)
- “How would you model many-to-many relationships (e.g., products ↔ tags) in a star schema?”

---

### 6) Data Architecture & Pipelines
**Priority:** ★★★★★ (High-value)

#### Core Patterns & Building Blocks
- Ingestion patterns: batch pull (cron jobs), push-based streaming (events via Pub/Sub/Kafka), CDC (Debezium/Kafka) for DB changes.
- Processing patterns: ETL (transform then load) vs ELT (load then transform) — ELT increasingly common with powerful warehouses (BigQuery).
- Orchestration: Airflow/Composer for DAG-based scheduling; best practices: modular tasks, idempotency, retries, SLA sensors.
- Idempotency & exactly-once: design sinks for idempotent writes (upserts, transactional writes) or dedupe with unique constraints.

#### Streaming Concepts (Must-Know)
- Event time vs processing time; watermarks; windowing and late data handling.
- Checkpointing & stateful processing: ensure fault tolerance (Spark Structured Streaming checkpoint directories).
- Backpressure & throughput control: consumer parallelism, batching, acknowledgements.

#### Observability & Data Quality (Must-Know)
- Metrics & logs: job durations, success/failure counts, data freshness lag, throughput.
- Lineage & metadata: OpenLineage / Data Catalog / Unity Catalog — helps debugging root cause.
- Data quality frameworks: Great Expectations, Deequ; run quality checks at ingestion and after key transforms.
- Alerting & SLA: alerts for delayed runs, high error rates, schema changes.

#### Governance & Security
- Access control (role-based), PII detection and masking, encryption at rest and in transit, key management (KMS), auditing.

#### Cost Optimization
- Store cold data in lower-cost classes; partitioning and clustering to reduce scan costs; right-size clusters; use spot/preemptible nodes for batch processing.

#### Practical Patterns to Prepare
- End-to-end pipeline: ingest events → raw landing (immutable) → staging normalized zone → curated zone (denormalized) → serving marts.
- Bake-in monitoring and lineage from day 1. Include retry policies and granular failure notifications.

#### Interview Prompts
- “Design a resilient ETL pipeline to process 1M events/min with near-real-time dashboards.”
- “How would you guarantee no-duplicates when ingesting from distributed producers?”

---

### 7) System Design for Data Engineers
**Priority:** ★★★★★ (Critical interview round)

#### Approach to Answering Design Questions
- Clarify requirements (throughput, latency, retention, consumers, consistency).
- Propose high-level architecture (ingest → storage → processing → serving).
- Break down component choices and justify (cost/perf/complexity).
- Address scaling, bottlenecks, failure modes, security, and monitoring.
- Trade-offs and alternatives.

#### Typical Building Blocks & Trade-Offs
- Ingestion: Pub/Sub/Kafka for streaming (low latency, ordering guarantees); batch ingestion for bulk loads.
- Storage:
	- Object store (GCS/S3) for raw + parquet for analytics.
	- Columnar stores (BigQuery/Redshift) for analytics queries.
	- Key-value / wide-column (Bigtable/Cassandra) for low-latency lookups.
- Processing: Beam/Dataflow or Spark Structured Streaming for stream; Spark/Dataproc for batch ELT.
- Serving: aggregated tables in BigQuery, materialized pre-aggregates, or OLAP stores.

#### Large-Scale Topics (Advanced)
- Partitioning & sharding strategies: date-based partitioning vs hash sharding — choose by query patterns & cardinality.
- Schema registry & contract testing: ensure producers and consumers agree on schema; use versioning and backward-compatible changes.
- Event-driven architectures: idempotency + out-of-order handling, event sourcing patterns.
- Throughput & latency planning: calculate write QPS, message size, retention → plan storage and compute.
- Backfilling strategy: how to reprocess historical data without impacting live systems (use isolated compute, deduplicate).

#### Practical Examples to Practice
- Design a clickstream pipeline: Pub/Sub → Dataflow (sessionization, enrich) → BigQuery for nightly OLAP + Bigtable for real-time user profiles.
- Design fraud detection: streaming ingest → feature store updates → scoring microservice → alerting pipeline.

#### Interview Drills
- “Design a photo-sharing app’s analytics pipeline (track uploads, views, likes) for 10M users.”
- “How to handle schema changes in a streaming JSON event pipeline?”

---

### 8) DSA (Data Structures & Algorithms) for Data Engineers
**Priority:** ★★★☆☆ (Must-know basics)

#### Focus & Scope
Data engineers rarely need competitive programming depth. Interviewers expect practical algorithmic thinking for ETL/transforms, deduping, aggregations, and streaming window logic.

#### Core Topics (Must-Know)
- Arrays & strings: scanning, two-pointer, sliding window (useful for session/windowing logic).
- Dictionaries / hashmaps / sets: frequency counters, joins on keys in memory, deduplication, caching.
- Sorting & searching: using built-in sorts but know O(n log n) tradeoffs; using sorting vs hashing for joins/merges.
- Prefix sums / cumulative aggregations: efficient aggregations over ranges.
- Simple graph/tree reasoning occasionally (e.g., DAGs in orchestration).

#### Practical Patterns
- Use hashing for O(1) lookups when deduping or joining small in-memory datasets.
- Streaming approximations: Bloom filters / HyperLogLog for cardinality (nice-to-have to mention).
- Complexity analysis for code: be ready to explain time/space complexity.

#### Interview Tasks
- “Given a stream of events, keep top-k users by activity in last hour” — explain sliding window + heap approach or approximate solutions (space/accuracy trade-off).

---

### 9) Behavioral & Project Explanation
**Priority:** ★★★★☆ (Often differentiator)

#### Structure — STAR but Technical
- **Situation:** context and why it mattered (scale, SLA, cost).
- **Task:** your responsibility and success criteria.
- **Action:** technical decisions, architecture, trade-offs, code/process changes — this is the longest bit.
- **Result:** measurable outcome: latency reduction, cost savings, reliability improvements; include metrics.

#### How to Explain an End-to-End Pipeline
Always be ready to explain for 2–3 of your best projects:
- Business problem & stakeholders.
- Data sources and formats.
- Ingestion mechanism and frequency.
- Storage format and partitioning schema.
- Key transformations and why they were chosen (joins, aggregations, windowing).
- Error handling, retries, idempotency.
- Observability: logs, metrics, alerts.
- Deployment: CI/CD, monitoring, rollback plan.
- Outcome: numbers and user impact.

#### Common Behavioral Themes & Sample Bullets
- **Ownership:** “I owned the migration of the nightly pipeline to Databricks — reduced runtime from 3 hours to 45 minutes by partition tuning and broadcast joins.”
- **Trade-off decisions:** “We chose ELT in BigQuery because of high aggregation needs and to avoid compute-heavy writes on the source DB.”
- **Failure & learning:** “A job failed after a schema change; I implemented schema validation and gating tests to prevent regressions.”

#### Mock Prep & Delivery Tips
- Write short one-paragraph ‘elevator’ summary + 3-minute technical deep dive for each project.
- Practice whiteboard diagrams: data flow arrows, components labeled with service names and reasons.
- Anticipate follow-ups: “Why not X?” or “How did you handle retries?” — have direct answers.

#### Study Plan — Concrete, 8-Week Ramp (Focused & Practical)
- **Weeks 1–2 (SQL + Python):** daily SQL problems (45 min), rewrite queries & EXPLAIN plans (30 min). Convert pandas scripts to PySpark or vice-versa.
- **Weeks 3–4 (Databricks & Spark internals):** deep read of Spark execution, tune real jobs on Databricks, practice MERGE/SCD patterns.
- **Weeks 5 (GCP & BigQuery + Dataflow):** build one sample ETL in Dataflow; cost-optimize a BigQuery dataset (partitioning/clustering).
- **Week 6 (Architecture & Modeling):** design 3 systems on whiteboard (clickstream, billing, fraud). Model schemas for each.
- **Week 7 (System design mocks):** 3 mock interviews with whiteboard answers; improve based on feedback.
- **Week 8 (Behavioral + polish):** prepare STAR stories, project one-pagers, rehearse with peers.

#### Quick Checklists & Cheat-Sheets (Copyable)
**SQL Optimization Checklist**
- Check table sizes & cardinalities.
- Look at EXPLAIN plan → find large scans, sorts, shuffles.
- Add selective filters earlier.
- Use partition pruning & clustering.
- Broadcast small tables for joins.
- Avoid SELECT *.
- Consider materialized views for expensive repeated aggregations.

**Spark Tuning Checklist**
- Check Spark UI for stage/task hot spots.
- Tune `spark.sql.shuffle.partitions` to match cluster cores.
- Use broadcast joins for small dimension tables.
- Cache reused dataframes; unpersist when done.
- Resolve skew with salting.
- Target Parquet file sizes ~128MB–1GB (for efficient read).

**Databricks + Delta Best Practices**
- Use bronze/silver/gold zones.
- Use MERGE for upserts and SCDs.
- Run compaction/OPTIMIZE regularly.
- Track lineage via Unity Catalog or third-party tools.
- Use workspace Git integration + jobs for CI/CD.

**Concrete Interview Prep Exercises (Pick and Do)**
- **SQL:** Build a retention/cohort query for event logs and optimize with clustering/partitioning. Explain your EXPLAIN plan.
- **PySpark:** Implement sessionization (group events into sessions by user and timeout) with Spark Structured Streaming. Add checkpointing and handle late events.
- **Databricks:** Convert a nightly job to use adaptive query execution and broadcast joins to drop runtime by >50%. Document before/after metrics.
- **System design:** Whiteboard a real-time personalization pipeline: components, data model, failure modes, and cost estimate.
- **Behavioral:** Prepare 5 STAR stories: ownership, failure, scale, impact, team conflict.