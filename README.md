# TFL-Journeys-Analysis

*A SQL project analysing public transport journey volumes for Transport for London (TfL)*

![Tower Bridge, London](london.jpg)

---

## 0. Overview

Transport for London (TfL) is the local government body, established in 2000 and managed by the Mayor of London's office, responsible for London's road network, taxis, and public transport — including the Underground, Overground, Docklands Light Railway (DLR), buses, trams, river services, and the Emirates Airline cable car.

![London Underground train departing a platform](tube.jpg)

The Mayor of London's office publishes transport usage data at [data.london.gov.uk](https://data.london.gov.uk/dataset). This project uses a cleaned and modified extract of that dataset, loaded into a Snowflake database, to answer three business questions about journey volume by transport type over time:

1. Which transport types are most heavily used overall?
2. How has the Emirates Airline cable car performed in its most popular months?
3. Which years saw the lowest usage of the Underground & DLR?

---

## 1. Tutorial: Your First Analysis

*Goal: Complete full analysis end-to-end and reproduce every result in this project, even if you've never queried this dataset before.*

### Step 1 — Confirm access to the data

The data lives in a Snowflake database called `TFL`, in a single table: `TFL.JOURNEYS`. Snowflake identifiers (databases, tables, columns) are upper case by default, so queries below use upper-case column names.

Run a sanity check to confirm you can see the table and that it's populated:

```sql
SELECT COUNT(*) AS row_count
FROM TFL.JOURNEYS;
```

You should get back a non-zero row count (the underlying extract used in this project contains monthly records from 2010 onward across six transport types).

### Step 2 — Look at a sample of rows

```sql
SELECT *
FROM TFL.JOURNEYS
LIMIT 10;
```

Take a moment to note the shape of the data: one row per `(MONTH, YEAR, JOURNEY_TYPE)` combination, with the journey volume stored in millions as a decimal.

### Step 3 — Answer Question 1: Most popular transport types

Sum journey volume across all months/years, grouped by transport type, ranked descending:

```sql
-- most_popular_transport_types
SELECT
    TFL.JOURNEYS.JOURNEY_TYPE,
    SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS) AS total_journeys_millions
FROM TFL.JOURNEYS
GROUP BY ALL
ORDER BY total_journeys_millions DESC;
```

**Result:**

| JOURNEY_TYPE | TOTAL_JOURNEYS_MILLIONS |
|---|---|
| Bus | 24,905.19 |
| Underground & DLR | 15,020.47 |
| Overground | 1,666.85 |
| TfL Rail | 411.31 |
| Tram | 314.69 |
| Emirates Airline | 14.58 |

Buses carry by far the largest share of journeys, followed by the Underground & DLR.

### Step 4 — Answer Question 2: Emirates Airline's best months

Filter to the cable car, exclude nulls, round the figures, and take the top 5 months by volume:

```sql
-- emirates_airline_popularity
SELECT
    MONTH,
    YEAR,
    ROUND(TFL.JOURNEYS.JOURNEYS_MILLIONS, 2) AS rounded_journeys_millions
FROM TFL.JOURNEYS
WHERE TFL.JOURNEYS.JOURNEYS_MILLIONS IS NOT NULL
    AND TFL.JOURNEYS.JOURNEY_TYPE = 'Emirates Airline'
GROUP BY ALL
ORDER BY rounded_journeys_millions DESC
LIMIT 5;
```

**Result:**

| MONTH | YEAR | ROUNDED_JOURNEYS_MILLIONS |
|---|---|---|
| 5 | 2012 | 0.53 |
| 6 | 2012 | 0.38 |
| 4 | 2012 | 0.24 |
| 5 | 2013 | 0.19 |
| 5 | 2015 | 0.19 |

The cable car's single busiest month is May 2012 — unsurprising, as this coincides with the run-up to the London 2012 Olympics.

### Step 5 — Answer Question 3: Least popular years for the Underground & DLR

Filter to the Underground & DLR, sum by year, and take the 5 lowest-volume years:

```sql
-- least_popular_years_tube
SELECT
    YEAR,
    TFL.JOURNEYS.JOURNEY_TYPE,
    SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS) AS total_journeys_millions
FROM TFL.JOURNEYS
WHERE TFL.JOURNEYS.JOURNEY_TYPE = 'Underground & DLR'
GROUP BY ALL
ORDER BY total_journeys_millions ASC
LIMIT 5;
```

