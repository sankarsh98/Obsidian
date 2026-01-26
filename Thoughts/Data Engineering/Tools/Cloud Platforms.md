# Cloud Platforms

> *AWS, GCP, and Azure for Data Engineering*

---

## Platform Comparison

| Aspect | AWS | GCP | Azure |
|--------|-----|-----|-------|
| **Market Share** | #1 | #3 | #2 |
| **Strengths** | Breadth of services | ML/Analytics | Enterprise |
| **Data Warehouse** | Redshift | BigQuery | Synapse |
| **Object Storage** | S3 | GCS | Blob Storage |
| **Streaming** | Kinesis | Pub/Sub | Event Hubs |
| **ETL** | Glue | Dataflow | Data Factory |

---

## AWS for Data Engineering

### Key Services

| Service | Purpose |
|---------|---------|
| **S3** | Object storage (data lake) |
| **Redshift** | Data warehouse |
| **Glue** | ETL and data catalog |
| **EMR** | Managed Spark/Hadoop |
| **Kinesis** | Real-time streaming |
| **Athena** | Query S3 with SQL |
| **Lambda** | Serverless compute |

### Typical Architecture

```
Sources → Kinesis → S3 (Raw) → Glue → S3 (Processed) → Redshift → QuickSight
```

### S3 Best Practices

```
s3://my-bucket/
├── raw/
│   └── orders/
│       └── year=2024/month=01/day=15/
├── processed/
│   └── orders/
└── curated/
    └── analytics/
```

---

## GCP for Data Engineering

### Key Services

| Service | Purpose |
|---------|---------|
| **GCS** | Object storage |
| **BigQuery** | Data warehouse (serverless) |
| **Dataflow** | Stream and batch (Apache Beam) |
| **Dataproc** | Managed Spark/Hadoop |
| **Pub/Sub** | Messaging/streaming |
| **Composer** | Managed Airflow |
| **Cloud Functions** | Serverless compute |

### Typical Architecture

```
Sources → Pub/Sub → Dataflow → BigQuery → Looker
                 ↓
               GCS (Archive)
```

### BigQuery Features

- **Serverless** - No infrastructure management
- **Columnar** - Fast analytics
- **Separation** - Compute and storage separate
- **Partitioning** - Time and integer partitions
- **Clustering** - Sort within partitions

---

## Azure for Data Engineering

### Key Services

| Service | Purpose |
|---------|---------|
| **ADLS Gen2** | Data lake storage |
| **Synapse** | Analytics workspace |
| **Data Factory** | ETL orchestration |
| **Databricks** | Spark platform |
| **Event Hubs** | Streaming ingestion |
| **Stream Analytics** | Real-time processing |

### Typical Architecture

```
Sources → Event Hubs → ADLS → Databricks → Synapse → Power BI
```

---

## Certification Paths

| Platform | Data Engineering Cert |
|----------|----------------------|
| **AWS** | Data Analytics Specialty |
| **GCP** | Professional Data Engineer |
| **Azure** | Data Engineer Associate (DP-203) |

---

## Cost Management

- **Reserved instances** for predictable workloads
- **Spot/Preemptible** for batch processing
- **Auto-scaling** to match demand
- **Storage tiering** (hot/cold/archive)
- **Tagging** for cost allocation

---

*Back to: [[Index|Tools Home]]*
