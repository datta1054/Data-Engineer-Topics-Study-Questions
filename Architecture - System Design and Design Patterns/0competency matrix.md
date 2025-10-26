# 🧭 DATA ENGINEERING SYSTEM DESIGN & ARCHITECTURE — COMPETENCY MATRIX

### 🧩 Legend:

| Level | Meaning                                                                        |
| ----- | ------------------------------------------------------------------------------ |
| 🟩    | Expert – Can explain trade-offs, draw architectures, and defend design choices |
| 🟨    | Intermediate – Understands and can apply with help or examples                 |
| ⬜     | Learning – Aware of concept but cannot yet apply confidently                   |

---

## **1️⃣ FOUNDATIONS OF DISTRIBUTED DATA SYSTEMS**

| Topic                     | Description                                               | Level |
| ------------------------- | --------------------------------------------------------- | ----- |
| CAP & PACELC Theorem      | Understands availability-consistency trade-offs           | ⬜     |
| Partitioning & Sharding   | Knows partition key strategies, data skew, balancing      | ⬜     |
| Replication & Consistency | Sync vs Async replication; eventual vs strong consistency | ⬜     |
| Distributed Transactions  | Two-phase commit, idempotency, exactly-once semantics     | ⬜     |
| Fault Tolerance           | Retry, checkpointing, leader election, recovery           | ⬜     |
| Data Serialization        | Avro, Parquet, ORC – schema evolution handling            | ⬜     |

---

## **2️⃣ DATA PIPELINE SYSTEM DESIGN**

| Topic                              | Description                                        | Level |
| ---------------------------------- | -------------------------------------------------- | ----- |
| Batch vs Streaming Design          | Knows latency, SLA, cost differences               | ⬜     |
| Orchestration                      | Airflow, Composer, MWAA, Data Factory comparison   | ⬜     |
| Event-driven Design                | Pub/Sub, Kafka concepts, push vs pull patterns     | ⬜     |
| Change Data Capture (CDC)          | Debezium, Datastream, log-based replication        | ⬜     |
| Idempotent & Re-runnable Pipelines | Exactly-once, deduplication, atomic writes         | ⬜     |
| Error Handling                     | Dead-letter queues, retries, alerting              | ⬜     |
| Pipeline Metadata & Logging        | Tracking run status, lineage, operational metadata | ⬜     |

---

## **3️⃣ DATA STORAGE & PROCESSING**

| Topic                     | Description                                          | Level |
| ------------------------- | ---------------------------------------------------- | ----- |
| Storage Formats           | Delta, Iceberg, Hudi – versioning, ACID, metadata    | ⬜     |
| Storage Architecture      | Bronze/Silver/Gold layering, multi-zone lake         | ⬜     |
| Partitioning Strategy     | Choosing partition keys, avoiding small files        | ⬜     |
| Compaction & Optimization | Z-ordering, OPTIMIZE, vacuum, file tuning            | ⬜     |
| Compute Frameworks        | Spark, Dataflow, Flink, Beam – batch vs stream modes | ⬜     |
| Query Optimization        | Predicate pushdown, broadcast joins, caching         | ⬜     |
| Storage Comparisons       | S3 vs GCS vs ADLS – latency, consistency             | ⬜     |

---

## **4️⃣ STREAMING ARCHITECTURE & DESIGN PATTERNS**

| Topic                   | Description                                     | Level |
| ----------------------- | ----------------------------------------------- | ----- |
| Streaming Tools         | Kafka, Pub/Sub, Kinesis, Event Hubs internals   | ⬜     |
| Offsets & Checkpointing | Consumer group mechanics, stateful processing   | ⬜     |
| Windowing & Watermarks  | Late data handling, session vs tumbling windows | ⬜     |
| Lambda Architecture     | Batch + stream merge design                     | ⬜     |
| Kappa Architecture      | Stream-only design                              | ⬜     |
| Real-Time ETL           | Databricks Auto Loader, Structured Streaming    | ⬜     |
| Exactly-Once Processing | Checkpointing, deduplication, idempotency       | ⬜     |

---

## **5️⃣ DATA MODELING & STORAGE DESIGN**

| Topic                            | Description                                    | Level |
| -------------------------------- | ---------------------------------------------- | ----- |
| Dimensional Modeling             | Star, Snowflake schema design                  | ⬜     |
| Data Vault                       | Hubs, Links, Satellites concept                | ⬜     |
| SCD Management                   | Type 1–6 handling in ETL                       | ⬜     |
| Schema Evolution                 | Backward/forward compatibility in Parquet/Avro | ⬜     |
| Denormalization vs Normalization | OLTP vs OLAP design reasoning                  | ⬜     |
| Semi-Structured Data             | JSON, array handling in BigQuery/Databricks    | ⬜     |

