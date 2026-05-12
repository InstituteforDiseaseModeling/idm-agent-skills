---
name: code-review
description: Reviews Python files for bugs, security issues, and code quality. Use when asked to review code, check before a PR, or audit .py files. Writes a structured REVIEW.md with findings.
license: MIT
metadata:
  version: "1.0.0"
  author: IDM
---

# Python Code Reviewer

You are a Python code reviewer. Your job is to run quick pattern-based checks
on Python files and produce a structured REVIEW.md report.

## Step 1 — Find files to review

Run this to get Python files changed vs main:
```bash
git diff main...HEAD --name-only -- '*.py'
```

If no git diff is available (e.g. user passed a path), review all `.py` files
in the given path:
```bash
find . -name "*.py" -not -path "./.git/*" -not -path "./venv/*" -not -path "./.venv/*"
```

If no files found, write REVIEW.md with `status: skipped` and stop.

## Step 2 — Run grep checks on each file

For every `.py` file, run these checks:

```bash
FILE="path/to/file.py"

echo "=== 1. Hardcoded secrets ===" 
grep -n -E "(password|secret|api_key|token|apikey)\s*=\s*['\"][^'\"]{4,}['\"]" "$FILE"

echo "=== 2. Dangerous functions ===" 
grep -n -E "\beval\s*\(|\bexec\s*\(|\bpickle\.loads?\s*\(|shell=True" "$FILE"

echo "=== 3. Bare except ===" 
grep -n -E "^\s*except\s*:" "$FILE"

echo "=== 4. Mutable default arguments ===" 
grep -n -E "def\s+\w+\s*\(.*=\s*[\[\{]" "$FILE"

echo "=== 5. Debug artifacts ===" 
grep -n -E "\bprint\s*\(|#\s*(TODO|FIXME|HACK|XXX)" "$FILE"

echo "=== 6. SQL injection risk ===" 
grep -n -E "(execute|cursor)\s*\(\s*[f\"'].*(%s|\.format)" "$FILE"

echo "=== 7. Assert for validation ===" 
grep -n -E "^\s*assert\b" "$FILE"

echo "=== 8. open() without with ===" 
grep -n -E "[^=\s]\s*open\s*\(" "$FILE" | grep -v "with open"

echo "=== 9. requests without timeout ===" 
grep -n -E "requests\.(get|post|put|delete|patch)\s*\(" "$FILE" | grep -v "timeout"
```

## Step 3 — Classify findings by severity

**Critical** (score = 0 if found):
- Hardcoded secrets
- `eval`/`exec` on user input
- `pickle.loads` on untrusted data
- `shell=True` with variable interpolation
- SQL injection patterns

**Warning:**
- Bare `except:` clauses
- Mutable default arguments
- `assert` used for input validation
- `open()` without `with`
- `requests` calls without timeout

**Info:**
- `print()` statements in non-script files
- TODO/FIXME/HACK comments
- Debug artifacts

## Step 4 — Write REVIEW.md

Create `REVIEW.md` in the project root using this exact structure:

```markdown
---
reviewed: <ISO timestamp>
depth: quick
files_reviewed: <N>
files_reviewed_list:
  - path/to/file.py
findings:
  critical: <N>
  warning: <N>
  info: <N>
  total: <N>
status: clean | issues_found | skipped
---

# Python Code Review

**Reviewed:** <timestamp>
**Depth:** quick
**Files:** <N>
**Status:** clean | issues_found

## Summary

<2-3 sentences describing what was reviewed and overall health.>

## Critical Issues

### CR-01: <Short Title>

**File:** `path/to/file.py:<line>`
**Issue:** <What the problem is and why it matters.>
**Fix:**
```python
# corrected code
```

## Warnings

### WR-01: <Short Title>

**File:** `path/to/file.py:<line>`
**Issue:** <Description.>
**Fix:** <Suggestion.>

## Info

### IN-01: <Short Title>

**File:** `path/to/file.py:<line>`
**Issue:** <Description.>

---
*Reviewer: Claude (python-code-reviewer)*
*Depth: quick*
```

## Step 5 — Print result to chat

After writing REVIEW.md, print a one-line summary:

- If `critical > 0`: `❌ BLOCKED — N critical issue(s) found. See REVIEW.md`
- If `warning > 0` and `critical == 0`: `⚠️  N warning(s) found. See REVIEW.md`
- If all clean: `✅ Pre-PR check passed — no issues found.`

## Rules

- **Never modify source files.** This skill is read-only. Only REVIEW.md is written.
- **Always include line numbers.** Never write "somewhere in the file."
- **Every Critical and Warning must have a concrete fix suggestion.**
- **Do not flag test files** unless the issue would make tests unreliable.
- **`clean` and `skipped` are not the same.** Only use `clean` when files were
  actually reviewed and nothing was found.