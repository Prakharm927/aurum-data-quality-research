# Aurum diagrams

[Home](../README.md) · [Group 1 guide](group1-tools.md) · [Group 2 guide](group2-tools.md) · [Sources](sources.md)

## 1. The proposed Aurum quality flow

```mermaid
flowchart TD
  source["Aurum source data"] --> bronze["Bronze raw load"]
  bronze --> runner["Selected DQ tool or service"]
  rules["Dataset rules"] --> runner
  runner --> results["Quality results"]
  results --> policy{"Aurum promotion policy"}
  policy -->|"Pass"| silver["Silver validated data"]
  policy -->|"Blocking failure"| hold["Hold load and review"]
  policy -->|"Warning"| review["Review under configured policy"]
  silver --> gold["Gold business output"]
```

Bronze holds raw data. A DQ tool evaluates rules and returns results. Aurum's policy decides whether the data can move to Silver. Gold contains business outputs built from Silver.

A failed tool execution must be recorded as an execution error, not treated as a passing validation.

---

## 2. Group 1 — tools Aurum manages directly

```mermaid
flowchart TD
  start{"Aurum need"}
  start -->|"Validate PostgreSQL"| direct["Evaluate Soda and GX"]
  start -->|"Use dbt"| dbt["dbt tests"]
  dbt -->|"Add trend monitoring"| elementary["Elementary OSS"]
  start -->|"Use Spark"| spark["Deequ"]
  start -->|"Catalog and lineage"| catalog["OpenMetadata"]
```

Group 1 tools are mostly code-first or self-managed options. The exact fit depends on whether Aurum is using PostgreSQL, dbt, Spark, or a catalog platform.

See [Group 1 tools](group1-tools.md).

---

## 3. Group 2 — managed cloud DQ services

```mermaid
flowchart TD
  aurum["Aurum US Funds Bronze"] --> choice{"Where does the client's data live?"}
  choice -->|"PostgreSQL / AWS-friendly"| aws["AWS Glue Data Quality<br/>JDBC route"]
  choice -->|"BigQuery / GCP"| google["Google Automatic Data Quality"]
  choice -->|"Supported Azure / Purview source"| ms["Microsoft Purview Data Quality"]

  aws --> result["DQ result"]
  google --> result
  ms --> result

  result --> policy{"Aurum policy"}
  policy -->|"Pass"| silver["Promote to Silver"]
  policy -->|"Fail"| hold["Hold and investigate"]
```

The simplest difference is:

- **AWS Glue Data Quality**: most direct Group 2 path for the current PostgreSQL-first prototype because of JDBC.
- **Google Automatic Data Quality**: natural when data is already in BigQuery or another supported Google-side table.
- **Microsoft Purview Data Quality**: natural for Microsoft/Azure environments using supported Purview DQ sources.

The cloud provider runs the DQ service, but Aurum still owns the final promotion decision.

See [Group 2 tools](group2-tools.md).

---

## 4. US Funds example used across both groups

The research uses the same current Aurum dataset so the tools are easy to compare:

```text
29 physical CSV files
        ↓
4 logical tables

mutual_funds
mutual_fund_prices
etfs
etf_prices
```

The main teaching example is `mutual_fund_prices`, built from the A-Z files and containing about 75.7 million rows in the completed run.

Common candidate checks:

```text
fund_symbol is present
price_date is present
fund_symbol + price_date does not repeat
price follows the agreed range
price symbol exists in metadata
load volume matches expected scope
```

One fund appears on many dates, so `fund_symbol` alone must not be treated as unique in a price-history table.

---

## 5. How to read the per-tool diagrams

### Group 1

| Diagram | What to notice |
|---|---|
| Soda | Rules and table feed a scan; Aurum decides what follows |
| GX | Data and a suite meet in a validation workflow |
| dbt | Tests can sit before and after transformation |
| Deequ | PostgreSQL data must first become a Spark DataFrame |
| Elementary | Current metrics are compared with historical metrics |
| OpenMetadata | Metadata and DQ results feed a shared table view |

### Group 2

| Diagram | What to notice |
|---|---|
| AWS Glue DQ | PostgreSQL can connect through JDBC before AWS runs the checks |
| Google Automatic DQ | Current PostgreSQL data needs to be available in a supported Google table |
| Microsoft Purview DQ | First confirm that the source is supported for Purview Data Quality |

All diagrams describe proposed integrations. They are alternatives or complementary pieces, not tools that all need to be deployed together.
