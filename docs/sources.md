# Sources

[Home](../README.md) · [Group 1 guide](group1-tools.md) · [Group 2 guide](group2-tools.md) · [Group 3 guide](group3-tools.md) · [Group 4 guide](group4-tools.md)

Reviewed on 10 October 2026. Product behavior is described from official documentation. Aurum fit and the shortlists are engineering assessments.

## Input context

- User-provided comparison workbook: Data_Quality_Tools_Comparison.xlsx, especially the Comparison and How It Works sheets.
- User-provided Aurum project context: PostgreSQL-oriented Bronze, Silver and Gold design, and the completed US Funds scan.
- The earlier scan reported 23,783 mutual-fund metadata rows, 75,657,739 mutual-fund price rows, 2,310 ETF metadata rows and 3,866,030 ETF price rows.
- Example rows, later reload sizes, Bronze schema name and the price alias are illustrative. Candidate rules require the dataset owner's agreement.

# Group 1 sources

## Soda

- [Current Soda Core repository](https://github.com/sodadata/soda-core): v4 installation, public PyPI packages and legacy v3 package names.
- [Soda Core v3 overview](https://docs.soda.io/soda-documentation/soda-v3/overview-main): SodaCL and the open-source scan engine.
- [SodaCL tutorial](https://docs.soda.io/soda-documentation/soda-v3/soda-cl-overview/quick-start-sodacl): compound duplicate checks.
- [Soda v3 scans](https://docs.soda.io/soda-documentation/soda-v3/run-a-scan): scan usage and results.
- [Soda CLI reference](https://docs.soda.io/reference/cli-reference): contract verification commands.

Version correction from the workbook: current v4 packages such as soda-postgres are on public PyPI. The workbook's statement that v4 requires Soda's private package index is not current. The YAML example in the guide is explicitly v3 syntax.

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

Configuration correction from the workbook: timestamp_column is recommended, not mandatory, for volume anomalies. Without it, total table row counts are used.

## OpenMetadata

- [PostgreSQL connector](https://docs.open-metadata.org/latest/connectors/database/postgres): metadata, profiling, DQ and lineage workflows.

Lineage completeness depends on ingestion configuration and available query or pipeline metadata. It is not guaranteed merely by registering PostgreSQL.

# Group 2 sources

## AWS Glue Data Quality

- [AWS Glue Data Quality overview](https://docs.aws.amazon.com/glue/latest/dg/glue-data-quality.html): AWS documents two entry points, Data Catalog Data Quality and ETL job Data Quality.
- [DQDL reference](https://docs.aws.amazon.com/glue/latest/dg/dqdl.html): AWS Data Quality Definition Language.
- [Data Catalog DQ getting started and supported source types](https://docs.aws.amazon.com/glue/latest/dg/data-quality-getting-started.html): when Lake Formation is disabled, JDBC is listed as Supported while Amazon RDS and Aurora are separately listed as Not Supported.
- [AWS Glue ETL job Data Quality tutorial](https://docs.aws.amazon.com/glue/latest/dg/tutorial-data-quality.html): DQ can run inside a Glue ETL job against data flowing through the job.
- [AWS Glue JDBC connections](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-programming-etl-connect-jdbc-home.html): PostgreSQL JDBC connection support for AWS Glue.

## Google Cloud automatic data quality

- [Auto data quality overview](https://docs.cloud.google.com/knowledge-catalog/docs/auto-data-quality-overview): managed scans, supported table model, rules and monitoring.
- [Use auto data quality](https://docs.cloud.google.com/knowledge-catalog/docs/use-auto-data-quality): creating and running DQ scans.
- [Data profiling](https://docs.cloud.google.com/knowledge-catalog/docs/use-data-profiling): column statistics and rule recommendations.

Google documentation now uses the Knowledge Catalog name for capabilities previously associated with Dataplex Universal Catalog.

## Microsoft Purview Data Quality

- [Purview Data Quality overview](https://learn.microsoft.com/en-us/purview/unified-catalog-data-quality): profiling, rules, scans, monitoring and operational requirements.
- [Supported Data Quality sources](https://learn.microsoft.com/en-us/purview/unified-catalog-data-quality-supported-sources-file-formats): current supported sources and formats for profiling and DQ scans.
- [PostgreSQL in Purview Data Map](https://learn.microsoft.com/en-us/purview/register-scan-postgresql): PostgreSQL metadata scanning and lineage capabilities.

Important distinction: PostgreSQL support in Purview Data Map does not mean PostgreSQL is supported by Purview Data Quality. The current Data Quality supported-source list does not include PostgreSQL.

# Group 3 sources

## Monte Carlo

- [Postgres integration](https://docs.getmontecarlo.com/docs/postgres): default volume monitoring uses hourly table metadata. Opt-in freshness and volume row-count monitors run count(*) queries for each selected table. The support table does not mark PostgreSQL lineage as supported.

The Group 3 guide treats Monte Carlo as a commercial observability platform, not as a drop-in replacement for Aurum's promotion policy.

## Anomalo

- [Data stack integrations](https://www.anomalo.com/integrations/): PostgreSQL is publicly listed as a supported integration.
- [Data validation tools](https://www.anomalo.com/data-validation-tools/): automated checks, no-code and SQL validation rules, profiling, pipeline integration, and root-cause support.
- [Automated anomaly detection](https://www.anomalo.com/anomaly-detection-software/): Anomalo says its algorithms need about 2 weeks to produce useful results, continue improving for roughly 30 to 60 days, and work best when a table has at least 100 rows per day.

Detailed source-specific connector documentation is private to customers/pilots, so PostgreSQL-specific permissions, query behavior and exact feature coverage should be verified in a POC.

## Bigeye

- [Connect Postgres](https://docs.bigeye.com/docs/connect-postgresql): read-only PostgreSQL connection and profiling setup.
- [Data source connections](https://docs.bigeye.com/docs/source-support): direct and agent-based connection models.
- [Metrics](https://docs.bigeye.com/docs/metrics): metric-based anomaly monitoring.
- [Custom rules](https://docs.bigeye.com/docs/custom-rules): SQL-based rules for business-specific checks.
- [Lineage Plus](https://docs.bigeye.com/docs/lineage): column-level lineage capabilities and the documented lineage connector list. PostgreSQL is not among the listed database lineage connectors.
- [Impact Analysis](https://docs.bigeye.com/docs/impact-analysis): downstream impact analysis from lineage.

For Aurum, PostgreSQL lineage should be confirmed in a POC because PostgreSQL is not among the database lineage connectors listed on the Lineage Plus documentation.

# Group 4 sources

## Informatica Data Quality

- [Run data quality rules on assets](https://onlinehelp.informatica.com/IICS/prod/DGC/en/cloud-data-governance-and-catalog-working-with-assets/Run_data_quality_rules_on_assets.html): rule templates, glossary linkage, rule occurrences, score bands, daily/weekly/monthly schedules, Metadata Command Center Data Quality enablement, and the listed technical sources. PostgreSQL is not named on this page; JDBC is the generic entry.
- [Informatica pricing](https://www.informatica.com/pricing.html?isappinstalled=0): consumption-based pricing for Informatica Intelligent Data Management Cloud.

For Aurum, PostgreSQL support should be confirmed with Informatica in a POC before treating this as a direct PostgreSQL route.

## Talend Data Quality from Qlik

- [Qlik completes acquisition of Talend](https://www.qlik.com/us/news/company/press-room/press-releases/qlik-acquires-talend): Qlik announced completion of the Talend acquisition in May 2023.
- [Talend PostgreSQL components](https://help.qlik.com/talend/r/en-US/8.0/components/postgresql/postgresql-component): PostgreSQL components including tPostgresqlInput, tPostgresqlValidRows, and tPostgresqlInvalidRows.
- [tPostgresqlValidRows](https://help.qlik.com/talend/en-US/components/8.0/postgresql/tpostgresqlvalidrows): row checks against regular-expression patterns or DQ rules, optional WHERE clause, Feature Manager installation, and listed Talend editions.
- [Talend Trust Score](https://help.qlik.com/talend/en-US/data-preparation-user-guide/Cloud/talend-trust-score): Talend Cloud Trust Score from 0 to 5.
- [tMatchGroup](https://help.qlik.com/talend/en-US/components/8.0/data-matching/tmatchgroup): matching/grouping component and Feature Manager installation.

## Collibra Data Quality and Observability

- [Classic PostgreSQL connection](https://productresources.collibra.com/docs/collibra/latest/Content/DataQuality/DBConnection/ref_postgresql.htm): driver class org.postgresql.Driver, default port 5432, listed driver version 42.5.1, read access, ROLE_ADMIN, no pushdown, Spark/Yarn/Parallel JDBC processing, and JDK 8 and 11.
- [Cloud supported data sources](https://productresources.collibra.com/docs/collibra/2026.08/Content/UnifiedDataQuality/DataSources/ref_data-quality-supported-data-sources.htm): PostgreSQL listed as a JDBC source using Pullup processing with driver 25.0.9543; no PostgreSQL pushdown is shown.
- [Compatibility matrix](https://productresources.collibra.com/docs/collibra/2026.08/Content/UnifiedDataQuality/ref_dq-compatibilities.htm): PostgreSQL driver versions vary by Collibra Platform release.
- [Data quality scores](https://productresources.collibra.com/docs/collibra/latest/Content/UnifiedDataQuality/co_about-data-quality-scores.htm): 90 to 100 pass, 76 to 89 warning, 0 to 75 fail.

Classic and Cloud are separate apps with separate documentation, so the Aurum design should name the intended app and pin the release.

## Ataccama ONE

- [Supported connectors](https://docs.ataccama.com/ataccama-one-agentic/latest/data-processing/supported-connectors.html): PostgreSQL username/password connection, support for catalog/data processing and Edge processing, no PostgreSQL lineage support, and pushdown listed only for Snowflake and Databricks.
- [Data observability](https://docs.ataccama.com/one/latest/data-observability/data-observability.html): monitoring of DQ results, volume/statistical anomalies, schema changes, and freshness; PostgreSQL freshness setup requires track_commit_timestamp = on and a database restart; at least sample profiling is required before observability configuration.
- [Run DQ Evaluation](https://docs.ataccama.com/one/latest/data-quality/run-dq-evaluation.html): DQ evaluation and mapping rules to governed data context.
- [Rule types](https://docs.ataccama.com/one/latest/data-quality/rules.html): DQ rule definitions and rule mapping.

A glossary can help with governed rule mapping, but rules can also be mapped directly to attributes.

## Validation scope

This is documentation research. None of these DQ or observability engines was benchmarked or connected to the Aurum runtime as part of this update.
