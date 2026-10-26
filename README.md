# Rebleeding aSAH Measurement Instrument Database — prototype

- `data/instruments.csv` contains the instrument records.
- `data/fields.csv` controls which fields appear in the main table, pop-up, filters, and search.
- `images/` contains the SAH HOPE logo and instrument graphics.
- URL fields can render as clickable links with link text configured entirely in `data/fields.csv`.

This version is configured for the **Rebleeding** core domain only.

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

Supported `format` values:

- `text` — standard text field
- `primary` — primary instrument-name field; automatically shows the acronym beside it
- `badge` — pill-style status display
- `longtext` — full-width text section in the pop-up
- `image` — image displayed near the top of the pop-up
- `url` — safe clickable `http://` or `https://` hyperlink; link text comes from `link_text`
- `hidden` — stored in the CSV but not directly rendered







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

- a small `ⓘ` appears inside the badge;
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

