# Tracker Spreadsheet — Schema

The skill reads and writes a single `.xlsx` file with two sheets.

## File location

```
[YOUR_HUNT_DIR]/london_room_hunt.xlsx
```

Set `YOUR_HUNT_DIR` in your `config.md`. The skill creates this file on first run if it doesn't exist.

---

## Sheet 1: `Listings` (rooms in shared flats)

| Column | Type | Notes |
|---|---|---|
| Title | Text | Listing title from the platform |
| Platform | Text | SpareRoom / OpenRent / Rightmove / Zoopla |
| URL | Hyperlink | Full listing URL — used for deduplication |
| Area | Text | Neighbourhood name |
| Postcode | Text | e.g. SE11 |
| Price (pcm) | Number | Monthly rent in £ |
| Bills Included | Yes/No/Unknown | |
| Available From | Date | Move-in availability date |
| Furnished | Yes/No/Unknown | |
| Bed Count | Number | Total bedrooms in the property (not just the room) |
| Flatmates | Text | Notes on current flatmates (age, profession, etc.) |
| Contact | Text | Landlord/agent contact if listed |
| Notes | Text | Any extra details |
| Status | Text | See status values below |
| Priority | Text | High / Medium / Low |
| Found On | Date | Date this row was added |

### Status values

| Status | Meaning |
|---|---|
| `NEW 🔴` | Just found, not yet contacted |
| `To Contact` | Shortlisted, ready to message |
| `Contacted` | Outreach message sent |
| `Viewing Booked` | Viewing confirmed |
| `Viewed` | Viewing done |
| `Rejected` | Not suitable or taken |
| `Offer Made` | Applied / holding deposit discussed |
| `Secured ✅` | Signed and done |

### Row colours

| Priority | Fill colour | Hex |
|---|---|---|
| High | Green | `E2EFDA` |
| Medium | Yellow | `FFFFC7` |
| Low | Red/orange | `FCE4D6` |

---

## Sheet 2: `Studios & 1-Beds` (whole-unit lets)

Same columns as Listings, with these differences:

| Column | Notes |
|---|---|
| Bed Count | Studio / 1-Bed (no restriction, unlike rooms) |
| Flatmates | Not applicable — leave blank |

---

## Formatting applied by the skill

- Header row: dark blue fill (`1F3864`), white bold text
- Row 1 frozen (header stays visible when scrolling)
- Auto-filter enabled on all columns
- URLs written as clickable hyperlinks
- Column widths: Title=45, Platform=12, URL=50, Area=18, Postcode=10, Price=12, Bills=10, Available=14, Beds=10, Furnished=10, Notes=40, Contact=20, Status=10, Priority=10, Found On=12

---

## Deduplication

The skill loads all values from the `URL` column into a Python `set` at the start of each run. Any new listing whose URL already exists in the set is silently skipped. This makes every run idempotent — safe to run as many times as you want without creating duplicate rows.

---

## Manual setup (optional)

If you want to create the spreadsheet before the first run:

```bash
pip install openpyxl
python3 - <<'EOF'
import openpyxl
from openpyxl.styles import PatternFill, Font, Alignment
from openpyxl.utils import get_column_letter

wb = openpyxl.Workbook()

for sheet_name, cols in [
    ("Listings", ["Title","Platform","URL","Area","Postcode","Price (pcm)","Bills Included","Available From","Furnished","Bed Count","Flatmates","Contact","Notes","Status","Priority","Found On"]),
    ("Studios & 1-Beds", ["Title","Platform","URL","Area","Postcode","Price (pcm)","Bills Included","Available From","Bedrooms","Furnished","Notes","Contact","Status","Priority","Found On"]),
]:
    ws = wb.create_sheet(sheet_name)
    header_fill = PatternFill("solid", fgColor="1F3864")
    header_font = Font(color="FFFFFF", bold=True, name="Arial", size=11)
    for i, col in enumerate(cols, 1):
        cell = ws.cell(row=1, column=i, value=col)
        cell.fill = header_fill
        cell.font = header_font
    ws.freeze_panes = "A2"
    ws.auto_filter.ref = ws.dimensions

# Remove default sheet
if "Sheet" in wb.sheetnames:
    del wb["Sheet"]

import os, pathlib
path = pathlib.Path.home() / "my-room-hunt" / "london_room_hunt.xlsx"
path.parent.mkdir(parents=True, exist_ok=True)
wb.save(path)
print(f"Created {path}")
EOF
```
