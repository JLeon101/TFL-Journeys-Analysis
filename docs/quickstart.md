# Quickstart: Run the TFL Journey Analysis End to End

> Connect to the TFL Snowflake database, inspect the JOURNEYS table, and execute all three SQL analyses to reproduce every result in this project.

This quickstart walks you through the complete TfL Journeys Analysis from first connection to final result. By the end you will have confirmed access to the `TFL.JOURNEYS` Snowflake table, inspected the data shape, and executed all three SQL queries that answer the project's business questions — reproducing every number in the analysis, even if you have never queried this dataset before.

## Step 1: Confirm access to the data

The data lives in a Snowflake database called `TFL`, in a single table: `TFL.JOURNEYS`. Snowflake identifiers (databases, tables, columns) are upper case by default, so all queries in this project use upper-case column names.

Run this sanity check to confirm you can see the table and that it is populated:

```sql
SELECT COUNT(*) AS row_count
FROM TFL.JOURNEYS;
```

You should get back a non-zero row count. The underlying extract used in this project contains **935 rows** of monthly records from 2010 onward across six transport types. If you receive zero rows or an error, verify that your Snowflake role has `SELECT` privileges on `TFL.JOURNEYS`.

## Step 2: Inspect a sample of rows

Before running any aggregations, take a moment to look at the raw data:

```sql
SELECT *
FROM TFL.JOURNEYS
LIMIT 10;
```

Note the shape of the result: **one row per `(MONTH, YEAR, JOURNEY_TYPE)` combination**, with journey volume stored in the `JOURNEYS_MILLIONS` column as a decimal figure in millions. The `REPORT_DATE` column holds the date the data was reported, and `DAYS` records the number of calendar days in that month — useful if you later want to compute a daily average.

## Step 3: Most popular transport types

Sum journey volume across all months and years, grouped by transport type, and rank the results descending to answer **Question 1: Which transport types are most heavily used overall?**

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

| JOURNEY\_TYPE | TOTAL\_JOURNEYS\_MILLIONS |
|---|---|
| Bus | 24905.19 |
| Underground & DLR | 15020.47 |
| Overground | 1666.85 |
| TfL Rail | 411.31 |
| Tram | 314.69 |
| Emirates Airline | 14.58 |

Buses carry by far the largest share of journeys — roughly 60% of all recorded volume — followed by the Underground & DLR at just under 38%.

## Step 4: Emirates Airline best months

Filter to the Emirates Airline cable car, exclude null rows, round the figures, and take the top 5 months by volume to answer **Question 2: How has the Emirates Airline performed in its most popular months?**

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

| MONTH | YEAR | ROUNDED\_JOURNEYS\_MILLIONS |
|---|---|---|
| 5 | 2012 | 0.53 |
| 6 | 2012 | 0.38 |
| 4 | 2012 | 0.24 |
| 5 | 2013 | 0.19 |
| 5 | 2015 | 0.19 |

The cable car's single busiest month is May 2012 — a clear spike driven by the run-up to the **London 2012 Olympics**, for which the Emirates Airline was originally built as event infrastructure.

## Step 5: Least popular years for the Underground & DLR

Filter to the Underground & DLR, sum journey volumes by year, and surface the five lowest-volume years to answer **Question 3: Which years saw the lowest usage of the Underground & DLR?**

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

| YEAR | JOURNEY\_TYPE | TOTAL\_JOURNEYS\_MILLIONS |
|---|---|---|
| 2020 | Underground & DLR | 310.18 |
| 2021 | Underground & DLR | 748.45 |
| 2022 | Underground & DLR | 1064.86 |
| 2010 | Underground & DLR | 1096.15 |
| 2011 | Underground & DLR | 1156.65 |

The three lowest years (2020–2022) directly reflect the **COVID-19 pandemic**, during which UK government guidance restricted non-essential travel and encouraged remote working, causing a dramatic collapse in commuter traffic into central London; 2010 and 2011 are low because the network was simply carrying fewer total passengers in those earlier years.

You have now reproduced the full TfL Journeys Analysis end to end.

> [!TIP]
> Ready to go further? Head to the **Guides** section to learn how to check for null journey volumes, pivot two transport types side by side, calculate year-over-year change, isolate a single calendar month across all years, and add entirely new questions to the project.
