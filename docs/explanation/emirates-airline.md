# Emirates Airline Cable Car: Ridership Patterns and Scale

> Why the Emirates Airline cable car is analysed separately from other TfL modes, and what the May 2012 ridership spike reveals about its purpose.

The Emirates Airline cable car links the Greenwich Peninsula and the Royal Docks across the River Thames. It is one of six transport types covered in the TfL Journeys dataset, but it operates at an order-of-magnitude lower ridership than every other mode — its all-time total of 14.58 million journeys is less than a tenth of the next lowest mode (Tram at 314.69 million). That difference in scale is not just a footnote; it fundamentally shapes how the cable car must be analysed.

## Why the Emirates Airline is analysed in isolation

If the cable car's figures are included in charts or summary tables alongside bus and Underground numbers, its seasonal pattern becomes invisible. At the scale of 24,905 million bus journeys or 15,020 million Underground & DLR journeys, the cable car's monthly fluctuations — measured in fractions of a million — compress to a flat line. Isolating it and working at its own scale is the only way to see its behaviour clearly: a sharp spike in mid-2012 followed by a consistent but much lower seasonal pattern in subsequent years.

## Top 5 months by ridership

Filtering to the Emirates Airline and ranking months by rounded journey volume:

| MONTH | YEAR | ROUNDED\_JOURNEYS\_MILLIONS |
|---|---|---|
| 5 | 2012 | 0.53 |
| 6 | 2012 | 0.38 |
| 4 | 2012 | 0.24 |
| 5 | 2013 | 0.19 |
| 5 | 2015 | 0.19 |

## The May 2012 peak and its Olympic origins

The cable car's busiest month by a wide margin is May 2012, with 0.53 million journeys — nearly 40% more than the second-busiest month (June 2012, 0.38M). This spike is directly tied to the cable car's origins: it was built as event infrastructure for the London 2012 Olympic and Paralympic Games, designed to provide an additional river crossing between the O2 Arena on the Greenwich Peninsula and the ExCeL exhibition centre in the Royal Docks, both of which hosted Olympic events.

> [!NOTE]
> May 2012 precedes the Olympic Games opening ceremony, which took place on 27 July 2012. The ridership spike in April and May 2012 therefore reflects pre-Games anticipation, early visitor arrivals, and media interest in the new cable car, rather than direct Olympics event traffic. June 2012 — also elevated — bridges the pre-Games period and the start of the Games themselves. After the Games concluded in September 2012, ridership fell sharply and has never returned to 2012 levels.

The appearance of May 2013 and May 2015 in the top five (both at 0.19M) suggests a consistent late-spring seasonal pattern in subsequent years, likely driven by tourism and leisure use during warmer months. However, even those peak months are less than 40% of the May 2012 figure, confirming that the 2012 spike reflects Olympics-driven demand rather than regular commuter behaviour.

## Query

The following query produces the top-5 results table above:

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

To inspect the cable car's month-by-month pattern across all years — for example, to confirm whether May is consistently the strongest month — see the [Seasonal Patterns guide](../guides/seasonal-patterns.md).
