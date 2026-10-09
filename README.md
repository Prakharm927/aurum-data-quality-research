# Aurum Data Quality Research

Simple explanations of data-quality tools using the Aurum platform.

## Start here

1. [Aurum diagrams](docs/diagrams.md): the data flow and where each tool fits.
2. [Group 1 tools](docs/group1-tools.md): open-source/code-first tools using one shared Aurum example.
3. [Group 2 tools](docs/group2-tools.md): cloud-managed DQ services explained using the US Funds dataset.
4. [Group 3 tools](docs/group3-tools.md): commercial data-observability platforms explained using the US Funds dataset.
5. [Sources](docs/sources.md): official documentation and version notes.

## Original research scope

The comparison covers **16 tools across 4 groups**:

1. Open-source / code-first
2. Cloud-native managed services
3. Data-observability platforms
4. Enterprise suites

Groups 1-3 are now documented in this repository. Group 4 remains to be written.

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

## Group 3 at a glance

| Tool | Main use in Aurum |
|---|---|
| Monte Carlo | Continuous PostgreSQL monitoring, anomaly detection and incident context |
| Anomalo | Learned data anomalies plus explicit validation rules |
| Bigeye | Metric-based monitoring, custom rules and lineage-aware investigation |

## Group 4 remaining

The final original group still to document contains:

- Informatica Data Quality
- Talend Data Quality / Qlik
- Collibra Data Quality & Observability
- Ataccama ONE

## Current evaluation direction

For the PostgreSQL-first Aurum prototype, Group 1 still provides the simplest initial validation POC path, especially Soda Core and GX Core.

For Group 2, AWS Glue Data Quality has the most direct documented PostgreSQL route through JDBC.

Group 3 is broader. Monte Carlo, Anomalo and Bigeye become more relevant when Aurum needs continuous production monitoring, learned anomalies, incident management, and investigation context across many datasets. They should not be treated as automatic replacements for Aurum's deterministic promotion policy.

These are documentation-based architecture assessments. They do not claim the tools are already installed, benchmarked or selected.
