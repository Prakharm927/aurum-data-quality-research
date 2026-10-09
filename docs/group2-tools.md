# Group 2 tools for Aurum

[Home](../README.md) · [Group 1 guide](group1-tools.md) · [Sources](sources.md)

## What Group 2 means

Group 2 contains **cloud-managed data-quality services**.

Simple idea:

> Aurum gives the cloud service access to the data, the cloud service runs the checks, and Aurum uses the result to decide whether data can move forward.

Aurum still owns the final pipeline decision.

~~~mermaid
flowchart LR
  files["US Funds files"] --> bronze["Aurum Bronze"]
  bronze --> cloud["Cloud DQ service"]
  rules["Quality rules"] --> cloud
  cloud --> result["DQ result"]
  result --> policy{"Aurum policy"}
  policy -->|"Pass"| silver["Promote to Silver"]
  policy -->|"Blocking failure"| hold["Hold / investigate"]
~~~

The provider runs most of the DQ infrastructure for us, but Aurum still has to handle connections, permissions, the final pass/fail policy, evidence, and what happens after a failure.

---

## The shared Aurum example — US Funds

The current US Funds dataset has **29 physical CSV files** mapped into four logical tables:

| Logical table | Historical rows from the completed run | Simple meaning |
|---|---:|---|
| mutual_funds | 23,783 | Mutual-fund metadata |
| mutual_fund_prices | 75,657,739 | Mutual-fund price history from A-Z files |
| etfs | 2,310 | ETF metadata |
| etf_prices | 3,866,030 | ETF price history |

For teaching, imagine Bronze contains:

'bronze.mutual_fund_prices(fund_symbol, price_date, price)'

**price is only a teaching alias. Map it to the real price column before implementation.**

Candidate checks:

| Check | Aurum example |
|---|---|
| Missing value | fund_symbol and price_date should be present |
| Duplicate | the pair fund_symbol + price_date should not repeat |
| Value range | price should follow an agreed valid range |
| Relationship | every price symbol should exist in mutual_funds |
| Load volume | the load should contain the expected scope of rows/files |

Important: a fund appears on many dates, so **fund_symbol alone must not be treated as unique in a price-history table**.

These are candidate rules for explaining the tools. Required columns, keys and price limits still need owner agreement.

### One bad load

Imagine Aurum receives these rows:

| fund_symbol | price_date | price | Problem |
|---|---|---:|---|
| ABCDX | 2021-01-04 | 25.40 | None |
| NULL | 2021-01-04 | 18.00 | Missing symbol |
| ABCDX | 2021-01-04 | 26.10 | Same fund + date as row 1 |
| XYZDX | 2021-01-05 | -3.00 | Invalid if the agreed rule is price >= 0 |

We use this same load for all three Group 2 tools.

---

# 1. AWS Glue Data Quality

## Simple idea

**AWS Glue Data Quality is AWS's managed service for checking data.**

Instead of Aurum running a DQ library itself, AWS runs the checking job.

Rules are written in **DQDL — Data Quality Definition Language**. Think of DQDL as AWS's rule language.

Example idea:

~~~text
fund_symbol must be present
price_date must be present
fund_symbol + price_date must not repeat
price must follow the agreed range
~~~

## Aurum + US Funds flow

~~~mermaid
flowchart TD
  pg["Aurum PostgreSQL Bronze\nmutual_fund_prices"] --> conn["AWS Glue JDBC connection"]
  conn --> catalog["Glue Data Catalog / Glue job"]
  dqdl["DQDL rules"] --> check["AWS Glue Data Quality"]
  catalog --> check
  check --> result["DQ score + rule results"]
  result --> policy{"Aurum policy"}
  policy -->|"Pass"| silver["Promote to Silver"]
  policy -->|"Fail"| hold["Hold / investigate"]
~~~

### What happens in our example?

1. Aurum loads the A-Z data into PostgreSQL Bronze.
2. AWS Glue connects to PostgreSQL through JDBC.
3. Glue runs rules on mutual_fund_prices.
4. The missing symbol, duplicate fund-date and invalid-price rule can fail.
5. Glue returns quality results.
6. **Aurum** decides whether the load can move to Silver.

AWS Glue supports PostgreSQL through its JDBC connection route. AWS Glue Data Quality supports JDBC sources compatible with the Data Catalog.

## One important detail

AWS Glue has two common DQ paths:

- **Data Catalog DQ** — run rules against cataloged data.
- **Glue ETL job DQ** — put a DQ step inside a Glue ETL job.

For Aurum, this matters because AWS documents failed-record identification for the ETL-job path, while Data Catalog DQ mainly gives rule results and scores.

## Why it could fit Aurum

- It can connect to PostgreSQL through JDBC.
- AWS manages the DQ compute.
- It supports rules, schedules, monitoring and anomaly features.
- DQ results can be connected to AWS monitoring services.

