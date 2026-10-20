# How COVID-19 Affected London Underground & DLR Ridership

> Explore why 2020–2022 saw the lowest Underground & DLR journey volumes in the TfL dataset, and how commuter dependency amplified the effect.

Three of the five lowest-volume years for London Underground & DLR in the entire dataset fall between 2020 and 2022. The scale of that dip — and what separates it from the other two low years, 2010 and 2011 — tells a clear story about the network's structural dependence on commuter traffic and the asymmetric impact of pandemic-era travel restrictions.

## Results: bottom 5 years by Underground & DLR volume

| YEAR | JOURNEY\_TYPE | TOTAL\_JOURNEYS\_MILLIONS |
|---|---|---|
| 2020 | Underground & DLR | 310.18 |
| 2021 | Underground & DLR | 748.45 |
| 2022 | Underground & DLR | 1,064.86 |
| 2010 | Underground & DLR | 1,096.15 |
| 2011 | Underground & DLR | 1,156.65 |

## Why the Underground was disproportionately affected

The Underground & DLR is primarily a commuter network: it is concentrated within Zones 1–3 and connects outer residential boroughs to central London workplaces. When UK government guidance restricted non-essential travel and encouraged remote working during the COVID-19 pandemic, the Underground lost the bulk of its demand almost overnight. Modes with more distributed, local usage patterns — such as buses, which serve neighbourhoods across all zones for short, often essential trips — were affected less severely by the same restrictions.

The 2020 figure of 310.18 million journeys represents roughly a 75% reduction from a typical pre-pandemic year. The partial recoveries in 2021 (748.45M) and 2022 (1,064.86M) reflect successive waves, changing restrictions, and the gradual, uneven return of commuters to offices — none of which restored ridership to pre-pandemic levels within the dataset's timeframe.

## Contrast with 2010 and 2011

The presence of 2010 and 2011 in the bottom five is not a sign of disruption. Those years are low simply because the network was carrying fewer total passengers a decade earlier — they represent an earlier point in the dataset's growth trajectory, not a shock. The gap between 2011 (1,156.65M) and 2022 (1,064.86M) is small, which shows just how severe the pandemic suppression was: three years after 2019, the network had still not recovered to where it stood in 2011.

> [!NOTE]
> The dataset reflects observed ridership, not underlying demand. Suppressed journey volumes during lockdown periods do not mean the Underground is less useful or that passenger preferences shifted permanently away from the network — many journeys simply did not happen because they were legally or practically prevented. Treat the 2020–2022 figures as reflecting a disruption to supply and behaviour, not a structural decline in the mode's relevance to London's transport system.

## Query

The following query produces the results table above:

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

To understand how ridership changed year-on-year rather than simply ranking years by total, see the [Year-over-Year Analysis guide](../guides/year-over-year-analysis.md) for the LAG-based query pattern.
