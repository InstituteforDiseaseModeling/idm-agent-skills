# Python Code Reviewer & Fixer

A pair of Claude Code skills for reviewing and fixing Python 3 code before pull requests. The reviewer finds issues; the fixer resolves them — each skill can also be used independently.

---

## Skills

| Skill | Purpose |
|-------|---------|
| `python-code-reviewer` | Scans Python files for bugs, security issues, and code quality problems. Produces `REVIEW.md`. |
| `python-code-fixer` | Reads `REVIEW.md` and applies fixes to source files. Produces `REVIEW-FIX.md`. |

---

## Quick Start

### 1. Review changed files
```
/python-code-reviewer:python-code-review
```
This scans all `.py` files changed vs `main`, writes `REVIEW.md` to the project root, and prints a one-line summary.

### 2. Fix the findings
```
/python-code-fixer:python-code-fix
```
This reads `REVIEW.md`, applies fixes to source files, commits each fix atomically, and writes `REVIEW-FIX.md`.

---

## What the Reviewer Checks

| Severity | Check |
|----------|-------|
| Critical | Hardcoded secrets (passwords, API keys, tokens) |
| Critical | Dangerous functions (`eval`, `exec`, `pickle.loads`, `shell=True`) |
| Critical | SQL injection patterns |
| Warning | Bare `except:` clauses |
| Warning | Mutable default arguments |
| Warning | `assert` used for input validation |
| Warning | `open()` without `with` |
| Warning | `requests` calls without timeout |
| Info | `print()` in non-script files |
| Info | TODO / FIXME / HACK comments |

Checks use pattern matching via the Claude Code `Grep` tool — no code execution required.

---

## Output Files

### `REVIEW.md`
Written by `python-code-reviewer` to the project root. Contains:
- YAML frontmatter with counts and status (`clean`, `issues_found`, or `skipped`)
- Findings grouped by severity (Critical → Warning → Info)
- Each finding includes file path, line number, issue description, and a concrete fix suggestion

### `REVIEW-FIX.md`
Written by `python-code-fixer` next to `REVIEW.md`. Contains:
- Summary of findings in scope, fixed, and skipped
- Per-finding result: commit hash (if fixed) or skip reason (if skipped)

---

## How the Fixer Works

The fixer reads each finding from `REVIEW.md` and:
1. Reads the actual source file at the cited line (never applies fixes blindly)
2. Adapts the fix suggestion to the current code state
3. Applies the fix using targeted edits
4. Verifies the fix with a Python syntax check (`python3 -m py_compile` or `python -m py_compile`)
5. Commits the fix atomically: `fix: CR-01 description`
6. Rolls back via `git checkout -- {file}` if verification fails

Findings that can't be cleanly applied are skipped with a documented reason — the fixer never forces broken fixes.

### Modes
- **Standalone Mode (default):** reads `./REVIEW.md`, writes `./REVIEW-FIX.md`, operates on the current working tree. No worktree setup needed.
- **Phase Mode:** invoked by an orchestrator with a `<config>` block. Creates an isolated git worktree, commits fixes there, then fast-forwards the main branch on cleanup.

---

## Scope & Exclusions

Both skills automatically exclude:
- `.git/`, `venv/`, `.venv/`, `env/`, `.env/`
- `node_modules/`, `__pycache__/`, `.tox/`, `.pytest_cache/`
- `build/`, `dist/`, `*.egg-info/`

Project-specific exclusions are honored from `.gitignore`, `.reviewignore`, or `tool.python-code-reviewer.exclude` in `pyproject.toml`.

The fixer skips any finding that targets a non-`.py` file.

---

## Requirements

- Python 3 (for syntax verification in the fixer)
- Git (for `git diff` scoping and atomic commits)
- Claude Code with these skills installed

---

## One-Line Status Legend

After a review run, the chat summary follows this priority:

| Output | Meaning |
|--------|---------|
| `❌ BLOCKED — N critical issue(s) found. See REVIEW.md` | PR should not merge |
| `⚠️ N warning(s) found. See REVIEW.md` | Issues to address before merge |
| `ℹ️ N info-level note(s). See REVIEW.md` | Low-priority observations |
| `✅ Pre-PR check passed — no issues found.` | Clean, ready to merge |
| `⏭️ Skipped — no Python files in scope.` | Nothing to review |

---

## Notes

- The reviewer is **read-only** — it never modifies source files, only writes `REVIEW.md`.
- The fixer only touches `.py` files explicitly cited in `REVIEW.md` findings.
- Finding IDs (`CR-NN`, `WR-NN`, `IN-NN`) and the `**File:**`/`**Issue:**`/`**Fix:**` fields in `REVIEW.md` must not be renamed — the fixer's parser depends on them.
- If `REVIEW.md` already exists, a new review run overwrites it entirely.