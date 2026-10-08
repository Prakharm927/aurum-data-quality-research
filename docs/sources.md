# Sources

[Home](../README.md) · [Group 1 guide](group1-tools.md)

Reviewed on 8 October 2026. Product behavior is described from official documentation. Aurum fit and the shortlist are engineering assessments.

## Input context

- User-provided comparison workbook: `Data_Quality_Tools_Comparisons.xlsx`, especially the Comparison and How It Works sheets.
- User-provided Aurum project context: PostgreSQL-oriented Bronze, Silver and Gold design, and the completed US Funds scan.
- The earlier scan reported 75,657,739 mutual-fund price rows across A–Z files. This guide did not rerun that scan.
- Example rows, later reload sizes, Bronze schema name and the `price` alias are illustrative. Candidate rules require the dataset owner's agreement.

## Soda

- [Current Soda Core repository](https://github.com/sodadata/soda-core): v4 installation, public PyPI packages and legacy v3 package names.
- [Soda Core v3 overview](https://docs.soda.io/soda-documentation/soda-v3/overview-main): SodaCL and the open-source scan engine.
- [SodaCL tutorial](https://docs.soda.io/soda-documentation/soda-v3/soda-cl-overview/quick-start-sodacl): compound duplicate checks.
- [Soda v3 scans](https://docs.soda.io/soda-documentation/soda-v3/run-a-scan): scan usage and results.
- [Soda CLI reference](https://docs.soda.io/reference/cli-reference): contract verification commands.

Version correction from the workbook: current v4 packages such as `soda-postgres` are on public PyPI. The workbook's statement that v4 requires Soda's private package index is not current. The YAML example in this guide is explicitly v3 syntax.

## Great Expectations

- [GX overview](https://docs.greatexpectations.io/docs/core/introduction/gx_overview).
- [GX glossary](https://docs.greatexpectations.io/docs/reference/learn/glossary/): sources, assets, batches, suites and definitions.
- [Run validations](https://docs.greatexpectations.io/docs/core/run_validations/).
- [Run a Checkpoint](https://docs.greatexpectations.io/docs/core/trigger_actions_based_on_results/run_a_checkpoint/).
- [Database dependencies](https://docs.greatexpectations.io/docs/core/set_up_a_gx_environment/install_additional_dependencies/): PostgreSQL support.
- [Project settings](https://docs.greatexpectations.io/docs/core/configure_project_settings/): stores and Data Docs.

The guide evaluates GX Core. It does not assume availability of GX Cloud or make claims about acquisition completion.

## dbt

- [Data tests](https://docs.getdbt.com/docs/build/data-tests): built-in tests, SQL tests and stored failures.
- [dbt build](https://docs.getdbt.com/reference/commands/build): dependency order and downstream skips.

The guide describes data tests. Unit tests of transformation code are a separate feature.

## Deequ

- [Official Deequ repository and README](https://github.com/awslabs/deequ): Spark requirements, VerificationSuite, suggestions and metrics.

Version correction from the workbook: Java 8 is not a universal requirement. The current README specifies Java 11 for 2.1.0 and later and Java 8 for earlier 2.0.x releases. Verify Spark, Java, Scala and PyDeequ compatibility for the selected release.

## Elementary

- [Cloud versus OSS](https://docs.elementary-data.com/cloud/cloud-vs-oss).
- [Product FAQ](https://docs.elementary-data.com/cloud/resources/faq): OSS dbt dependency and Cloud scope.
- [Volume anomalies](https://docs.elementary-data.com/data-tests/anomaly-detection-tests/volume-anomalies): historical comparisons, complete buckets and severity.

Configuration correction from the workbook: `timestamp_column` is recommended, not mandatory, for volume anomalies. Without it, total table row counts are used.

## OpenMetadata

- [PostgreSQL connector](https://docs.open-metadata.org/latest/connectors/database/postgres): metadata, profiling, DQ and lineage workflows.

Lineage completeness depends on ingestion configuration and available query or pipeline metadata. It is not guaranteed merely by registering PostgreSQL.

## Validation scope

This is documentation research. No DQ engine was installed, benchmarked or connected to the Aurum runtime as part of this update.
