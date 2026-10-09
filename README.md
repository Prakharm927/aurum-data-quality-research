# Aurum Data Quality Research

Simple explanations of data-quality tools using the Aurum platform.

## Start here

1. [Aurum diagrams](docs/diagrams.md): the data flow and where each tool fits.
2. [Group 1 tools](docs/group1-tools.md): open-source/code-first tools using one shared Aurum example.
3. [Group 2 tools](docs/group2-tools.md): cloud-managed DQ services explained using the US Funds dataset.
4. [Sources](docs/sources.md): official documentation and version notes.

## Group 1 at a glance

| Tool | Main use in Aurum |
|---|---|
| Soda Core | YAML rules or contracts against PostgreSQL |
| GX Core | Reusable validation controlled from Python |
| dbt tests | SQL checks alongside dbt sources and models |
| Deequ | Quality checks on Spark DataFrames |
| Elementary OSS | Historical anomaly monitoring in a dbt project |
| OpenMetadata | Catalog, ownership, lineage, profiling and DQ visibility |

## Group 2 at a glance

| Tool | Main use in Aurum |
|---|---|
| AWS Glue Data Quality | AWS-managed DQ with a direct PostgreSQL JDBC route |
| Google Automatic Data Quality | Managed DQ scans for BigQuery and supported Google-side tables |
| Microsoft Purview Data Quality | Managed DQ plus governance for supported Purview sources |

## Current evaluation direction

For the PostgreSQL-first Aurum prototype, Group 1 compares Soda Core and GX Core directly.

For Group 2, AWS Glue Data Quality has the most direct documented PostgreSQL path. Google Automatic Data Quality is strongest when data is already in BigQuery or another supported Google table. Microsoft Purview Data Quality is aimed at supported Purview sources; PostgreSQL is supported by Purview Data Map for metadata but is not currently listed as a Purview Data Quality source.

These are documentation-based architecture assessments. They do not claim the tools are already installed, benchmarked or selected.
