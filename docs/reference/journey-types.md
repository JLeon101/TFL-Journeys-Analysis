# JOURNEY_TYPE Values: Six TfL Transport Modes Explained

> All six valid JOURNEY_TYPE string values in the TFL.JOURNEYS Snowflake table, with descriptions of each Transport for London mode.

`JOURNEY_TYPE` is a `VARCHAR` column in `TFL.JOURNEYS`. Six distinct values are present in the dataset, each corresponding to a different Transport for London transport mode. Knowing the exact strings is important for `WHERE` clause filters — an incorrect value will silently return zero rows rather than raising an error.

## Valid Values

| Value | Notes |
|---|---|
| Bus | London bus network |
| Underground & DLR | London Underground and Docklands Light Railway, combined |
| Overground | London Overground |
| TfL Rail | Predecessor branding to the Elizabeth line |
| Tram | London Trams (Croydon) |
| Emirates Airline | Cable car linking the Greenwich Peninsula and Royal Docks |

> [!WARNING]
> `JOURNEY_TYPE` values are case-sensitive when used in `WHERE` clauses with quoted string literals. Use the exact capitalisation shown in the table above — for example, `'Underground & DLR'` not `'underground & dlr'` or `'Underground & dlr'`.

## Example Filter

```sql
WHERE JOURNEY_TYPE = 'Underground & DLR'
```

## Notes on Emirates Airline

Emirates Airline has an order-of-magnitude lower ridership than all other transport types in the dataset. It would be invisible in any summary chart dominated by bus and Underground figures. For analysis of this mode in isolation, see [Emirates Airline](../explanation/emirates-airline.md).
