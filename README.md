# Aurum Data Quality Research

Simple explanations of data-quality tools using the Aurum platform.

## Start here

1. [Aurum diagrams](docs/diagrams.md): the data flow and where each tool fits.
2. [Group 1 tools](docs/group1-tools.md): open-source/code-first tools using one shared Aurum example.
3. [Group 2 tools](docs/group2-tools.md): cloud-managed DQ services explained using the US Funds dataset.
4. [Group 3 tools](docs/group3-tools.md): commercial data-observability platforms explained using the US Funds dataset.
5. [Group 4 tools](docs/group4-tools.md): commercial enterprise suites explained using the US Funds dataset.
6. [Sources](docs/sources.md): official documentation and version notes.

## Original research scope

The comparison covers **16 tools across 4 groups**:

1. Open-source / code-first
2. Cloud-native managed services
3. Data-observability platforms
4. Enterprise suites

All four groups are now documented in this repository.

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
| AWS Glue Data Quality | AWS-managed DQ with a Data Catalog route and an ETL job route. Check the route for Postgres first. |
| Google Automatic Data Quality | Managed DQ scans for BigQuery and supported Google-side tables |
| Microsoft Purview Data Quality | Managed DQ plus governance for supported Purview sources |

## Group 3 at a glance

| Tool | Main use in Aurum |
|---|---|
| Monte Carlo | Continuous PostgreSQL monitoring, anomaly detection and incident context |
| Anomalo | Learned data anomalies plus explicit validation rules |
| Bigeye | Metric-based monitoring, custom rules and lineage-aware investigation |

## Group 4 at a glance

| Tool | Main use in Aurum | Source |
|---|---|---|
| Informatica Data Quality | Governed DQ rules inside the larger Informatica platform | [Sources](docs/sources.md#informatica-data-quality) |
| Talend Data Quality from Qlik | Build PostgreSQL DQ into Talend Studio ETL jobs | [Sources](docs/sources.md#talend-data-quality-from-qlik) |
| Collibra Data Quality & Observability | Enterprise DQ with separate Classic and Cloud PostgreSQL paths | [Sources](docs/sources.md#collibra-data-quality-and-observability) |
| Ataccama ONE | PostgreSQL DQ and observability with connector-specific limits | [Sources](docs/sources.md#ataccama-one) |

## Current evaluation direction

For the PostgreSQL-first Aurum prototype, Group 1 still provides the simplest initial validation POC path, especially Soda Core and GX Core.

For Group 2, AWS Glue Data Quality is the first Group 2 service to investigate for PostgreSQL. JDBC is supported in the Data Catalog route when Lake Formation is disabled, while Amazon RDS and Aurora have a separate caveat. The ETL job route is separate.

Group 3 is broader. Monte Carlo, Anomalo and Bigeye become more relevant when Aurum needs continuous production monitoring, learned anomalies, incident management, and investigation context across many datasets. They should not be treated as automatic replacements for Aurum's deterministic promotion policy.

Group 4 is not the first POC for the PostgreSQL-first prototype. It fits better when a client already owns Informatica, Talend, Collibra, or Ataccama and wants Aurum to work with that existing enterprise platform. Soda and GX remain the first validation candidates.

These are documentation-based architecture assessments. They do not claim the tools are already installed, benchmarked or selected.