**Result:**

| YEAR | JOURNEY_TYPE | TOTAL_JOURNEYS_MILLIONS |
|---|---|---|
| 2020 | Underground & DLR | 310.18 |
| 2021 | Underground & DLR | 748.45 |
| 2022 | Underground & DLR | 1,064.86 |
| 2010 | Underground & DLR | 1,096.15 |
| 2011 | Underground & DLR | 1,156.65 |

The three lowest years (2020–2022) reflect the COVID-19 pandemic's impact on commuting; 2010 and 2011 are low simply because the network was carrying fewer total passengers a decade earlier.

**You've now reproduced the full project.** The next sections help you go beyond these three queries.

---

## 2. How-To Guides

*Goal-oriented recipes for common tasks. Assumes familiarity with Step 1–2 of the tutorial.*

### How to check for missing or null journey volumes

```sql
SELECT JOURNEY_TYPE, COUNT(*) AS null_rows
FROM TFL.JOURNEYS
WHERE JOURNEYS_MILLIONS IS NULL
GROUP BY ALL;
```

Some transport types (e.g. TfL Rail, Emirates Airline) only entered service partway through the dataset's date range, so nulls or absent rows are expected for their early years.

### How to compare two transport types side by side

Pivot two `JOURNEY_TYPE` filters into columns using conditional aggregation:

```sql
SELECT
    YEAR,
    SUM(CASE WHEN JOURNEY_TYPE = 'Bus' THEN JOURNEYS_MILLIONS END) AS bus_millions,
    SUM(CASE WHEN JOURNEY_TYPE = 'Underground & DLR' THEN JOURNEYS_MILLIONS END) AS tube_millions
FROM TFL.JOURNEYS
GROUP BY ALL
ORDER BY YEAR;
```

### How to calculate year-over-year change for a transport type

```sql
WITH yearly AS (
    SELECT YEAR, SUM(JOURNEYS_MILLIONS) AS total_millions
    FROM TFL.JOURNEYS
    WHERE JOURNEY_TYPE = 'Underground & DLR'
    GROUP BY ALL
)
SELECT
    YEAR,
    total_millions,
    total_millions - LAG(total_millions) OVER (ORDER BY YEAR) AS change_millions
FROM yearly
ORDER BY YEAR;
```

### How to isolate a single calendar month across all years

Useful for spotting seasonal patterns (e.g. was May consistently strong for the cable car, or was 2012 an outlier?):

```sql
SELECT YEAR, ROUND(JOURNEYS_MILLIONS, 2) AS journeys_millions
FROM TFL.JOURNEYS
WHERE JOURNEY_TYPE = 'Emirates Airline'
    AND MONTH = 5
ORDER BY YEAR;
```

### How to add a new question to this project

