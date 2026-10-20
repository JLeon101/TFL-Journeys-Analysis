# Load the TfL CSV Source Extract with pandas read_csv

> Load tfl_journeys_final.csv into a pandas DataFrame with explicit column types, avoiding float inference for integer columns like MONTH, YEAR, and DAYS.

When working locally — for example, to prototype a query in a Jupyter notebook before running it against Snowflake, or to explore the data without a live warehouse connection — the raw source file `tfl_journeys_final.csv` is a direct substitute for `TFL.JOURNEYS`. However, pandas will infer column types from the first rows it reads, and integer-looking columns that contain any missing values (such as `month`, `year`, and `days`) can be silently promoted to `float64`. Specifying `dtype` explicitly prevents that inference and keeps column types predictable for downstream arithmetic and filtering.

## Loading the CSV

```python
import pandas as pd

df = pd.read_csv(
    "tfl_journeys_final.csv",
    dtype={"month": "int64", "year": "int64", "days": "int64", "journey_type": "string"},
    parse_dates=["report_date"],
)
```

## What each parameter does

- **`dtype={"month": "int64", "year": "int64", "days": "int64", ...}`** — Overrides pandas' default type inference for these three columns, ensuring they stay as integers rather than being promoted to `float64` if any `NaN` values are encountered during reading.
- **`dtype={..., "journey_type": "string"}`** — Loads the transport type column as pandas' nullable `StringDtype` rather than `object`, which gives cleaner string comparison behaviour and more explicit handling of missing values.
- **`parse_dates=["report_date"]`** — Converts the `report_date` column from its raw `MM/DD/YYYY` string representation into a proper `datetime64` dtype, enabling date arithmetic and time-based filtering without a separate conversion step.

## CSV structure

The file contains **935 data rows** plus a header row, with the following six columns:

| Column | Notes |
|--------|-------|
| `month` | Month as an integer (1–12) |
| `year` | Calendar year as an integer |
| `days` | Number of days in the given month |
| `report_date` | Reporting date, stored as `MM/DD/YYYY` in the file |
| `journey_type` | Transport mode name |
| `journeys_millions` | Journey volume in millions (decimal) |

> [!NOTE]
> CSV column names are **lower-case**. When the file is ingested into Snowflake, the object names are upper-cased automatically — so `month` in the CSV becomes `MONTH` in `TFL.JOURNEYS`, `journey_type` becomes `JOURNEY_TYPE`, and so on. Keep this in mind if you are comparing local DataFrame results against Snowflake query output.

For the full specification of the source file — including row count, date format, and how it maps to the Snowflake table schema — see the [CSV source reference](../reference/csv-source.md).
