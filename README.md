I can draft a README grounded in the provided script. I’ll describe its purpose, controls, report workflow, supported activities, expected sheet structure, setup, and limitations.# CS Reports

An Excel Script that generates crop schedule reports from all worksheets whose names start with `^`.

## Overview

The script reads crop schedule data from Excel, filters rows based on a selected date range and activity, then inserts matching rows into the active report sheet.

## Report Configuration

Configure the report using cells on the active worksheet:

| Cell | Purpose |
|---|---|
| `A2` | Start date |
| `A5` | End date |
| `A8` | Report activity |

Supported activity values include:

- `SEED`
- `NURSERY TRANS`
- `Move Out Of Chamber Date`
- `FINISHING TRANS`
- `TRANSPLANT`
- `HARVEST`
- `CUSTOM`

The script compares the entered dates in Excel serial-number format. If the entered dates are reversed, it swaps them. If only one date is entered, it uses that date for both ends of the range. If neither date is entered, it uses `TODAY()`.

## Required Workbook Sheets

### Crop Schedule Worksheets

Crop schedule worksheets must have names beginning with `^`, such as `^Line 1` or `^Line 2`.

Each worksheet should:

- Use its first row as the header row
- Include `PS Planted`
- Include the relevant date columns
- Use names matching the report headers where possible

Common date columns include:

- `Seed Date`
- `Transplant Date`
- `Nursery Transplant`
- `Move Out Of Chamber Date`
- `Finishing Transplant`
- `Harvest Date`

The line number is extracted from each worksheet name and added to mapped report rows.

### `WORKBOOK_VALIDATIONS`

This worksheet provides the report column layout.

Columns in the report correspond to values listed under the selected activity in `WORKBOOK_VALIDATIONS`. The script writes these headers to row 8, starting in column B.

## Report Workflow

1. Read every worksheet whose name starts with `^`.
2. Retrieve the report-specific column layout from `WORKBOOK_VALIDATIONS`.
3. Read and validate the start and end dates.
4. Filter crop schedule rows for the selected date range.
5. Map source columns to report columns.
6. Group matching rows by activity.
7. Clear and refresh the previous report area.
8. Format and insert the generated rows.

For selected activities, the script searches the activity’s date column. If that column is unavailable, it falls back to `Harvest Date`.

## Output

Generated report data is inserted starting at:

- Row: `8`
- Column: `B`

The output table is:

- Centered
- Styled with the workbook green fill color
- Automatically fit to column width

If no matching data is found, the script inserts a notice in cell `B8` and leaves the rest of the report area clear.

## Misnamed or Missing Columns

Every date-related source column must have a recognizable report activity name.

If a date column is not recognized, the script writes an error to `C7`:

```text
The script is not able to fully read all tabs. A misnamed date column has been found in tab <tab name>. Please correct and run the report again.
```

When needed, check:

- Spelling
- Extra whitespace
- Incorrect headers
- Missing `Harvest Date` fallback data
- Column names that do not match `WORKBOOK_VALIDATIONS`

## Date Conversion

Excel serial numbers greater than `40000` are converted into a `M/D/YYYY` UTC date string.

Values that are not considered Excel dates are preserved as-is.

## Limitations

- Worksheets must follow the `^Line` naming convention.
- Header names are used to determine source-to-report mappings.
- Line numbers must be numeric and present in worksheet names.
- The script assumes the report header area begins in row 8.
- The script clears data in the report area before inserting new results.
- `PS Planted` filtering is currently present as a function but disabled in the main workflow.
- Empty date values and misnamed columns can cause missing or misaligned report data.
- The script may require the Excel date number format to be reset after execution.
