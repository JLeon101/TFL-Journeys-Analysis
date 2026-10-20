# Calculate Year-Over-Year TfL Journey Volume Change

> Use a Snowflake CTE and LAG window function to compute year-over-year journey volume changes for any TfL transport type from TFL.JOURNEYS.

A simple annual total shows you how busy each year was, but it does not tell you whether ridership was rising or falling compared to the year before. To get that delta, combine a Common Table Expression (CTE) with the `LAG` window function. The CTE first collapses the monthly rows into a single annual total per year; the outer `SELECT` then uses `LAG` to look back at the previous year's total and subtract it, producing a `change_millions` column that is positive when ridership grew and negative when it fell.

## The year-over-year query

The example below calculates annual totals and year-over-year change for **Underground & DLR**:

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

## How each clause works

- **CTE (`yearly`):** Filters to a single transport type, then sums `JOURNEYS_MILLIONS` across all months to produce one `total_millions` figure per `YEAR`. `GROUP BY ALL` groups by every non-aggregated column in the `SELECT` list — in this case, just `YEAR`.
- **`LAG(total_millions) OVER (ORDER BY YEAR)`:** For each row, returns the `total_millions` value from the immediately preceding row when rows are ordered by `YEAR`. This gives you the prior year's total without a self-join.
- **`total_millions - LAG(...)`:** Subtracts the prior year's total from the current year's total to produce the absolute change in millions of journeys.

> [!NOTE]
> The first year in the result set will always have `NULL` for `change_millions` — there is no prior year to compare against. This is expected behaviour from `LAG` when no `DEFAULT` value is specified.

## Adapting the query for other transport types

Replace `'Underground & DLR'` in the `WHERE` clause with any other valid `JOURNEY_TYPE` value. The rest of the query is mode-agnostic and requires no other changes.