## Main catch

Aurum becomes dependent on AWS setup:

~~~text
AWS account
+ IAM permissions
+ Glue connection
+ network/VPC access
+ Glue/Data Catalog configuration
+ cloud cost
~~~

So managed does **not** mean zero setup.

### Easy meeting line

> "AWS Glue Data Quality can reach our PostgreSQL-style setup through JDBC, run AWS DQ rules, and return results. Aurum would still decide whether the US Funds load is promoted or held."

---

# 2. Google Cloud Dataplex / Knowledge Catalog Automatic Data Quality

## Simple idea

Google's managed DQ service runs **data-quality scans**.

You define rules such as:

~~~text
fund_symbol is not null
price is in a valid range
values are unique where required
custom SQL rule
~~~

Google runs the scan and stores the results.

The product name has changed over time. Current Google documentation refers to **Knowledge Catalog automatic data quality**, previously associated with **Dataplex Universal Catalog**.

## Aurum + US Funds flow

For our current PostgreSQL-first Aurum, there is an extra step:

~~~mermaid
flowchart TD
  pg["Aurum PostgreSQL Bronze"] --> move["Make the data available\nas a supported Google table"]
  move --> bq["BigQuery / supported catalog table"]
  rules["Google DQ rules"] --> scan["Automatic DQ scan"]
  bq --> scan
  scan --> result["Scan results"]
  result --> policy{"Aurum policy"}
  policy -->|"Pass"| silver["Promote / continue"]
  policy -->|"Fail"| hold["Hold / investigate"]
~~~

### Why is there an extra step?

Current Google automatic DQ is centered on **BigQuery** and supported catalog tables such as Iceberg REST Catalog tables.

Our current Aurum prototype is **PostgreSQL-first**.

So we should not draw this as if it were the normal current path:

~~~text
PostgreSQL → Google DQ directly
~~~

For the current US Funds setup, we would first need the data available in a supported Google table.

## US Funds example

Suppose mutual_fund_prices is available in BigQuery.

A Google DQ scan could check:

- fund_symbol is not null.
- price_date is not null.
- the agreed price rule.
- custom SQL for more complex checks.

Google also supports data profiling, which can calculate things such as null percentages, unique values and distributions. Profiling can help recommend quality rules.

## Why it could fit Aurum

It is useful if Aurum is deployed in a Google Cloud / BigQuery environment:

~~~text
Aurum client uses BigQuery
        ↓
Google DQ scans the table
        ↓
Aurum reads the result
~~~

Google manages the scan infrastructure, scheduling and monitoring.

## Main catch for our current Aurum setup

Our current Bronze target is PostgreSQL, not BigQuery.

So using Google Automatic DQ **only for this PostgreSQL prototype** would introduce an extra data-platform step.

### Easy meeting line

> "Google's service is simple if the client's data is already in BigQuery. For our current PostgreSQL US Funds setup, it is not the most direct fit because the DQ scan expects supported Google-side tables."

---

# 3. Microsoft Purview Data Quality

## Simple idea

**Microsoft Purview Data Quality combines data-quality checking with governance.**

It can profile supported data, attach quality rules, run scans, show results and notify teams about quality problems.

Think:

~~~text
Understand the table
        ↓
Create quality rules
        ↓
Run quality scan
        ↓
See quality result
        ↓
Investigate problems
~~~

## Aurum + US Funds flow

~~~mermaid
flowchart TD
  bronze["Aurum Bronze data"] --> supported{"Is the data source supported\nfor Purview DQ?"}
  supported -->|"Yes"| profile["Profile the table"]
  profile --> rules["Add DQ rules"]
  rules --> scan["Run Purview DQ scan"]
  scan --> result["Quality results / actions"]
  result --> policy{"Aurum policy"}
  policy -->|"Pass"| silver["Promote / continue"]
  policy -->|"Fail"| hold["Hold / investigate"]
  supported -->|"No"| gap["Need a supported source\nor future support"]
~~~

That first question is important for Aurum.

## Current PostgreSQL issue

Microsoft Purview **Data Map** can connect to PostgreSQL for metadata and lineage.

But that does **not** mean Purview's **Data Quality** feature can run DQ scans on PostgreSQL.

In Microsoft's current supported-source list for Purview Data Quality, PostgreSQL is not listed as a supported DQ source. Supported DQ sources include Azure SQL Database, Azure SQL Managed Instance, Snowflake, Google BigQuery, Fabric and others.

So for Aurum:

~~~text
PostgreSQL metadata in Purview       → possible
PostgreSQL Purview DQ scan directly  → not currently listed as supported
~~~

This distinction is important.

## US Funds example

If the US Funds table were in a supported source such as Azure SQL:

