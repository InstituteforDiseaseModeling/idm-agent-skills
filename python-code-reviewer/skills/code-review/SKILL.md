---
name: code-review
description: Reviews Python code for correctness, style, and IDM standards. Use when reviewing a PR, checking code before submitting, or analyzing Python code quality in IDM projects.
license: MIT
metadata:
  version: "1.0.0"
  author: IDM
---

## Overview

Perform a structured Python code review for IDM projects. Focus on what is actually present in the code — skip sections that are not relevant to the PR (e.g. if there is no simulation config, skip that section entirely).

## Review Structure

Always start with a one-paragraph summary of what the code does and the overall assessment before diving into details.

Use this severity system throughout:
- 🔴 **Blocking** — must fix before merge (bugs, security issues, broken tests)
- 🟡 **Suggestion** — recommended improvement (style, clarity, performance)
- 🔵 **Nitpick** — minor, optional (naming preferences, minor formatting)

---

## 1. Correctness

- Does the code do what it claims to do?
- Are there off-by-one errors, incorrect conditionals, or logic gaps?
- Are edge cases handled (empty inputs, None values, zero, negative numbers)?
- Are there any obvious bugs or unintended side effects?

## 2. Python Style & Formatting

- Follows PEP 8 (indentation, line length, whitespace)
- Uses type hints for function arguments and return values
- Docstrings present for all public functions, classes, and modules
- Naming is clear and follows snake_case for variables/functions, PascalCase for classes
- No magic numbers — use named constants instead
- No commented-out code left behind

## 3. Code Quality

- Each function does one thing
- No duplicated logic — shared code extracted to helpers
- Appropriate use of Python idioms (list comprehensions, context managers, etc.)
- No unnecessary complexity — simplest solution that works
- Imports organized: standard library → third party → local

## 4. Error Handling

- Exceptions caught at the right level
- No bare `except:` — always catch specific exception types
- Errors raise meaningful messages that help debugging
- Resources (files, connections) closed properly using `with` statements

## 5. Testing

Skip this section if no tests are included in the PR.

- New functionality has corresponding tests
- Tests cover happy path, edge cases, and failure cases
- Assertions are meaningful — not just `assert result is not None`
- Tests are independent and don't rely on each other's state
- Uses pytest conventions (fixtures, parametrize where appropriate)

## 6. IDM-Specific Checks

Apply only the subsections relevant to what the PR actually touches.

### idmtools usage
- Tasks and experiments constructed using idmtools builders, not raw dicts
- Platform configuration loaded from `idmtools.ini`, not hardcoded
- Asset collections used correctly for input files
- No hardcoded COMPS paths or environment names

### emodpy / emodpy-malaria usage
- Campaign and demographics builders used instead of raw JSON construction
- Simulation configuration set through proper emodpy config functions
- Input files referenced via asset collection, not absolute local paths
- No hardcoded simulation parameters that should be arguments

### General IDM conventions
- No hardcoded local paths (use `pathlib.Path` and relative paths)
- No credentials or tokens in code or comments
- Large data files not committed to the repo — referenced externally
- Dependencies added to `requirements.txt` or `setup.py`, not just imported

## 7. Documentation

- README updated if behavior or setup steps changed
- Any new environment variables or config options documented
- Complex logic has inline comments explaining *why*, not just *what*

## Output Format

Group findings by severity, then by section. Example:

---
### Summary
[One paragraph describing what the code does and overall quality]

### 🔴 Blocking
- `filename.py:42` — [issue and why it matters]

### 🟡 Suggestions
- `filename.py:15` — [what to improve and why]

### 🔵 Nitpicks
- `filename.py:8` — [minor preference]

### ✅ Looks good
- [note anything done particularly well — not just problems]
---

If a section has no findings, omit it from the output. Always end with the ✅ section to acknowledge what was done well.