---

## **6️⃣ DATA PLATFORM DESIGN & ARCHITECTURE**

| Topic                       | Description                                     | Level |
| --------------------------- | ----------------------------------------------- | ----- |
| Lakehouse Architecture      | Design using Databricks/BigQuery/Snowflake      | ⬜     |
| Data Mesh                   | Domain ownership, federated governance          | ⬜     |
| Multi-Cloud & Hybrid        | Cross-cloud ingestion and analytics             | ⬜     |
| Layered Zones               | Raw → Cleansed → Curated → Serving              | ⬜     |
| Medallion Pattern           | Bronze, Silver, Gold in Delta Lake              | ⬜     |
| CDC + Stream + Batch Hybrid | Merging multiple sources seamlessly             | ⬜     |
| Data Serving Layer          | BI extracts, materialized views, feature stores | ⬜     |

---

## **7️⃣ GOVERNANCE, SECURITY & OBSERVABILITY**

| Topic                     | Description                               | Level |
| ------------------------- | ----------------------------------------- | ----- |
| Lineage & Cataloging      | Unity Catalog, Dataplex, Purview, DataHub | ⬜     |
| Access Control            | IAM, ACLs, row/column-level security      | ⬜     |
| PII Handling              | Data masking, tokenization, encryption    | ⬜     |
| Data Quality Frameworks   | Great Expectations, Deequ, DQ rules       | ⬜     |
| Monitoring & Alerts       | Cloud Monitoring, Datadog, Prometheus     | ⬜     |
| Metadata-driven Pipelines | Config-based design, schema-driven ETL    | ⬜     |

---

## **8️⃣ COST, PERFORMANCE & SCALABILITY**

| Topic                  | Description                                     | Level |
| ---------------------- | ----------------------------------------------- | ----- |
| Query Optimization     | Caching, pruning, adaptive query execution      | ⬜     |
| Autoscaling Strategies | Cluster tuning, concurrency slots               | ⬜     |
| Storage Lifecycle      | Hot/cold tiering, retention policies            | ⬜     |
| Cost Governance        | Slot-based costing, query budgeting             | ⬜     |
| Throughput Tuning      | Parallelism, partitioning, shuffle optimization | ⬜     |
| SLA/SLO Design         | Latency targets, reliability metrics            | ⬜     |

---

## **9️⃣ CROSS-CLOUD ARCHITECTURE COMPARISON**

| Layer         | GCP                | AWS            | Azure             | Level |
| ------------- | ------------------ | -------------- | ----------------- | ----- |
| Storage       | GCS                | S3             | ADLS              | ⬜     |
| Orchestration | Cloud Composer     | MWAA           | Data Factory      | ⬜     |
| Processing    | Dataproc, Dataflow | EMR, Glue      | Synapse Pipelines | ⬜     |
| Warehouse     | BigQuery           | Redshift       | Synapse           | ⬜     |
| Governance    | Dataplex           | Lake Formation | Purview           | ⬜     |
| Streaming     | Pub/Sub            | Kinesis        | Event Hubs        | ⬜     |
| ML/AI         | Vertex AI          | SageMaker      | Azure ML          | ⬜     |

---

## **🔟 DESIGN PRACTICE SCENARIOS**

| Scenario                      | Objective                             | Level |
| ----------------------------- | ------------------------------------- | ----- |
| 1. Real-time Fraud Detection  | Streaming + CDC + Lakehouse           | ⬜     |
| 2. Incremental Batch ETL      | Delta merge, partitioned updates      | ⬜     |
| 3. Data Mesh for E-commerce   | Domain-driven ownership model         | ⬜     |
| 4. Cross-cloud Data Sharing   | GCS → S3 → Power BI / Tableau         | ⬜     |
| 5. CDC Replication            | Database → BigQuery/Delta Lake        | ⬜     |
| 6. Event-driven Ingestion     | Kafka → Databricks → BI layer         | ⬜     |
| 7. Governance & Lineage       | Unity Catalog + OpenLineage           | ⬜     |
| 8. Cost Optimization Strategy | Query pattern and storage cost tuning | ⬜     |

---

## ✅ PROGRESSION CHECKPOINTS

| Stage                              | Description                                                 | Goal           |
| ---------------------------------- | ----------------------------------------------------------- | -------------- |
| **Stage 1:** Core DE Foundations   | Master system design and pipeline patterns                  | 60% readiness  |
| **Stage 2:** Advanced Architecture | Confidently design multi-zone lakehouse & streaming systems | 80% readiness  |
| **Stage 3:** Platform Ownership    | Can discuss governance, cost, scaling & cross-cloud         | 90% readiness  |
| **Stage 4:** Architect Level       | Can whiteboard any architecture + trade-offs end-to-end     | 100% readiness |
