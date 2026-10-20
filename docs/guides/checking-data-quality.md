# Check for Null Journey Volumes in TFL.JOURNEYS Table

> Learn how to identify null or missing journey volumes in the TFL.JOURNEYS table, and why TfL Rail and Emirates Airline have expected nulls in early years.

Not every row in `TFL.JOURNEYS` contains a journey volume. Some transport types did not exist for the full date range covered by the dataset — they entered service partway through, leaving `JOURNEYS_MILLIONS` as `NULL` for the months before they launched. Before writing any aggregation query, it is worth knowing exactly which transport types are affected and how many null rows they carry.

## Run the null-check query

The query below counts null rows per transport type, so you can see at a glance whether missing values are concentrated in one mode or spread across many:

```sql
SELECT JOURNEY_TYPE, COUNT(*) AS null_rows
FROM TFL.JOURNEYS
WHERE JOURNEYS_MILLIONS IS NULL
GROUP BY ALL;
```

Run this against your Snowflake worksheet before starting any analysis that sums or averages journey volumes.

## Interpreting the results

The two transport types most likely to appear in the results are **TfL Rail** and **Emirates Airline**. Both entered service partway through the dataset's date range, so `JOURNEYS_MILLIONS` is `NULL` for the months before each launched. These nulls are an accurate reflection of the data, not a sign of a loading error or a missing file.

> [!NOTE]
> Nulls for **TfL Rail** and **Emirates Airline** in early years are expected — they are not data errors. Both modes entered service after the dataset's start date, so no journey figures exist for those earlier periods.

> [!TIP]
> Always run this null-check before writing aggregation queries such as `SUM` or `AVG` on `JOURNEYS_MILLIONS`. Knowing which transport types carry nulls lets you decide whether to add a `WHERE JOURNEYS_MILLIONS IS NOT NULL` filter or to use `SUM` (which ignores nulls by default in SQL) without an explicit guard.
