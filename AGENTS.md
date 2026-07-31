# AGENTS.md

## Cursor Cloud specific instructions

This repository is a **data-analysis project built entirely in Microsoft Excel**, not a
software application. There is no source code, build system, test suite, or dependency
manifest. The deliverable is `Excel Project Dataset.xlsx`, which analyzes bike-buyer data.

Workbook structure (`Excel Project Dataset.xlsx`):
- `bike_buyers` — raw dataset (~1000 records; columns: `ID`, `Marital Status`, `Gender`,
  `Income`, `Children`, `Education`, `Occupation`, `Home Owner`, `Cars`,
  `Commute Distance`, `Region`, `Age`, `Purchased Bike`).
- `Working Sheet` (hidden) — cleaned/derived columns used by the pivot tables.
- `Pivot Tables` (hidden) — pivot tables feeding the dashboard charts.
- `Dashboard` — final view with 7 charts and 3 slicers (Cars, Children, Home Owner).

### Environment (preinstalled in the Cursor Cloud VM snapshot)
The following are installed during environment setup and persist in the snapshot; they are
system tools, so they are intentionally **not** in the startup update script:
- LibreOffice Calc (`soffice`) — opens/recalculates/renders the workbook headlessly.
- `poppler-utils` (`pdftoppm`, `pdfinfo`) — turns rendered PDFs into images.
- A Python venv at `/home/ubuntu/.venvs/bike` with `pandas`, `openpyxl`, `pillow` for
  programmatic analysis. The startup update script recreates/refreshes this venv.

### How to "run" the workbook (render the dashboard)
There is no dev server. Rendering the workbook is how you exercise it:
```
soffice --headless --calc --convert-to pdf --outdir /tmp/render "Excel Project Dataset.xlsx"
pdftoppm -png -r 110 "/tmp/render/Excel Project Dataset.pdf" /tmp/render/page   # dashboard = last pages
```
Gotcha: LibreOffice does **not** render Excel slicer widgets — they show up as
"This shape represents a slicer" placeholder boxes. The charts and computed values render
correctly; only the interactive slicer controls are lost. This is a LibreOffice limitation,
not a problem with the workbook.

### How to analyze the data programmatically
```
/home/ubuntu/.venvs/bike/bin/python - <<'PY'
import pandas as pd
df = pd.read_excel("Excel Project Dataset.xlsx", sheet_name="bike_buyers")
print(df.groupby("Region")["Purchased Bike"].value_counts().unstack(fill_value=0))
PY
```

### Lint / test / build
Not applicable — there is no code to lint, test, or build. Validation means opening the
workbook (LibreOffice) and/or reading the dataset (pandas) as shown above.
