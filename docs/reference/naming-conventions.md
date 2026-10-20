# SQL Naming Conventions for the TfL Journeys Project

> Naming rules for SQL query cells, snake_case comment prefixes, GROUP BY ALL usage, and identifier casing conventions in the TfL Journeys Analysis project.

This project follows a small set of naming and style conventions to keep SQL readable and results programmatically referenceable. The conventions below apply to all queries in the project.

## Query Cell Naming

SQL cells are prefixed with a `-- snake_case_name` comment matching the notebook's `dataFrameVariableName`. This lets results be referenced programmatically downstream without relying on positional cell indices or ad-hoc variable names.

```sql
-- most_popular_transport_types
SELECT
    TFL.JOURNEYS.JOURNEY_TYPE,
    SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS) AS total_journeys_millions
FROM TFL.JOURNEYS
GROUP BY ALL
ORDER BY total_journeys_millions DESC;
```

The comment on the first line (`-- most_popular_transport_types`) is the query's canonical name. It matches the name used in the [Query Catalogue](query-catalogue.md).

## GROUP BY ALL

`GROUP BY ALL` is a Snowflake-specific syntax extension that groups by every selected column not wrapped in an aggregate function. It is used throughout this project in place of listing grouping columns explicitly.

**Advantage:** reduces duplication and transcription errors when a `SELECT` list changes during iterative analysis. Adding or removing a non-aggregate column in the `SELECT` clause automatically adjusts the grouping without requiring a matching edit to the `GROUP BY` clause.

**Tradeoff:** less explicit than naming each column. Some teams prefer explicit `GROUP BY` column lists in production SQL to make grouping intent unambiguous and to guard against unintended grouping when a query is later edited.

## Identifier Casing

Snowflake upper-cases unquoted identifiers by default. All object names — database, table, and column — use upper case in this project (`TFL`, `JOURNEYS`, `JOURNEY_TYPE`, `JOURNEYS_MILLIONS`, etc.) for consistency with how Snowflake stores and resolves them.

> [!TIP]
> When adding a new query, always prefix it with a `-- descriptive_snake_case_name` comment and include an explicit `ORDER BY` to ensure deterministic results. For "top N" or "bottom N" questions, also add a `LIMIT` clause.

---

For the full list of named queries that follow these conventions, see the [Query Catalogue](query-catalogue.md).
