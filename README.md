# Aurum Data Quality Research

Simple explanations of data-quality tools using the Aurum platform.

## Start here

- [Group 1 tools](docs/group1-tools.md): Soda, Great Expectations, dbt tests, Deequ, Elementary and OpenMetadata.
- [Diagrams](docs/diagrams.md): how the tools fit into Aurum and where Aurum makes the promotion decision.
- [Sources](docs/sources.md): official documentation and the comparison workbook used for this research.

The guide uses a proposed PostgreSQL Bronze → validation → Silver → Gold flow. The tools are candidates to evaluate, not integrations already implemented in Aurum.

## First evaluation

Compare Soda Core and GX Core on the same PostgreSQL load and rules. Consider dbt tests and Elementary when dbt is part of the pipeline, Deequ when Spark is part of processing, and OpenMetadata when catalog and lineage are also required.
