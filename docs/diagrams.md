# Aurum diagrams

[Home](../README.md) · [Six tool walkthroughs](group1-tools.md) · [Sources](sources.md)

## 1. The proposed Aurum quality flow

```mermaid
flowchart TD
  source["Aurum source data"] --> bronze["Bronze raw load"]
  bronze --> runner["Selected DQ tool"]
  rules["Dataset rules"] --> runner
  runner --> results["Quality results"]
  results --> policy{"Aurum promotion policy"}
  policy -->|"Pass"| silver["Silver validated data"]
  policy -->|"Blocking failure"| hold["Hold load and review"]
  policy -->|"Warning"| review["Review under configured policy"]
  silver --> gold["Gold business output"]
```

Bronze holds raw data. A tool evaluates rules and returns results. Aurum's policy decides whether the validated data can move to Silver. Gold contains business outputs built from Silver.

A warning can be allowed or held according to the rule policy. A failed tool execution must be recorded as an execution error, not treated as a passing validation.

Holding a load and quarantining individual rows are different actions. Row quarantine requires enough evidence to identify the failing records.

## 2. Where the six tools fit

```mermaid
flowchart TD
  start{"Aurum need"}
  start -->|"Validate PostgreSQL"| direct["Evaluate Soda and GX"]
  start -->|"Use dbt"| dbt["dbt tests"]
  dbt -->|"Add trend monitoring"| elementary["Elementary OSS"]
  start -->|"Use Spark"| spark["Deequ"]
  start -->|"Catalog and lineage"| catalog["OpenMetadata"]

```

These are different needs, so the tools can complement one another. A catalog can sit alongside a validation runner. Historical monitoring can complement explicit rules.

## 3. How to read the tool diagrams

The [Group 1 guide](group1-tools.md) contains a diagram for every tool.

| Diagram | What to notice |
|---|---|
| Soda | Rules and table feed a scan; Aurum decides what follows |
| GX | Data and a suite meet in a Validation Definition; a Checkpoint runs it |
| dbt | Test sources before dependent builds; test candidates before publishing |
| Deequ | PostgreSQL data must first be available as a Spark DataFrame |
| Elementary | Current metrics are compared with history |
| OpenMetadata | Metadata and DQ workflows feed a shared table view |

Solid arrows show data, results or workflow order. Dashed arrows show optional services or integrations.

All diagrams describe proposed integrations. Per-tool diagrams show alternatives, not six tools that must all be deployed.
