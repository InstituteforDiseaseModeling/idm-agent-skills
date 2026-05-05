# experiment-dashboard

A Claude Code plugin for creating and deploying a self-hosted Python website to
visualize simulation and experiment results from idmtools or similar container
platforms. Renders charts, data tables, and file download links directly from
container output files.

---

## Features

- **Self-hosted Python server** — runs locally via Flask or FastAPI, no cloud
  required
- **Auto-detects output formats** — reads CSV, JSON, and HDF5 files from your
  idmtools output directory
- **3 display modes** — charts & graphs, raw data tables, and file browser with
  download links
- **Zero database required** — reads directly from container output files
- **Configurable** — set output directory and port via environment variables or
  CLI flags

---

## Installation

### Step 1 — Add the IDM marketplace (one time)

```bash
claude plugin marketplace add https://github.com/InstituteforDiseaseModeling/idm-agent-skills --scope user
```

### Step 2 — Install the plugin

Inside Claude Code:
```
/plugin install experiment-dashboard@idm-agent-skills
```

---

## Skills vs agents

This plugin ships with both a **skill** and an **agent**. They do similar
things but work differently — pick whichever fits your situation:

| | Skill | Agent |
|---|---|---|
| **How it works** | Runs once, does the task, done | Conversational — asks questions, waits for your input |
| **Best for** | Repeatable runs where you know your config | First-time setup or when you want to choose options interactively |
| **Think of it as** | A function you call | A specialist you hire for a session |

---

## Usage

### As a skill (quick repeatable runs)

Use this when you already know your output directory and just want to build
and launch the dashboard fast:

```
/experiment-dashboard:results-dashboard
```

Scaffolds and launches a self-hosted dashboard pointed at your idmtools output
directory, with charts, tables, and file download links — no back-and-forth.

### As an agent (guided setup session)

Use this the first time, or when you want to configure options interactively.
The agent asks about your output directory, port, and display modes before
building anything:

```
/agents
# select experiment-dashboard from the list
```
You can type with prompt:  
`Build a dashboard for my emod experiment results`

The agent will:
1. Ask you 4 configuration questions (output directory, port, display modes, and folder name)
2. Confirm your choices before writing any files
3. Inspect your output files and report what it finds
4. Scaffold, install dependencies, and launch the dashboard

---

## Output

The plugin generates a self-hosted Python web app in your project:

```
dashboard/
  app.py              # Flask/FastAPI server entry point
  templates/
    index.html        # Main dashboard page
    charts.html       # Charts & graphs view
    tables.html       # Raw data table view
    files.html        # File browser & download view
  static/
    style.css
  requirements.txt
```

Run it with:

```bash
pip install -r dashboard/requirements.txt
python dashboard/app.py --output-dir ./outputs --port 8000
```

Then open `http://localhost:8000` in your browser.

---

## Display modes

| Mode | What it shows |
|------|---------------|
| Charts & graphs | Simulation output metrics rendered with Plotly |
| Data tables | Raw result data in sortable, filterable HTML tables |
| File browser | Browse and download output files from the container run |

---

## Supported output formats

| Format | How it's read |
|--------|--------------|
| `.csv` | Pandas — rendered as sortable tables and line/bar charts |
| `.json` | Auto-parsed — nested structures flattened into tables |
| `.hdf5` | h5py — datasets extracted and rendered as tables or charts |

If the output directory is empty or missing, the dashboard shows a clear error
state with the expected path.

---

## Configuration

| Variable / Flag | Default | Description |
|----------------|---------|-------------|
| `--output-dir` / `OUTPUT_DIR` | `./outputs` | Path to idmtools output directory |
| `--port` / `PORT` | `8000` | Port to serve the dashboard on |
| `--host` / `HOST` | `localhost` | Host to bind the server to |

---

## Pre-run workflow (recommended)

Add a `.claude/commands/launch-dashboard.md` to your project:

```markdown
---
name: launch-dashboard
description: Build and launch the experiment results dashboard
---

1. Run /experiment-dashboard:results-dashboard
2. Point the dashboard at the idmtools output directory
3. Launch the server and print the local URL to chat
```

Then after any container run:
```
/launch-dashboard
```

---

## Plugin structure

```
experiment-dashboard/
  .claude-plugin/
    plugin.json               # plugin manifest
  skills/
    results-dashboard/
      SKILL.md                # dashboard scaffolding + server logic
  agents/
    experiment-dashboard.md   # guided setup agent
  README.md
```

---

## Notes

- **Read-only** — never modifies your simulation output files
- **Local only** — serves on localhost by default; set `--host 0.0.0.0` to
  expose on your network
- **idmtools conventions** — recognizes standard idmtools output directory
  structures automatically
- **Project conventions** — if your project has a `CLAUDE.md` defining output
  paths or formats, the skill respects those

---

## Part of IDM Agent Skills

This plugin is part of the
[idm-agent-skills](https://github.com/InstituteforDiseaseModeling/idm-agent-skills)
marketplace — a collection of Claude Code plugins for IDM engineering workflows.