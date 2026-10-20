# Why a Single Denormalised Table for TFL Journey Data

> Understand why TFL Journeys Analysis uses one wide Snowflake table instead of a star schema, and when GROUP BY ALL is appropriate for exploratory SQL.

The TfL Journeys Analysis is built on a deliberate structural choice: a single Snowflake table rather than a normalised schema with dimension and fact tables. Understanding that decision — and the related choice to use `GROUP BY ALL` — helps you reason about when the same pattern is right for your own projects, and when it is not.

## One table, no joins

The project uses a single denormalised table, `TFL.JOURNEYS`, rather than splitting transport types or dates into separate dimension tables. The rationale is straightforward: the dataset has a small, fixed set of transport types (six in total), and every analytical question reduces to summing journey volumes across some combination of `MONTH`, `YEAR`, and `JOURNEY_TYPE`.

Denormalisation means every question is answerable with a simple `SELECT … GROUP BY` without requiring any joins. That trade-off — wider rows in exchange for simpler queries — is appropriate for exploratory analysis against a small, stable dataset. It is not the right pattern for a production data warehouse where dimension tables would need to be updated independently, referential integrity enforced, or the model extended to many more transport types or geographies.

## GROUP BY ALL

`GROUP BY ALL` is a Snowflake-specific extension that automatically groups by every selected column that is not wrapped in an aggregate function. Instead of repeating every non-aggregated column in the `GROUP BY` clause, you write `GROUP BY ALL` and let Snowflake infer the grouping set from the `SELECT` list.

**Without `GROUP BY ALL` (explicit column list):**

```sql
SELECT
    TFL.JOURNEYS.JOURNEY_TYPE,
    SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS) AS total_journeys_millions
FROM TFL.JOURNEYS
GROUP BY TFL.JOURNEYS.JOURNEY_TYPE
ORDER BY total_journeys_millions DESC;
```

**With `GROUP BY ALL` (Snowflake extension):**

```sql
SELECT
    TFL.JOURNEYS.JOURNEY_TYPE,
    SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS) AS total_journeys_millions
FROM TFL.JOURNEYS
GROUP BY ALL
ORDER BY total_journeys_millions DESC;
```

The difference is minor with a single grouping column, but `GROUP BY ALL` eliminates duplication and reduces transcription errors when the `SELECT` list grows during iterative analysis — for example, when you add `YEAR` or `MONTH` mid-exploration without having to remember to add it to the `GROUP BY` clause as well.

> [!NOTE]
> Many teams prefer explicit `GROUP BY` column lists in production SQL. Listing columns explicitly makes it unambiguous which dimensions are being grouped, guards against unintended grouping if the query is later edited, and is portable across SQL dialects that do not support `GROUP BY ALL`. Use `GROUP BY ALL` as a convenience for iterative, exploratory work; switch to explicit column lists before promoting a query to a scheduled job or shared report.

## Table grain

`TFL.JOURNEYS` has one row per `(MONTH, YEAR, JOURNEY_TYPE)` combination. Journey volume is stored in the `JOURNEYS_MILLIONS` column as a `FLOAT` representing millions of journeys. All aggregations in this project — totals by transport type, totals by year — are sums of that column filtered and grouped at whatever granularity the question requires.

For the full column definitions, data types, and valid values of `JOURNEY_TYPE`, see the [Table Schema reference](../reference/table-schema.md).
