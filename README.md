# Rebleeding aSAH Measurement Instrument Database — prototype

- `data/instruments.csv` contains the instrument records.
- `data/fields.csv` controls which fields appear in the main table, pop-up, filters, and search.
- `images/` contains the SAH HOPE logo and instrument graphics.
- URL fields can render as clickable links with link text configured entirely in `data/fields.csv`.

This version is configured for the **Rebleeding** core domain only and includes three linked views: **Overview**, **Domain Match**, and **Feasibility**. All three views share the same search, filters, and instrument records.

## Folder structure

```text
index.html
.nojekyll
README.md

data/
  instruments.csv
  fields.csv
  badge_styles.csv

images/
  team-logo.svg
  example-instrument-graphic.svg
```


| Column | Purpose |
|---|---|
| `field` | Exact column header from `instruments.csv` |
| `label` | Human-readable label shown on the website |
| `table` | `Yes` to show as a main table column |
| `popup` | `Yes` to show in the instrument pop-up |
| `filter` | `Yes` to automatically create a dropdown filter |
| `search` | `Yes` to include this field in free-text search |
| `display_order` | Number controlling left-to-right / top-to-bottom order |
| `format` | How the value is displayed |
| `link_text` | Text displayed for fields using the `url` format; leave blank for other formats |
| `view` (optional) | Assign a field to the `domain-match` or `feasibility` comparison view; use `overview` or `none` to keep it out of the assessment views |

Supported `format` values:

- `text` — standard text field
- `primary` — primary instrument-name field; automatically shows the acronym beside it
- `badge` — pill-style status display
- `longtext` — full-width text section in the pop-up
- `image` — image displayed near the top of the pop-up
- `url` — safe clickable `http://` or `https://` hyperlink; link text comes from `link_text`
- `hidden` — stored in the CSV but not directly rendered







## Domain Match and Feasibility view tabs

The three tabs share the same search and filtering controls:

- **Overview:** The original overview table; its columns remain controlled by the `table` column in `data/fields.csv`.
- **Domain Match:** A side-by-side comparison of the selected instruments' domain match assessment columns, including colour-coded ratings and clickable/hoverable badge rationales where present.
- **Feasibility:** A horizontally scrolling side-by-side comparison, including categorical feasibility ratings **and open-text fields**. The leftmost instrument-name column remains visible while scrolling.

An instrument can still be opened by selecting a row or its instrument-name link from any of the three views. The record pop-up, search, filters, viewed markers, record navigation, and print functionality remain available.

### Linking directly to a view

Use the `view` query parameter in the published GitHub Pages URL:

```text
?view=domain-match
?view=feasibility
```

The Overview is the default. A direct instrument link can also include a view parameter, e.g. `?view=feasibility&instrument=REB001` (replace `REB001` with a real instrument ID from your CSV). The selected tab is retained in the browser URL when a record is opened or closed.

### Populating the tabs from your CSV files

**No changes to the existing data are required to display the tabs.** They automatically discover common assessment column names in `data/fields.csv`, provided those fields also exist in `data/instruments.csv`.

For reliable control, add an optional `view` column to `data/fields.csv`. Use `domain-match` or `feasibility` for the assessment columns, `overview` or `none` for fields that should not appear in a comparison, and leave it blank for automatic detection. `assessment_view` and `tab` are also accepted as column names if preferred.

Illustrative configuration (not a replacement for your existing `fields.csv`):

| `field` | `label` | `format` | `view` |
|---|---|---|---|
| `domain_match` | Overall Domain Match | `badge` | `domain-match` |
| `domain_match_score` | Domain Match Score | `text` | `domain-match` |
| `feasibility_ease_of_use` | Ease of use | `badge` | `feasibility` |
| `feasibility_time_required` | Time required | `badge` | `feasibility` |
| `feasibility_time_notes` | Time required — details | `longtext` | `feasibility` |
| `feasibility_cost` | Cost | `badge` | `feasibility` |
| `feasibility_patient_burden` | Patient burden or risk | `badge` | `feasibility` |

Every named `field` must also be a column in `data/instruments.csv`. Columns in this example are **illustrative**; the updated HTML will not invent values or add columns to your existing data. It will also recognise assessment field names containing `domain_match`, `feasibility`/`feas_`, and several common feasibility dimensions (e.g. `equipment`, `training`, `cost`, `patient_burden`) without requiring `view` entries.

Rating rationales continue to use the existing `<assessment_field>_rationale` naming convention in `data/instruments.csv`. For example, `feasibility_ease_of_use_rationale` provides the rationale when someone hovers, focuses, or selects its rating. These rationale columns do not appear as duplicate comparison columns by default. To display a distinct text field directly in the Feasibility table, assign its `view` to `feasibility` and choose `longtext` or `text` as its format. There is **no character limit** on displayed open text.

If no relevant assessment fields are present in the supplied CSVs, the tab shows a configuration message rather than creating fictional assessment results. Any existing colour rules in `data/badge_styles.csv` take priority; otherwise the comparison view uses the standard fallback colours described below.

### Comparison rating colours

Domain Match recognises **Good** (green), **Partially — somewhat good** (yellow), **Partially — somewhat poor** (orange), **Poor** (red), and **Unable to say / Unable to judge** (white). Feasibility recognises **0 Green**, **1 Amber**, **2 Red**, and **U White**, with unassessed responses displayed in grey. The visible legends explain the meaning of the ratings. Your `data/badge_styles.csv` can override these fallbacks for exact values.

### Printing and PDF export

**Print current view** prints the active Overview, Domain Match or Feasibility table, including the current search and filter results, with a matching title. The browser's **Save as PDF** print destination can be used for export. When an instrument record is open, use its **Print** button (or the browser's print command) to print that full record in portrait orientation. The tables print in landscape orientation. Large or detailed feasibility tables may span multiple pages.