~~~text
mutual_fund_prices
        ↓
Purview profiling
        ↓
Rules:
- symbol completeness
- date completeness
- uniqueness
- validity
        ↓
Purview DQ scan
        ↓
Result
        ↓
Aurum policy
~~~

Purview provides built-in quality dimensions such as completeness, consistency, conformity, accuracy, freshness and uniqueness.

## Why it could fit Aurum later

Purview becomes interesting when the client needs both:

- data quality, and
- enterprise governance/catalog-style control.

For example, a large Azure-based client may already use Purview.

Then Aurum could work alongside Purview results instead of building every management screen from scratch.

## Main catch for current Aurum

For our **PostgreSQL-first US Funds prototype**, Purview Data Quality is not a direct DQ path today.

Also, Purview is a larger governance platform, so adopting it only for a few simple checks would be heavy.

### Easy meeting line

> "Purview is useful when the company already uses Microsoft's governance stack. It can manage profiling, quality rules and scans for supported sources. But PostgreSQL is currently supported for Purview metadata, not listed for Purview Data Quality scans, so it is not a direct fit for our current US Funds prototype."

---

# Compare the three

| Tool | Very simple meaning | Current PostgreSQL fit | Best Aurum situation | Main catch |
|---|---|---|---|---|
| AWS Glue Data Quality | AWS runs our DQ checks | **Direct route available through JDBC** | Aurum/client is on AWS or wants AWS-managed DQ | AWS setup, IAM, networking and cost |
| Google Automatic Data Quality | Google runs DQ scans | **Not the direct current route** | Data is already in BigQuery / supported Google tables | Current Aurum data would need a supported Google-side table |
| Microsoft Purview Data Quality | Microsoft runs DQ + governance scans | **PostgreSQL DQ not currently listed as supported** | Azure / Purview-heavy enterprise client | Larger platform and source-support limits |

---

# One US Funds problem across all three

Suppose the A-Z load produces:

~~~text
NULL  | 2021-01-04 | 18.00
ABCDX | 2021-01-04 | 25.40
ABCDX | 2021-01-04 | 26.10
XYZDX | 2021-01-05 | -3.00
~~~

Aurum wants to know:

~~~text
Is symbol missing?
Is fund_symbol + price_date repeated?
Does price violate the agreed rule?
~~~

The actual quality question is the same.

The difference is **where the checker runs**:

~~~text
AWS Glue
PostgreSQL → AWS managed DQ → result → Aurum

Google Automatic DQ
PostgreSQL → supported Google table → Google managed DQ → result → Aurum

Microsoft Purview
Supported Purview DQ source → Microsoft managed DQ → result → Aurum
~~~

---

# What should Aurum learn from Group 2?

Group 2 shows an important architecture idea:

> Aurum does not have to build or host every data-quality engine itself.

For a client already committed to a cloud platform, Aurum could use that platform's DQ service and keep Aurum focused on:

~~~text
orchestration
+ promotion policy
+ evidence
+ cross-layer decisions
+ reporting
~~~

For **our current PostgreSQL-first US Funds prototype**, the documentation-based fit is:

1. **AWS Glue Data Quality** is the most direct of these three because AWS supports PostgreSQL through JDBC.
2. **Google Automatic Data Quality** makes more sense when the data is already in BigQuery or another supported Google table.
3. **Microsoft Purview Data Quality** makes more sense in a supported Azure/Purview environment; current Purview DQ documentation does not list PostgreSQL as a DQ source.

This is an **architecture fit assessment**, not a benchmark or a claim that Aurum has already implemented these services.

---

# What we should test if Group 2 gets a POC

Use the same US Funds rules and the same three test situations:

~~~text
1. Clean load
2. Bad load
3. Cloud DQ job fails / cannot run
~~~

For every service, Aurum must distinguish:

~~~text
PASS
FAIL
CHECK DID NOT RUN
~~~

A cloud-service error must never be treated as a data-quality pass.

Compare connection effort, rule coverage, failed-row evidence, runtime on representative data, source-database load, API/result integration with Aurum, cloud cost, and security/network setup.

Start small before testing the 75.7M-row price table.

---

# Meeting-ready explanation

> "Group 2 is the managed-cloud group. Instead of Aurum installing the DQ engine itself, AWS, Google or Microsoft runs the checks for us. We can use the same US Funds checks, like missing fund symbol, duplicate fund plus date and invalid prices. AWS Glue is the most direct with our current PostgreSQL setup because it supports JDBC. Google's automatic DQ is mainly for BigQuery and supported Google tables, so our current PostgreSQL data would need an extra step. Purview is strong for Microsoft governance and DQ, but current Purview DQ support does not list PostgreSQL. In every case, the cloud tool only gives the quality result; Aurum still decides whether the load can move from Bronze to Silver."
