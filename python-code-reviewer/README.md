# python-code-reviewer

A Claude Code plugin for reviewing Python code before creating a PR. Runs fast,
grep-based pattern checks across changed files and produces a structured
`REVIEW.md` report with severity-classified findings and concrete fix suggestions.

---

## Features

- **Quick pattern matching** — no full file reads needed; runs in seconds
- **9 check categories** — secrets, dangerous functions, bare excepts, mutable
  defaults, debug artifacts, SQL injection, assert misuse, resource leaks,
  missing timeouts
- **Severity classification** — Critical (blocks PR), Warning, Info
- **Structured output** — writes `REVIEW.md` with YAML frontmatter, finding IDs
  (CR-01, WR-01, IN-01), line numbers, and fix suggestions
- **Two invocation modes** — quick inline skill or dedicated review agent

---

## Installation

### Step 1 — Add the IDM marketplace (one time)

```bash
claude plugin marketplace add https://github.com/InstituteforDiseaseModeling/idm-agent-skills --scope user
```

### Step 2 — Install the plugin

Inside Claude Code:
```
/plugin install python-code-reviewer@idm-agent-skills
```

---

## Usage

### As a skill (recommended for pre-PR checks)

```
/python-code-reviewer:review
```

Runs grep checks on all `.py` files changed vs `main`, writes `REVIEW.md`,
and prints a one-line summary to chat.

### As an agent (for a dedicated review session)

```
/agents
# select python-code-reviewer from the list
```

Spins up a full review session with tool restrictions — the agent can only
read files and write `REVIEW.md`, never modify source.

---

## Output

The plugin writes `REVIEW.md` to your project root after every review:

```markdown
---
reviewed: 2026-05-04T10:30:00Z
depth: quick
files_reviewed: 3
files_reviewed_list:
  - src/simulation.py
  - src/calibration.py
  - tests/test_utils.py
findings:
  critical: 1
  warning: 2
  info: 1
  total: 4
status: issues_found
---

# Python Code Review

**Reviewed:** 2026-05-04T10:30:00Z
**Depth:** quick
**Files:** 3
**Status:** issues_found

## Summary

Reviewed 3 files changed vs main. One critical issue found — a hardcoded API
token in src/simulation.py that must be removed before merging. Two warnings
for bare except clauses and one informational TODO comment.

## Critical Issues

### CR-01: Hardcoded API Token

**File:** `src/simulation.py:14`
**Issue:** API token hardcoded as a string literal. Anyone with repo access
can read and misuse this credential.
**Fix:**
```python
# use environment variable instead
import os
api_token = os.environ["API_TOKEN"]
```

## Warnings

### WR-01: Bare Except Clause

**File:** `src/calibration.py:88`
**Issue:** Bare `except:` silently swallows all exceptions including
KeyboardInterrupt and SystemExit, making bugs hard to detect.
**Fix:** Catch specific exceptions: `except ValueError:` or `except Exception as e:`

...
```

---

## Check categories

| # | Check | Severity |
|---|-------|----------|
| 1 | Hardcoded secrets (password, token, api_key) | Critical |
| 2 | Dangerous functions (eval, exec, pickle.loads, shell=True) | Critical |
| 3 | SQL injection patterns | Critical |
| 4 | Bare `except:` clauses | Warning |
| 5 | Mutable default arguments | Warning |
| 6 | `assert` used for input validation | Warning |
| 7 | `open()` without `with` statement | Warning |
| 8 | `requests` calls without timeout | Warning |
| 9 | Debug artifacts (print, TODO, FIXME, HACK) | Info |

---

## Status values

| Status | Meaning |
|--------|---------|
| `issues_found` | One or more findings exist |
| `clean` | Files reviewed, nothing found |
| `skipped` | No `.py` files found to review |

`clean` and `skipped` are not the same — `clean` means files were reviewed
and passed.

---

## Pre-PR gate (recommended workflow)

Add a `.claude/commands/pre-pr.md` to your project:

```markdown
---
name: pre-pr
description: Review Python changes before creating a PR
---

1. Run /python-code-reviewer:review
2. If REVIEW.md shows critical > 0, print BLOCKED and stop
3. If clean, print "✅ Safe to open PR"
```

Then before every PR:
```
/pre-pr
```

---

## Plugin structure

```
python-code-reviewer/
  .claude-plugin/
    plugin.json          # plugin manifest
  skills/
    review/
      SKILL.md           # grep checks + REVIEW.md output logic
  agents/
    python-code-reviewer.md   # dedicated review agent
  README.md
```

---

## Notes

- **Read-only** — never modifies source files; only writes `REVIEW.md`
- **Test files** — test files are reviewed but flagged with lower priority
  unless the issue would make tests unreliable (e.g. bare except hiding
  assertion failures)
- **Scope** — performance issues are out of scope unless they are also
  correctness issues (infinite loops, not slow algorithms)
- **Project conventions** — if your project has a `CLAUDE.md` defining
  conventions (e.g. "we use assert for preconditions intentionally"), the
  reviewer respects those

---

## Part of IDM Agent Skills

This plugin is part of the
[idm-agent-skills](https://github.com/InstituteforDiseaseModeling/idm-agent-skills)
marketplace — a collection of Claude Code plugins for IDM engineering workflows.