When deploying, replace `index.html` with the updated HTML and retain your existing `data/` and `images/` folders. You do not need to replace or recreate the instrument data files unless you want to add or reclassify assessment fields.

## Instrument pop-up navigation

The instrument pop-up supports faster browsing through the instruments currently shown by the active search and filters:

- **Sticky footer navigation** keeps `Previous`, the record position (for example, `3 of 14`), and `Next` visible while scrolling through long records.
- **Desktop side arrows** provide an additional visual way to move between records on wider screens. They are hidden on smaller screens to avoid covering content.
- **Keyboard navigation**: while the pop-up is open, use the left and right arrow keys to move to the previous or next instrument.
- Navigation follows the **currently filtered list**, rather than always moving through every instrument in the database.
- Previous/Next controls are automatically disabled at the first and last matching records.

## Enhanced record navigation and deep links

The instrument pop-up includes several navigation aids:

- **Sticky instrument header** keeps the instrument title, `Back to results`, `Copy link`, and close controls visible while scrolling.
- **Back to results** closes the record, restores the prior results-page position, and highlights the originating row to help users re-orient.
- **Viewed markers** are stored only in the visitor's browser. Opened instruments display `✓ Viewed`; `Clear viewed markers` resets this local history.
- **Record jump menu** lists all instruments in the currently filtered/search result set. It uses the full instrument name and adds the acronym in parentheses only when an acronym exists.
- **Keyboard hints** in the footer advertise the available shortcuts: left/right arrows browse records and `Esc` closes the dialog.
- **Direct instrument links** use `?instrument=INSTRUMENT_ID`. Visiting such a URL automatically opens that instrument record. Instrument names in the results table are also real links, so users can copy them or open them in a new tab.
- **Copy link** copies the direct URL for the currently open record.

Viewed status uses browser `localStorage`; it does not require an account or server-side viewing history.


## Full-screen instrument images

Instrument graphics in the pop-up can be opened in a full-screen viewer:

- **Click the image** to enlarge it.
- With the image focused, press **Enter** or **Space** to enlarge it.
- Select the **×** button, click the dark background, or press **Esc** to close the full-screen image.
- Focus returns to the instrument image after the full-screen viewer closes.
- A short `Click image to enlarge` hint is displayed below instrument graphics.

The full-screen viewer uses the existing `image` and `image_alt` values from `data/instruments.csv`; no additional CSV fields are required.


## Configurable badge colours

Badge colours are controlled by `data/badge_styles.csv`, so colours can be changed without editing `index.html`.

The file uses four columns:

| Column | Purpose |
|---|---|
| `field` | Exact field name from `instruments.csv`, or `*` for a global rule |
| `value` | Exact category/response value to match |
| `background` | Badge background colour |
| `text` | Badge text colour |

Example:

```csv
field,value,background,text
*,Yes,#e8f5ee,#23613b
*,Partially / with concerns,#fff6dd,#7a5a00
*,No,#fdecec,#9b2c2c
*,Unable to judge,#eef2f6,#475467
status,Retained,#e8f5ee,#23613b
status,Identified,#edf4ff,#285ea8
```

Rules are matched case-insensitively.

- A **field-specific** rule takes priority over a wildcard (`*`) rule.
- A wildcard rule lets the same category use the same colour across multiple assessment columns.
- If no matching rule is found, the badge uses the default neutral grey style.
- A field must still use `format=badge` in `data/fields.csv` for these styles to be applied.

This makes it possible to use consistent traffic-light-style colours for responses such as `Yes`, `Partially / with concerns`, `No`, and `Unable to judge`, while giving fields such as `status` their own colour scheme.


## Badge rationale pop-ups

Any field displayed with `format = badge` can have an optional rationale. The rationale is stored in `data/instruments.csv` using the naming convention:

```text
<badge_field>_rationale
```

For a Domain Match field named `domain_match`, add a column named:

```text
domain_match_rationale
```

Example:

```csv
id,name,domain_match,domain_match_rationale
REB001,Example Instrument,Yes,"Directly assesses the intended Rebleeding domain."
REB002,Another Instrument,Partially / with concerns,"Captures some evidence of recurrent bleeding but has limitations for consistently identifying the intended Rebleeding event."
```

If `domain_match_rationale` is also listed in `data/fields.csv`, configure it as a hidden field:

```text
field = domain_match_rationale
format = hidden
table = No
popup = No
filter = No
search = No
```

When a badge has a non-empty rationale:

- the coloured badge itself is interactive (no separate information icon);
- **hovering** over the badge shows the rationale;
- **keyboard focus** shows the rationale;
- **clicking or tapping** pins the rationale open for touch-screen users;
- clicking elsewhere closes it;
- `Esc` closes the rationale first while an instrument record is open.

The feature is generic. For example, a badge field named `feasibility` can use `feasibility_rationale`.

Badge colours still come from `data/badge_styles.csv`, so the rationale feature does not change the configurable colour system.


### Rationale pop-ups inside the instrument window

The rationale viewer now uses the browser's native **Popover API** (`popover="manual"`). The instrument record itself is a modal `<dialog>`, and ordinary page elements can be hidden behind that dialog even with a high `z-index`.

Native popovers are also placed in the browser's **top layer**, so the rationale can appear above an open instrument record as intended. A fallback is included for browsers without Popover API support.

## Prototype disclaimer

The included instrument records have been identified as potential candidate instruments for measuring the aSAH Core Domain: Rebleeding. They should not be treated as the definitive aSAH instrument inventory or as COS endorsements.

