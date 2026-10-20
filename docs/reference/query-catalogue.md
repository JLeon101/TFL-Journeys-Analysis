# SQL Query Catalogue: All Three TfL Journeys Queries

> Catalogue of all three named SQL queries in the TfL Journeys Analysis project, with purpose, row counts, and complete SQL source code.

This catalogue lists every named query in the project, following the `-- snake_case_name` naming convention. Each query is self-contained and reproducible — running any of them against a populated `TFL.JOURNEYS` table will return the results shown below. For the rules governing query naming, see [Naming Conventions](naming-conventions.md).

---

## most\_popular\_transport\_types

Totals journey volume by transport type across all months and years, ranked from highest to lowest.

**Expected result rows:** 6

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

---

## emirates\_airline\_popularity

Returns the top 5 months by Emirates Airline (cable car) journey volume, excluding null values and rounding figures to two decimal places.

**Expected result rows:** 5

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

---

## least\_popular\_years\_tube

Returns the bottom 5 years by total Underground & DLR journey volume, ordered from lowest to highest.

**Expected result rows:** 5

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
