# TFL.JOURNEYS Table Schema: Columns, Types, and Grain

> Full column definitions, data types, grain, and identifier casing rules for the TFL.JOURNEYS Snowflake table used in the TfL journey analysis.

`TFL.JOURNEYS` is the single table backing all analyses in this project. It lives in a Snowflake database named `TFL` and holds monthly journey-volume records for six Transport for London transport modes. Every query in the project reads from this one table — no joins are required.

## Table Grain

One row per `(MONTH, YEAR, JOURNEY_TYPE)` combination. A single calendar month for a single transport type is therefore represented by exactly one row, with the journey volume for that combination stored in the `JOURNEYS_MILLIONS` column.

## Column Reference

| Column | Data Type | Definition |
|---|---|---|
| MONTH | INTEGER | Month in numeric format, e.g. 1 = January |
| YEAR | INTEGER | Calendar year |
| DAYS | INTEGER | Number of days in the given month |
| REPORT_DATE | DATE | Date the data was reported |
| JOURNEY_TYPE | VARCHAR | Method of transport |
| JOURNEYS_MILLIONS | FLOAT | Journey volume, in millions |

## Identifier Casing

Snowflake databases, tables, and columns are upper case by default. Queries in this project use upper case for consistency with the underlying object names, though Snowflake identifiers are case-insensitive unless quoted.

> [!NOTE]
> The source CSV uses lower-case column names (`month`, `year`, `days`, `report_date`, `journey_type`, `journeys_millions`) — Snowflake upper-cases these on ingestion to `MONTH`, `YEAR`, `DAYS`, `REPORT_DATE`, `JOURNEY_TYPE`, and `JOURNEYS_MILLIONS`.

## Quick Verification Query

Run the following to confirm the table is accessible and populated:

```sql
SELECT COUNT(*) AS row_count
FROM TFL.JOURNEYS;
```

You should receive a non-zero row count. The underlying extract contains monthly records from 2010 onward across six transport types.

---

For the full list of valid values in the `JOURNEY_TYPE` column, see [Journey Types](journey-types.md).
