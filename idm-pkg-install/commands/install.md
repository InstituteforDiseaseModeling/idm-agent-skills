---
description: |
  Install an IDM package. Accepts optional arguments to skip prompts.
  First argument: install source — 'pypi', a GitHub URL, or a local path.
  Second argument (optional, for repo/local only): install mode — 'dev' (editable), 'prod' (standard), or 'requirements' (install from requirements.txt only).
  Omit any argument to be prompted.
argument-hint: "[pypi|github-url|local-path] [dev|prod|requirements]"
allowed-tools: Bash, Read, Edit, Glob, Grep
---

Arguments provided: $ARGUMENTS

**Step 1 — Determine the install source.**

Parse the first argument:
- `pypi` → install a released package from PyPI (user will be asked for the package name and optional version)
- A GitHub URL (starts with `https://github.com`) → clone and install from that repo
- A local path (anything else, or nothing) → install from that directory; default to current working directory if omitted

If no argument was given, ask the user:
> "Where should I install from?
> 1. **PyPI** — install a released version by package name (e.g. `emodpy==2.1.0`)
> 2. **GitHub repo** — clone and install from a GitHub URL
> 3. **Local path** — install from a directory on this machine (default: current directory)"

Wait for the user's answer before continuing.

**Step 2 — Create an isolated environment. This step is MANDATORY. Never skip it.**

Ask the user which environment tool to use, unless they already specified one:
> "Should I create a **venv** (lightweight, no conda needed) or a **conda** environment? Or do you have an existing environment you'd like me to activate instead?"

Do not proceed to installation until an isolated environment is created and activated. Installing into the system Python or an unknown environment is not acceptable.

**Step 3 — Determine the install mode** (skip for PyPI source — PyPI always installs a fixed release).

For GitHub or local sources, parse the second argument:
- `dev` → editable install (`pip install -e .`)
- `prod` → standard install (`pip install .`)
- `requirements` → install dependencies only from `requirements.txt` (no package install)

If the mode was passed as an argument, use it directly. Otherwise **defer this question until after Step 4 inspects the repo** — then:
- If only `requirements.txt` is present (no `pyproject.toml`, no `setup.py`/`setup.cfg`) → auto-select `requirements` mode, no prompt.
- If an installable package is present → ask:
  > "Should I do a **dev install** (editable, `pip install -e .` — source changes take effect immediately, best for active development) or a **prod install** (standard, `pip install .` — builds from local source into site-packages, source edits don't take effect until you reinstall)?"
- If both are present and unclear → offer all three options (`dev`, `prod`, `requirements`).

Wait for the user's answer before continuing when a prompt is required.

**Step 4 — Follow the idm-pkg-install skill for the chosen source:**

- **PyPI source** → follow the "Install from PyPI" section
- **GitHub or local source** → follow the main workflow, applying the chosen install mode at the install step
