---
name: results-dashboard
description: Create a self-hosted Python website to display simulation
  and experiment results from idmtools container platform runs,
  including charts, data tables, and file downloads.
---

Build a self-hosted Python web dashboard to visualize and browse
simulation or experiment results produced by idmtools or similar
container platforms.

## Stack
- Python (Flask or FastAPI for serving)
- Jinja2 or plain HTML templates
- Plotly or Matplotlib for charts and graphs
- Pandas for table rendering
- Static file serving for download links

## What to build
1. **Charts & graphs** — render simulation output metrics visually
2. **Data tables** — display raw result data in sortable HTML tables
3. **File browser / download links** — let users browse and download
   output files from the container run

## Key behaviors
- Read result files from a configurable output directory
- Auto-detect common idmtools output formats (CSV, JSON, HDF5)
- Serve locally on a configurable port (default: 8000)
- If output directory is empty or missing, show a clear error state
- Do NOT require a database — read directly from output files

## Trigger phrases
- "show my simulation results"
- "host my experiment output"
- "build a dashboard for my idmtools run"
- "visualize results from the container"