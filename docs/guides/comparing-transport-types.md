# Compare TfL Transport Types Side by Side in Snowflake

> Use conditional aggregation in Snowflake SQL to pivot two TfL transport types into columns for easy year-by-year comparison of journey volumes.

When you want to compare two transport types year by year, the most readable approach is a pivot using conditional aggregation. Instead of returning one row per `(YEAR, JOURNEY_TYPE)` pair, you write a `CASE WHEN` expression for each mode inside a `SUM`, producing one row per year with each mode in its own column. This makes it easy to scan up and down the results and spot diverging trends without needing to mentally group alternate rows.

## The pivot query

The example below places **Bus** and **Underground & DLR** side by side for every year in the dataset:

```sql
SELECT
    YEAR,
    SUM(CASE WHEN JOURNEY_TYPE = 'Bus' THEN JOURNEYS_MILLIONS END) AS bus_millions,
    SUM(CASE WHEN JOURNEY_TYPE = 'Underground & DLR' THEN JOURNEYS_MILLIONS END) AS tube_millions
FROM TFL.JOURNEYS
GROUP BY ALL
ORDER BY YEAR;
```

Each `CASE WHEN` expression evaluates to the journey volume only for the matching transport type, and to `NULL` for all other rows. `SUM` ignores nulls, so the result for each column is the total for that mode alone.

## Adapting the query for other transport types

To compare a different pair of modes, replace the string literals in the two `CASE WHEN` filters with any two values from the list of valid `JOURNEY_TYPE` values:

- `'Bus'`
- `'Underground & DLR'`
- `'Overground'`
- `'TfL Rail'`
- `'Tram'`
- `'Emirates Airline'`

Also update the column aliases (`bus_millions`, `tube_millions`) to match your chosen modes so the output remains readable.

> [!TIP]
> You can extend this pattern to three or more columns by adding further `CASE WHEN` expressions inside additional `SUM` calls. Each new expression follows the same structure — just add another `SUM(CASE WHEN JOURNEY_TYPE = '...' THEN JOURNEYS_MILLIONS END) AS alias_name` to the `SELECT` list.
