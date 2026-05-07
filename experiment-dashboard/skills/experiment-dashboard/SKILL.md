---
name: experiment-dashboard
description: Create a self-hosted Python website to display simulation
  and experiment results from idmtools container platform runs,
  including charts, data tables, and file downloads.
---

# SKILL: COMPS-Style Data Dashboard

## Purpose
Generate data dashboards that match the visual style of the provided screenshots. This skill instructs the agent to extract colors, typography, spacing, and layout directly from reference screenshots rather than using hardcoded values.

---

## When to use this skill
Trigger on requests like:
- "Build a dashboard for my experiment results"
- "Show my simulation outputs in a table"
- "Create a viewer for my output files"
- "Make a chart from my InsetChart.json / output data"
- "Display my simulation run results"

---

## Output format decision
Choose the output format based on context:
- **React/JSX** — if the user mentions a React app, component, or frontend project
- **HTML + CSS + JS** (single file) — default for standalone dashboards or when unspecified
- **Python (Dash or Streamlit)** — if the user mentions Python, Jupyter, or a backend stack

When unsure, default to **HTML + CSS + JS** in a single self-contained file.

---

## Screenshot references

Each page of the dashboard has a designated reference screenshot. Before building any section, look at the corresponding screenshot and extract the visual style from it directly — colors, fonts, spacing, borders, layout proportions, and component shapes.

| Dashboard page | Reference screenshot | What to extract |
|---|---|---|
| Experiments list | `screenshots/experiments.png` | Top nav bar, filter bar, table columns, row styles, tag formatting, badge styles, pagination, date formatting |
| Experiment row selected + META panel | `screenshots/experiments_detail.png` | Selected row highlight, bottom detail panel layout, form field styles, tag pills, right pane summary card, donut chart |
| Simulations list + detail panel | `screenshots/simulations.png` | Status banner, simulation table, bottom split panel (file tree + code viewer), tab styles, selected row style |
| Chart / output viewer | `screenshots/insetchart.png` | Modal layout, chart line style, tooltip style, channel selector, legend/radio buttons |

> **Rule:** Do not invent or assume colors, fonts, or spacing. If a style is needed, look at the relevant screenshot and match it. If a screenshot is ambiguous, match the closest visible element.

---

## How to read each screenshot

When you receive a screenshot as a reference, extract the following before writing any code:

1. **Background colors** — page background, table header, selected rows, status bars
2. **Text colors and sizes** — links, labels, tags (key vs value), dates, badges
3. **Border styles** — table borders, panel dividers, pill/badge outlines
4. **Component shapes** — border radius of pills, badges, buttons, modals
5. **Layout proportions** — column widths, panel split ratio, padding/spacing rhythm
6. **Interactive states** — active vs inactive tabs, selected vs unselected rows, active filter pills

State your extracted values briefly before generating code, so the user can correct anything before you build.

---

## Required UI sections

Build these sections in order. For each, refer to the screenshot listed above.

### 1. Top navigation bar
Refer to: `screenshots/experiments.png`
- Match the bar background, icon styles, active vs inactive states, and right-side avatar/action buttons exactly as shown.

### 2. Filter bar
Refer to: `screenshots/experiments.png`
- Match the pill styles (active vs inactive), dropdown arrows, refresh/star icons, and pagination control layout.

### 3. Experiments table
Refer to: `screenshots/experiments.png`
- Match all column widths, header row style, cell padding, tag formatting (key vs value), count badges, action icon buttons, and date cell layout.

### 4. Status banner
Refer to: `screenshots/simulations.png`
- Match the banner background color, status label style, and count number size/weight.

### 5. Simulations table
Refer to: `screenshots/simulations.png`
- Match the table header, selected row highlight, related buttons (Parent/Siblings), activity badge, and succeeded status badge.

### 6. Bottom split panel
Refer to: `screenshots/simulations.png`
- Left pane (file tree): match tab bar, active tab style, file row layout, icons, file sizes, selected file highlight.
- Right pane (content viewer): renders different content depending on the selected file type — see **File rendering rules** below.

### 7. Chart modal
Refer to: `screenshots/insetchart.png`
- Match the modal header layout (ID, channel dropdown, checkbox, configure link, close button), chart line style, tooltip style, and channel radio button legend at the bottom.

---

## Data mapping instructions

When the user provides data (JSON, CSV, HDF5, Python dict, etc.), map it to the dashboard as follows:

| Data field | UI element |
|---|---|
| Experiment / run name | Clickable name in Meta column |
| Owner / username | Owner column |
| Tags / metadata dict | Tags column — render as `key: value` pairs |
| Simulation count | Sizes badge |
| Status (succeeded/failed/running) | Status banner + Activity cell badge |
| Created / modified timestamps | Date columns |
| Output files list | File tree in bottom panel |
| Time-series output (channels) | Line chart in modal |
| JSON file content | Syntax-highlighted code panel in right pane |
| CSV file content | Rendered data table in right pane |

If the user's data has different field names, infer the mapping from context and note any assumptions made.

---

## File rendering rules

When the user clicks a file in the left pane, the right pane renders based on file extension. Detect extension from the filename and switch accordingly.

### JSON files (`.json`)
- Render as a syntax-highlighted code panel
- Style (dark background, line numbers, key/string/number colors) extracted from `screenshots/simulations.png`
- Parse the JSON and pretty-print with 2-space indentation
- Highlight: object keys, string values, numeric values, boolean/null values each in a distinct color matching the screenshot
- Show a search bar above the panel (match style from `screenshots/simulations.png`)
- If the JSON contains a `Channels` object with array values, also offer a "View Chart" button to open the chart modal

### CSV files (`.csv`)
- Render as an interactive data table in the right pane
- Parse the first row as column headers; remaining rows as data
- Table style: match the experiments/simulations table style from `screenshots/simulations.png` (header row background, border, cell padding, font size)
- Features to include:
  - Column headers are sortable (click to sort ascending/descending, show a sort arrow)
  - Show row count above the table (e.g. "124 rows")
  - If the CSV has more than 100 rows, paginate or show a "Load more" button rather than rendering all rows at once
  - If a column contains only numeric values, right-align it
  - If a column contains only date/timestamp values, format them consistently
- Show a search/filter input above the table to filter rows by keyword

### Other file types
| Extension | Right pane behavior |
|---|---|
| `.txt`, `.log`, `.processed.txt` | Plain text, monospace font, line numbers, same dark panel style as JSON |
| `.png`, `.jpg`, `.gif` | Render the image inline, centered, with a subtle border |
| `.h5`, `.hdf5` | Show a message: "HDF5 file — use the chart icon to visualize channels" |
| Unknown | Show filename, size, and a download prompt |

### General right pane rules
- Always show the filename as a header at the top of the right pane (match style from `screenshots/simulations.png`)
- Show a loading indicator briefly while content is being "loaded" (simulate with a short JS timeout if needed)
- If the file content is not available, show a placeholder: "Select a file to preview its contents"

---

## Chart rendering rules

Use **Chart.js** (load from cdnjs) for all charts unless the output format is Python.

- Extract line color, fill opacity, grid line color, and tooltip style from `screenshots/insetchart.png`
- Always wrap canvas in a `position: relative` div with explicit height
- Always add `role="img"` and `aria-label` to canvas for accessibility
- Disable Chart.js default legend and build a custom HTML legend matching the screenshot
- Round all displayed numbers — no raw float artifacts

---

## Navigation behavior

Support at least two views with JS-driven navigation (no page reloads):

1. **Experiments view** — top-level list; style from `screenshots/experiments.png`
2. **Simulations view** — drill-down from a selected experiment; style from `screenshots/simulations.png`
3. **Chart modal** — opens over simulations view when a chart file or chart icon is clicked; style from `screenshots/insetchart.png`

---

## Experiment row selection and detail panel behavior

Refer to: `screenshots/experiments_detail.png`

The experiments page has two distinct states:

### Default state (no row selected)
- Show only the full-width experiments table
- No bottom panel visible
- Rows show a subtle hover highlight when the mouse moves over them (match hover color from `screenshots/experiments.png`)

### Selected state (user clicks a row)
- Highlight the selected row (match selected row style from `screenshots/experiments_detail.png`)
- Reveal a bottom detail panel below the table — split into left (form) and right (summary) panes
- The bottom panel shows **META tab only** — do not render a CONFIG tab or any other tabs
- The panel stays visible until the user clicks a different row or clicks away

### META tab content (left pane)
Extract field layout and style from `screenshots/experiments_detail.png`. Render these fields as labeled read-only inputs:
- **Id** — full experiment UUID, with a copy-to-clipboard icon button on the right
- **Name** — experiment name as plain text input
- **Description** — text input, may be empty
- **Tags** — render each tag as a small pill (e.g. `idmtools: ...`, `task_type: ...`), match pill style from `screenshots/experiments_detail.png`

