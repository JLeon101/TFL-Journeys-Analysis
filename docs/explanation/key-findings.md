# Key Findings: London Transport Journey Volume Trends

> Summary of the three analytical findings from the TfL Journeys Analysis: bus dominance, Emirates Airline seasonality, and Underground usage dips.

The TfL Journeys Analysis answers three questions about journey volume across London's public transport network. Together, the results reveal a striking concentration of ridership in buses and the Underground, an Olympics-driven spike in cable car usage, and the severe but temporary suppression of Underground commuter traffic during the COVID-19 pandemic.

## Most popular transport types

Summing all journey volumes across every month and year in the dataset, ranked by total ridership:

| JOURNEY\_TYPE | TOTAL\_JOURNEYS\_MILLIONS |
|---|---|
| Bus | 24,905.19 |
| Underground & DLR | 15,020.47 |
| Overground | 1,666.85 |
| TfL Rail | 411.31 |
| Tram | 314.69 |
| Emirates Airline | 14.58 |

Buses account for roughly 60% of all journeys in the dataset — nearly double the Underground & DLR's share. This dominance reflects the bus network's combination of reach and affordability: it serves areas the Underground does not, is not concentrated primarily within Zones 1–3, and has a lower per-journey cost accessible to a wider cross-section of Londoners. The Underground & DLR, despite its cultural prominence, is fundamentally a central-London commuter network; the bus is the backbone of the wider city.

> [!TIP]
> Comparing Emirates Airline's total of 14.58 million journeys against bus or Underground figures in the same table is misleading. The cable car operates at an order-of-magnitude lower scale than every other mode, so aggregate comparisons obscure rather than reveal its patterns. The Emirates Airline is best analysed at its own scale — see [Emirates Airline: Ridership Patterns and Scale](emirates-airline.md) for a dedicated breakdown.

## Emirates Airline peak months

Filtering to the cable car and ranking months by rounded journey volume, the top five months are:

| MONTH | YEAR | ROUNDED\_JOURNEYS\_MILLIONS |
|---|---|---|
| 5 | 2012 | 0.53 |
| 6 | 2012 | 0.38 |
| 4 | 2012 | 0.24 |
| 5 | 2013 | 0.19 |
| 5 | 2015 | 0.19 |

The cable car's single busiest month is May 2012. The Emirates Airline cable car was built as event infrastructure for the London 2012 Olympics; the Games opened in July 2012, and the spike in the months immediately preceding them reflects Olympics-driven build-up tourism and early visitor activity rather than regular commuter demand. After 2012, May continues to appear in the top months, suggesting a consistent seasonal pattern, but at volumes far below the Olympics peak.

## Least popular years for Underground & DLR

Filtering to Underground & DLR journeys and ranking years by total annual volume ascending, the five lowest years are:

| YEAR | JOURNEY\_TYPE | TOTAL\_JOURNEYS\_MILLIONS |
|---|---|---|
| 2020 | Underground & DLR | 310.18 |
| 2021 | Underground & DLR | 748.45 |
| 2022 | Underground & DLR | 1,064.86 |
| 2010 | Underground & DLR | 1,096.15 |
| 2011 | Underground & DLR | 1,156.65 |

The three lowest years — 2020, 2021, and 2022 — span the COVID-19 pandemic. UK government guidance repeatedly restricted non-essential travel and encouraged remote work, and the Underground & DLR was disproportionately affected because it depends heavily on commuter traffic into central London. 2010 and 2011 appear in the bottom five for a different reason entirely: those years are simply earlier in the dataset's history, when the network was carrying fewer total passengers. They represent a growth baseline, not a disruption.

For a deeper analysis of the pandemic's effect on Underground ridership, see [How COVID-19 Affected London Underground & DLR Ridership](pandemic-impact.md). For a dedicated look at the cable car's seasonal patterns, see [Emirates Airline Cable Car: Ridership Patterns and Scale](emirates-airline.md).
