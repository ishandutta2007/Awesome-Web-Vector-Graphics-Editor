# Awesome-Web-Vector-Graphics-Editor

## Top ML Feature Stores Ecosystem

**Curated List of SaaS Products & Open-Source GitHub Projects**  
*Focused on Point-in-Time Correctness, Online/Offline Serving & Training-Serving Skew Prevention*  
**Last updated: March 2026**

This repository tracks notable **SaaS platforms** and **open-source projects** for **ML Feature Stores**. These tools solve a specific set of production ML failure modes: training-serving skew (feature logic computed differently at training vs. inference), feature duplication (ten teams computing "customer 30-day spend" ten different ways), and online/offline latency mismatch (aggregations that can't be computed at request time without pre-materialization).

**Examples** include Feast, Hopsworks, Tecton, Databricks Feature Store, AWS SageMaker Feature Store, and Google Vertex AI Feature Store (the category leaders).

**Open-source emphasis**: This section is heavily expanded with every major active project for self-hosting, cloud-agnostic feature serving, and transparent point-in-time joins — ideal for teams wanting feature consistency without per-feature managed pricing, and willing to own the operational burden that comes with self-hosting.

Contributions welcome! Open a PR to add/update entries. Keep descriptions factual and link to official sites.

## Table of Contents
- [SaaS/Hosted Platforms](#saas-hosted-platforms)
- [Open-Source GitHub Projects](#open-source-github-projects)
- [How to Contribute](#how-to-contribute)
- [Disclaimer](#disclaimer)

## SaaS/Hosted Platforms

- **Tecton** — **The managed, real-time platform built by the creators of Feast.** Enterprise SaaS platform founded by the original Feast creators [citation:8]. **Streaming is first-class** with native Kafka/Kinesis integration and a meaningfully different latency profile than Feast's batch-oriented default. **Feature monitoring is built in** — freshness alerts, data quality checks, and drift detection. **Vendor-reported sub-10ms p99 serving latency** positions it for fraud detection and real-time personalization. **Best for enterprises needing rapid time-to-production and guaranteed SLAs**, where the cost of a managed platform is justified by reduced operational risk.

- **Databricks Feature Store** — **Integrated into the Lakehouse with Unity Catalog governance.** **Feature Views** (Public Preview) define features declaratively and let Databricks compute and manage the pipelines; **Feature Tables** are Delta tables you populate yourself [citation:4]. **Point-in-time correction** builds training datasets reflecting feature values at label-event time, and models automatically track lineage to features used in training [citation:4]. **Online serving via Lakebase** with automatic scaling and scale-to-zero [citation:11]. **Best for organizations already standardized on Databricks** wanting feature consistency without a separate platform.

- **AWS SageMaker Feature Store** — **The cloud-native option for AWS-first stacks.** Tight integration with SageMaker training and inference, with online and offline stores managed by AWS. Feature groups, Studio Classic UI, and Boto3 SDK support for creating and sharing features [citation:5]. **Best for teams whose entire ML stack already lives in AWS** and where minimizing integration friction matters more than cross-cloud portability.

- **Google Vertex AI Feature Store** — **GCP's managed feature store with BigQuery source integration.** **Feature Views** define features declaratively from BigQuery tables or views, with **Feature Online Stores** serving low-latency reads via REST API [citation:6][citation:13]. Supports entity ID columns and configurable sync schedules. **Best for GCP-native organizations** wanting managed feature serving without operating Redis or Bigtable.

## Open-Source GitHub Projects

- **Feast** — **The open-source standard, cloud-agnostic and code-first.** Apache 2.0 licensed, **5,000+ GitHub stars** [citation:1]. **Features are defined in Python**, decoupling models from any specific storage or compute backend [citation:2]. **Two foundational components**: an **offline store** for historical feature extraction (training, batch scoring) and a **low-latency online store** for real-time serving [citation:2]. **Point-in-time correctness** is the underappreciated value — `get_historical_features` with an `entity_df` containing `event_timestamp` performs an AS OF join, preventing future data from leaking into training [citation:2]. **Push model** pushes feature values to the online store rather than pulling at request time, reducing retrieval latency [citation:2]. **Feast 0.59** added dbt integration for importing models as FeatureViews and OpenLineage integration. **The operational reality**: you own the materialization jobs, the online store (Redis for performance), and the registry. **The silent trap**: if your `entity_df` lacks `event_timestamp`, Feast silently uses "now" and you've manufactured a leakage bug no test will catch. **Best for teams with engineering bandwidth** wanting open-source flexibility, cost control, and no vendor lock-in, especially when features are mostly batch-computed with occasional streaming needs.

- **Hopsworks** — **The open-source feature store with more built-in capability than Feast, without Tecton's price tag.** **Part of a broader Data-Intensive AI platform** with feature store, model registry, and serving integrated [citation:3]. **Feature Store API** supports writing dataframes to **Feature Groups** (with primary keys and event time for online enablement) and reading from **Feature Views** that join feature groups [citation:3]. **Training dataset creation** with train/test splits and multiple output formats (csv, tsv, tfrecord, parquet) [citation:3]. **Online serving** via `get_online_feature_vector` for real-time predictions [citation:3]. **Environment independent** — works in Spark mode for data engineering jobs and Python mode for data science, on Hopsworks itself, Amazon SageMaker, or Kubeflow [citation:3]. **Best for teams comfortable with a less mature but actively developing ecosystem** wanting more out-of-the-box capability than raw Feast.

### Additional Strong Open-Source Options

- **Feathub** — Stream-batch unified feature store for real-time ML from Alibaba. Best for teams wanting a unified streaming and batch feature definition model.
- **ByteHub** — Feature store focused on simplicity. Best for smaller teams wanting a lighter-weight approach.
- **Hamilton** — Not a feature store per se, but a lineage-focused framework from the Apache Software Foundation that teams use for feature engineering with lineage tracking.

**Frameworks for building custom systems**: Combine **Feast** for cloud-agnostic feature serving with point-in-time correctness and pluggable online/offline stores, **Hopsworks** for a more integrated platform with feature store plus model registry and training dataset creation, **Tecton** if real-time streaming features and managed SLAs justify the cost, and **Databricks Feature Store** or **SageMaker Feature Store** if your stack is already standardized on one cloud — to build a feature store layer tailored to your feature freshness requirements, team operational capacity, and tolerance for vendor lock-in.

## How to Contribute

1. Fork the repo.
2. Add/edit entries in `README.md` (follow existing format).
3. Include: name, link, 1–2 sentence description, and whether it's SaaS or open-source.
4. Submit PR with a short explanation.

Star the repo if you find it useful!

## Disclaimer

- This is a **community-curated** list — not exhaustive and not an endorsement.
- Feature stores differ significantly in streaming maturity, operational burden, and cost model. **Feast is the open-source standard** with 5,000+ GitHub stars and cloud-agnostic design [citation:1][citation:2], but requires operational ownership of materialization jobs and the online store. **Tecton is the managed real-time choice** from Feast's creators [citation:8], with built-in monitoring and sub-10ms p99 latency positioning it for fraud and personalization. **Hopsworks provides more integrated platform capability** (feature store + model registry + serving) than raw Feast [citation:3]. **Feast's point-in-time join has a silent failure mode**: if `entity_df` lacks `event_timestamp`, Feast uses "now" without warning — a contract test asserting timestamp bounds is the only defense. **Databricks Feature Store's Feature Views are in Public Preview** — evaluate stability for production use [citation:4]. **A feature store is not always necessary** — if your models use only request-time features without time-window aggregations or cross-model sharing, running Feast or Hopsworks is over-engineering.
- Self-hosted open-source solutions require proper online store configuration (Redis for performance, DynamoDB or Bigtable for managed), materialization job ownership, and awareness that "free" self-hosted still has a headcount cost.

---

**Made for ML platform engineers and data scientists building feature consistency across training and serving.**
Let's make feature stores more open, point-in-time-correct, and operationally honest.
