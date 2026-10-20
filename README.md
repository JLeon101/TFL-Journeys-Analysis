[README.md](https://github.com/user-attachments/files/33695945/README.md)
# TFL Journeys Analysis: Explore London Transport Data with SQL

> A Snowflake SQL project analysing Transport for London journey volumes across six transport types from 2010 onward, using open data from the Mayor of London.

![Tower Bridge, London](london.jpg)

The TFL Journeys Analysis project uses a cleaned extract of Transport for London (TFL) usage statistics — published via the Mayor of London's open data portal — loaded into a Snowflake database. It answers three concrete business questions about journey volumes across buses, the Underground & DLR, Overground, TfL Rail, Trams, and the Emirates Airline cable car.

- **[Introduction](docs/introduction.md)** — Understand the dataset, data model, and what questions this project answers.
- **[Quickstart](docs/quickstart.md)** — Run all three SQL analyses end-to-end and reproduce every result in minutes.
- **[Guides](docs/guides/checking-data-quality.md)** — Goal-oriented recipes for null checks, pivots, year-over-year trends, and more.
- **[Reference](docs/reference/table-schema.md)** — Table schema, column definitions, valid journey types, and the full query catalogue.

## What's in this project

- **935 Data Rows** — Monthly journey records from January 2010 across six transport modes.
- **3 Core Queries** — Named SQL queries answering specific business questions about TfL ridership.
- **Snowflake SQL** — Built on a single denormalised table optimised for exploratory analysis.

## Getting started

### Step 1: Confirm data access

Connect to the `TFL` Snowflake database and run a row-count sanity check on `TFL.JOURNEYS`.

### Step 2: Inspect the schema

Review the [table schema](docs/reference/table-schema.md) to understand the grain, column types, and valid journey type values.

### Step 3: Run the analysis

Follow the [Quickstart](docs/quickstart.md) to execute all three SQL queries and compare your results to the expected output.

### Step 4: Extend the analysis

Use the [Guides](docs/guides/checking-data-quality.md) to write additional queries — pivots, year-over-year trends, seasonal slices, and more.

> [!NOTE]
> This dataset is a modified extract of TfL usage statistics. Treat totals as indicative for exploratory analysis. For authoritative figures, refer to the original source at [data.london.gov.uk](https://data.london.gov.uk/dataset).

## Documentation

Browse every page in the project, grouped as in the documentation site.

### Get Started

- [Introduction](docs/introduction.md)
- [Quickstart](docs/quickstart.md)

### Guides

- [Data Quality](docs/guides/checking-data-quality.md)
- [Compare Types](docs/guides/comparing-transport-types.md)
- [YoY Analysis](docs/guides/year-over-year-analysis.md)
- [Seasonal Patterns](docs/guides/seasonal-patterns.md)
- [Load CSV Data](docs/guides/loading-csv-data.md)

### Explanation

- [Data Model Design](docs/explanation/data-model-design.md)
- [Key Findings](docs/explanation/key-findings.md)
- [Pandemic Impact](docs/explanation/pandemic-impact.md)
- [Emirates Airline](docs/explanation/emirates-airline.md)

### Reference

- [Table Schema](docs/reference/table-schema.md)
- [Journey Types](docs/reference/journey-types.md)
- [Query Catalogue](docs/reference/query-catalogue.md)
- [CSV Source](docs/reference/csv-source.md)
- [Naming Conventions](docs/reference/naming-conventions.md)
