# Isolate Monthly Seasonal Patterns in TfL Journey Data

> Fix the MONTH filter to a single calendar month and vary YEAR to reveal seasonal trends, using Emirates Airline May volumes as a worked example.

Aggregating journey volumes by year gives a useful high-level view, but it hides the within-year variation that reveals whether a transport mode is weather-sensitive, event-driven, or genuinely seasonal. By fixing the `MONTH` filter to a single calendar month and letting `YEAR` vary freely, you can ask a much sharper question: was a particular month consistently strong across many years, or was a spike in one year an isolated event? For example, was May always the Emirates Airline's best month, or was 2012 a one-off driven by a specific event?

## The seasonal query

The example below retrieves May journey volumes for **Emirates Airline** across every year in the dataset:

```sql
SELECT YEAR, ROUND(JOURNEYS_MILLIONS, 2) AS journeys_millions
FROM TFL.JOURNEYS
WHERE JOURNEY_TYPE = 'Emirates Airline'
    AND MONTH = 5
ORDER BY YEAR;
```

`MONTH` is a numeric column — `5` means May. See the note below for the full mapping.

## Interpreting the results

Running this query lets you scan May figures year by year and immediately see whether the volume is flat, trending, or spiked. For the Emirates Airline, the data shows a sharp peak in May 2012 that is far above any other year in the result set. That peak coincides with the run-up to the London 2012 Olympics, for which the cable car was originally built as event infrastructure. To read more about why the Emirates Airline figures behave differently from all other modes, see the [Emirates Airline explanation](../explanation/emirates-airline.md).

Without isolating a single month like this, the Olympic-year spike would be partially averaged away inside the annual total, making it harder to attribute.

> [!TIP]
> Chart the results year-by-year (e.g. in a notebook or BI tool) rather than reading raw numbers — a line chart over `YEAR` makes recurring seasonal peaks immediately visible and separates genuine seasonality from one-off spikes at a glance.

## Month number reference

The `MONTH` column is numeric. Use the mapping below when setting your filter:

| Value | Month |
|-------|-------|
| `1` | January |
| `2` | February |
| `3` | March |
| `4` | April |
| `5` | May |
| `6` | June |
| `7` | July |
| `8` | August |
| `9` | September |
| `10` | October |
| `11` | November |
| `12` | December |