1. Write and test the SQL in a new notebook cell, prefixed with a `-- descriptive_snake_case_name` comment (this project's convention — see [Reference: naming conventions](#naming-conventions)).
2. Confirm the result set is deterministic — add an explicit `ORDER BY` and a `LIMIT` if the question asks for "top N" or "bottom N" results.
3. Sanity-check totals against Question 1's output, since every other transport-type total should be internally consistent with it.
4. Document the finding in one sentence, as done in Tutorial Steps 3–5.

### How to load a fresh extract of the source CSV

If working from the raw `tfl_journeys_final.csv` file rather than the live Snowflake table, load it with explicit typing so `MONTH`, `YEAR`, and `DAYS` aren't inferred as floats:

```python
import pandas as pd

df = pd.read_csv(
    "tfl_journeys_final.csv",
    dtype={"month": "int64", "year": "int64", "days": "int64", "journey_type": "string"},
    parse_dates=["report_date"],
)
```

---

## 3. Reference

*Information-oriented technical description. No narrative — just the facts.*

### Table: `TFL.JOURNEYS`

| Column | Definition | Data type |
|---|---|---|
| `MONTH` | Month in numeric format, e.g. `1` = January | `INTEGER` |
| `YEAR` | Calendar year | `INTEGER` |
| `DAYS` | Number of days in the given month | `INTEGER` |
| `REPORT_DATE` | Date the data was reported | `DATE` |
| `JOURNEY_TYPE` | Method of transport | `VARCHAR` |
| `JOURNEYS_MILLIONS` | Journey volume, in millions | `FLOAT` |

**Grain:** one row per `(MONTH, YEAR, JOURNEY_TYPE)`.

**Identifier case:** Snowflake databases, tables, and columns are upper case by default. Queries in this project use upper case for consistency with the underlying object names, though Snowflake identifiers are case-insensitive unless quoted.

### Valid values of `JOURNEY_TYPE`

| Value | Notes |
|---|---|
| `Bus` | London bus network |
| `Underground & DLR` | London Underground and Docklands Light Railway, combined |
| `Overground` | London Overground |
| `TfL Rail` | Predecessor branding to the Elizabeth line |
| `Tram` | London Trams (Croydon) |
| `Emirates Airline` | Cable car linking the Greenwich Peninsula and Royal Docks |

### Source CSV: `tfl_journeys_final.csv`

- 935 data rows (plus header), columns: `month, year, days, report_date, journey_type, journeys_millions`
- `report_date` is stored as `MM/DD/YYYY`
- Lower-case column headers — Snowflake upper-cases these on ingestion

### Query catalogue

| Query name | Purpose | Result rows |
|---|---|---|
| `most_popular_transport_types` | Total journeys by transport type, all-time | 6 |
| `emirates_airline_popularity` | Top 5 months by cable car volume | 5 |
| `least_popular_years_tube` | Bottom 5 years by Underground & DLR volume | 5 |

### Naming conventions

- SQL cells are prefixed with a `-- snake_case_name` comment matching the notebook's `dataFrameVariableName`, so results can be referenced programmatically downstream.
- `GROUP BY ALL` (Snowflake-specific syntax) is used in place of listing grouping columns explicitly, since every non-aggregated selected column is intended to be grouped.

---

## 4. Explanation

*Understanding-oriented discussion of context and design decisions.*

### Why Snowflake, and why one wide table?

The project deliberately uses a single denormalised table (`TFL.JOURNEYS`) rather than splitting transport types or dates into separate dimension tables. For a dataset of this size and a fixed, small set of transport types, denormalisation keeps every question answerable with a simple `SELECT … GROUP BY` rather than requiring joins — appropriate for exploratory analysis rather than a production data warehouse.

### Why `GROUP BY ALL` instead of listing columns?

`GROUP BY ALL` is a Snowflake extension that groups by every selected column that isn't wrapped in an aggregate function. It trades a small amount of explicitness for reduced duplication and fewer transcription errors when a `SELECT` list changes during iterative analysis — a reasonable trade-off in an exploratory notebook, though many teams prefer explicit `GROUP BY` column lists in production SQL for readability and to guard against unintended grouping when a query is edited later.

### Why does bus ridership dominate?

London's bus network is the most extensive and accessible mode covered by this dataset — it reaches areas the Underground doesn't, has a lower per-journey cost, and (unlike the Underground & DLR) isn't concentrated primarily within Zones 1–3. That combination of coverage and affordability is the likely driver behind buses accounting for roughly 60% of all journeys in the dataset, nearly double the Underground & DLR's share.

### Why do 2020–2022 stand out for the Underground?

These years span the COVID-19 pandemic, during which UK government guidance repeatedly restricted non-essential travel and encouraged remote work. The Underground & DLR, which depends heavily on commuter traffic into central London, was disproportionately affected compared to modes with more distributed, local usage patterns — consistent with the sharp dip visible in the "least popular years" result.

### Why look at Emirates Airline separately?

The Emirates Airline cable car has an order-of-magnitude lower ridership than every other mode (see the [most popular transport types](#step-3--answer-question-1-most-popular-transport-types) result), so it would be invisible in any chart or summary dominated by bus and Underground figures. Isolating it lets its own seasonal pattern — a clear spike around May 2012, coinciding with the London 2012 Olympics for which the cable car was built as event infrastructure — become visible rather than being lost in the scale of the larger modes.

### A note on data provenance

This dataset is a modified extract of TfL usage statistics published via the Mayor of London's open data portal. As with any open government dataset, treat exact totals as indicative rather than authoritative for anything beyond exploratory analysis; for citations or downstream reporting, refer back to the original source.

---

## Appendix: Files in this project

| File | Description |
|---|---|
| `tfl_journeys_final.csv` | Raw source extract used to populate `TFL.JOURNEYS` |
| `notebook.ipynb` | Notebook containing the three SQL analyses in this document |
| `london.jpg` | Tower Bridge — used as a cover image |
| `tube.jpg` | Underground train departing a platform — used as a cover image |