Field label style: right-aligned plain text label, input is a light-bordered text box. Match spacing and font from the screenshot.

### META tab content (right pane)
Extract layout from `screenshots/experiments_detail.png`. Render:
- **Experiment size** — e.g. "Experiment: 49.068 Gb" as a labeled line
- **Children count button** — e.g. "Children: 100 Simulations" as a clickable badge/button with a chart icon; clicking navigates to the simulations view
- **Status summary** — green dot + "Succeeded (100)" label
- **Donut or pie chart** — showing simulation status breakdown (Succeeded / Failed / Running); extract chart colors from the screenshot

### Behavior rules
- Clicking a row selects it and opens the detail panel; clicking the same row again deselects and closes it
- Only one row can be selected at a time
- The experiment name in the detail panel META tab should match the clicked row
- "Children: N Simulations" button navigates to the simulations view filtered to that experiment

---

## Simulation row selection and detail panel behavior

Refer to: `screenshots/experiments_detail.png`

The simulation page has two distinct states:

### Default state (no row selected)
- Show only the full-width simulation table
- No bottom panel visible
- Rows show a subtle hover highlight when the mouse moves over them (match hover color from `screenshots/simulations.png`)

### Selected state (user clicks a row)
- Highlight the selected row (match selected row style from `screenshots/simulations_detail.png`)
- Reveal a bottom detail panel below the table — split into left (form) and right (summary) panes
- The bottom panel shows META, Config, Output tabs
- The panel stays visible until the user clicks a different row or clicks away

### META tab content (left pane)
Extract field layout and style from `screenshots/simulations_detail.png`. Render these fields as labeled read-only inputs:
- **Id** — full simulation UUID, with a copy-to-clipboard icon button on the right
- **Experiment Id** - Experiment UUID
- **Name** — simulation name as plain text input
- **Description** — text input, may be empty
- **Tags** — render each tag as a small pill (e.g. `idmtools: ...`, `task_type: ...`), match pill style from `screenshots/simulations_detail.png`

Field label style: right-aligned plain text label, input is a light-bordered text box. Match spacing and font from the screenshot.

### META tab content (right pane)
Extract layout from `screenshots/simulations_detail.png`. Render:
- **Simulation Status** — e.g. "Simulation: Succeeded"

### Config tab content (left pane)
Extract field layout and style from `screenshots/simulations_detail.png`. Render these fields as labeled read-only inputs:
- **Command** — read command from simulation's _run.sh file from line `exec -a ...`

### Config tab content (right pane)
- Leave it empty

### Output tab content (left pane)
Extract field layout and style from `screenshots/simulations.png`. Render these fields as labeled read-only inputs:
- Show File Tree, Assets and Output are folder and click it can show files under it.
- Assets/ folder is typically a symlink to the experiment's Assets directory

### Output tab content (right pane)
- Detail file content follow above file type roles


### Behavior rules
- Clicking a row selects it and opens the detail panel; clicking the same row again deselects and closes it
- Only one row can be selected at a time
- The simulation name in the detail panel META tab should match the clicked row

---

## Code quality rules

- Single-file output preferred (HTML with embedded CSS + JS, or single JSX file)
- No external dependencies except Chart.js from cdnjs
- No `localStorage` or `sessionStorage` — keep all state in JS variables or React `useState`
- Mobile: table should scroll horizontally on small screens (`overflow-x: auto` wrapper)
- Round every number shown to the user

---

## Checklist before delivering output

- [ ] Styles for each section extracted from the correct reference screenshot
- [ ] Extracted style values stated to the user before code is written
- [ ] Top nav matches `screenshots/experiments.png`
- [ ] Filter bar and experiments table match `screenshots/experiments.png`
- [ ] Default state for experiment: no bottom panel visible when no row is selected
- [ ] Selected state for experiment: bottom META panel appears on row click, matches `screenshots/experiments_detail.png`
- [ ] META panel for experiment shows Id, Name, Description, Tags fields (left) and size/status/donut chart (right)
- [ ] No CONFIG tab rendered for experiment view
- [ ] Status banner and simulations table match `screenshots/simulations.png`
- [ ] Bottom split panel matches `screenshots/simulations.png`
- [ ] JSON files render as syntax-highlighted code panel
- [ ] CSV files render as a sortable, filterable data table
- [ ] Right pane switches content based on file extension
- [ ] Chart modal matches `screenshots/insetchart.png`
- [ ] All numbers rounded
- [ ] Canvas has `role="img"` and `aria-label`
- [ ] Output is self-contained (no missing imports)