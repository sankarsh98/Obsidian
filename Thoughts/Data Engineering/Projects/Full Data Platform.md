# Full Data Platform Project

> *Build an end-to-end data platform*

---

## Project Overview

Build a complete data platform with:
- Multiple data sources
- Batch and streaming
- Full observability
- IaC deployment

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                          SOURCES                                │
│   PostgreSQL  │  REST APIs  │  Kafka Events  │  CSV Files      │
└───────────────────────────────┬─────────────────────────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              ▼                                   ▼
       ┌─────────────┐                    ┌─────────────┐
       │   Airflow   │                    │   Kafka     │
       │   (Batch)   │                    │ (Streaming) │
       └──────┬──────┘                    └──────┬──────┘
              │                                  │
              └─────────────┬────────────────────┘
                            ▼
                    ┌─────────────┐
                    │  Data Lake  │
                    │   (S3/GCS)  │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  Warehouse  │
                    │ (Snowflake) │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
       ┌─────────────┐          ┌─────────────┐
       │  Dashboards │          │   ML/APIs   │
       └─────────────┘          └─────────────┘
```

---

## Components

1. **Batch Ingestion**: Airflow + Python
2. **Streaming Ingestion**: Kafka + Spark
3. **Storage**: S3/GCS + Delta Lake
4. **Transform**: dbt
5. **Warehouse**: Snowflake
6. **Serving**: Looker, APIs
7. **Monitoring**: Prometheus + Grafana
8. **IaC**: Terraform

---

*Back to: [[Index|Projects Home]]*
