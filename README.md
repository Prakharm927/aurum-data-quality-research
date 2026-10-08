# Aurum Data Quality Research

Simple explanations of data-quality tools using the Aurum platform.

## Start here

1. [Aurum diagrams](docs/diagrams.md): the data flow and where each tool fits.
2. [Group 1 tools](docs/group1-tools.md): six tools, one shared example, and short explanations.
3. [Sources](docs/sources.md): official documentation and version notes.

## Group 1 at a glance

| Tool | Main use in Aurum |
|---|---|
| Soda Core | YAML rules or contracts against PostgreSQL |
| GX Core | Reusable validation controlled from Python |
| dbt tests | SQL checks alongside dbt sources and models |
| Deequ | Quality checks on Spark DataFrames |
| Elementary OSS | Historical anomaly monitoring in a dbt project |
| OpenMetadata | Catalog, ownership, lineage, profiling and DQ visibility |

## First evaluation

Compare Soda Core and GX Core on the same PostgreSQL load and rules. Evaluate dbt tests and Elementary OSS when dbt is used, Deequ when Spark is used, and OpenMetadata when catalog and lineage are required.

These diagrams show proposed integrations with Aurum's PostgreSQL Bronze → Silver → Gold design. They do not claim these tools are already installed or that a winner has been tested.

Scope: Group 1 only. Cloud-managed DQ, commercial observability and enterprise suites can be documented separately.
