[introduction.md](https://github.com/user-attachments/files/33696185/introduction.md)
# TFL Journeys Analysis: London Transport Data in Snowflake

> Learn how TFL Journeys Analysis uses a Snowflake SQL database to explore Transport for London journey volumes across six transport types from 2010 onward.

Transport for London (TFL) is the local government body established in 2000 and managed by the Mayor of London's office, responsible for London's road network, taxis, and public transport — including the Underground, Overground, Docklands Light Railway (DLR), buses, trams, river services, and the Emirates Airline cable car. The Mayor of London's office publishes transport usage statistics at [data.london.gov.uk](https://data.london.gov.uk/dataset), making it one of the most detailed open datasets on urban mobility in the world. This project loads a cleaned and modified extract of that data into a Snowflake database and uses SQL to answer targeted questions about how Londoners move around their city.

## The Dataset

The dataset contains **935 rows** of monthly records spanning from **2010 onward**, covering **six transport types**: Bus, Underground & DLR, Overground, TfL Rail, Tram, and Emirates Airline. Each row represents one month of journey activity for a single transport type, with the journey volume recorded in millions as a decimal figure. The data was sourced from the Mayor of London's open data portal at [data.london.gov.uk](https://data.london.gov.uk/dataset) and loaded into a single Snowflake table: `TFL.JOURNEYS`.

## Business Questions

The project is structured around three analytical questions that span the full breadth of the dataset:

1. **Which transport types are most heavily used overall?** Aggregates all-time journey volumes by mode to rank the six transport types.
2. **How has the Emirates Airline cable car performed in its most popular months?** Isolates the cable car — whose ridership is an order of magnitude smaller than other modes — to surface its seasonal peak.
3. **Which years saw the lowest usage of the Underground & DLR?** Identifies the five lowest-volume years for the Tube, exposing both early-decade baselines and pandemic-era disruption.

## Data Model

The project uses a **single denormalised table**, `TFL.JOURNEYS`, rather than splitting transport types or dates into separate dimension tables. For an exploratory dataset of this size with a fixed, small set of transport types, denormalisation keeps every question answerable with a simple `SELECT … GROUP BY` — no joins required. The table grain is one row per `(MONTH, YEAR, JOURNEY_TYPE)` combination, with journey volume stored in the `JOURNEYS_MILLIONS` column as a float.

| Column | Type | Description |
|---|---|---|
| `MONTH` | `INTEGER` | Month in numeric format, e.g. `1` = January |
| `YEAR` | `INTEGER` | Calendar year |
| `DAYS` | `INTEGER` | Number of days in the given month |
| `REPORT_DATE` | `DATE` | Date the data was reported |
| `JOURNEY_TYPE` | `VARCHAR` | Method of transport |
| `JOURNEYS_MILLIONS` | `FLOAT` | Journey volume, in millions |

## Data Source

All journey volume figures are drawn from the Mayor of London's open data portal at [data.london.gov.uk](https://data.london.gov.uk/dataset). The extract used here has been cleaned and modified for use in this project.

> [!NOTE]
> This project uses a **modified extract** of TfL's published usage statistics. Treat all totals as indicative rather than authoritative. For citations or downstream reporting, refer back to the original source at [data.london.gov.uk](https://data.london.gov.uk/dataset).

## Explore the Project

- **[Quickstart](quickstart.md)** — Connect to the TFL Snowflake database and run all three analyses end to end in under 10 minutes.
- **[Table Schema](reference/table-schema.md)** — Full column definitions, valid JOURNEY\_TYPE values, and the query catalogue for TFL.JOURNEYS.
