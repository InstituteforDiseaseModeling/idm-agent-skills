# Contributing to IDM Agent Skills

This repository is a collection of Claude Code plugins for IDM engineering
workflows. Anyone at IDM can contribute a new skill or agent — this guide
walks you through the process.

---

## What is a skill vs an agent?

| | Skill | Agent |
|---|---|---|
| **What it is** | A set of instructions Claude follows to complete a task | A full conversational session with its own persona and tool restrictions |
| **Best for** | Repeatable, single-step tasks | Multi-step workflows that need user input or configuration |
| **Invoked via** | `/plugin-name:skill-name` | `/agents` → select from list |
| **File type** | `skills/<name>/SKILL.md` | `agents/<name>.md` |

Start with a skill. Add an agent only if your workflow needs back-and-forth
configuration with the user.

---

## Repository structure

```
idm-agent-skills/
  <plugin-name>/
    .claude-plugin/
      plugin.json         # plugin manifest
    skills/
      <skill-name>/
        SKILL.md          # skill instructions
    agents/
      <agent-name>.md     # agent definition (optional)
    README.md             # plugin documentation
```

Each plugin lives in its own top-level folder. One plugin can contain
multiple skills and agents.

---

## Step-by-step: adding a new skill

### Step 1 — Create your plugin folder

```bash
mkdir -p my-plugin/.claude-plugin
mkdir -p my-plugin/skills/my-skill
```

### Step 2 — Write the plugin manifest

Create `my-plugin/.claude-plugin/plugin.json`:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "One or two sentences describing when Claude should use this plugin. Be specific about trigger phrases and what it does. Add a Do NOT use for... line to prevent false triggers.",
  "author": {
    "name": "Your Name"
  },
  "keywords": [
    "keyword1",
    "keyword2"
  ]
}
```

**Tips for a good description:**
- Say when to trigger it ("Use when the user mentions X or asks to Y")
- Say what it does ("Generates a Z report with...")
- Say when NOT to use it ("Do NOT use for general web development...")

### Step 3 — Write the skill

Create `my-plugin/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: One-line summary of what this skill does.
---

<Instructions for Claude go here. Be specific and step-by-step.>

## What to do
1. First, do X
2. Then, do Y
3. Finally, output Z

## Rules
- Never modify source files
- Always confirm before destructive actions
```

### Step 4 — Write the README

Copy the structure from an existing plugin README (e.g. `experiment-dashboard/README.md`)
and update it for your plugin. Every README should include:

- What the plugin does
- Installation instructions
- Usage (skill and/or agent invocation)
- Output description
- Plugin structure diagram

### Step 5 — Test locally

```bash
# Add the local repo as a marketplace source
claude plugin marketplace add /path/to/idm-agent-skills --scope user

# Install your plugin
/plugin install my-plugin@idm-agent-skills

# Invoke your skill
/my-plugin:my-skill
```

### Step 6 — Open a pull request

- Branch name: `add/<plugin-name>`
- PR title: `Add <plugin-name> plugin — <one line description>`
- Include a short demo or screenshot in the PR description if possible

---

## Naming conventions

| Thing | Convention | Example |
|---|---|---|
| Plugin name | kebab-case | `experiment-dashboard` |
| Skill folder | kebab-case | `results-dashboard` |
| Agent file | kebab-case | `experiment-dashboard.md` |
| Plugin version | semver | `1.0.0` |

---

## Writing good skill instructions

The quality of your `SKILL.md` determines how well Claude performs the task.
Follow these principles:

- **Be explicit** — don't assume Claude knows your domain; spell out every step
- **Define outputs** — describe exactly what files or messages the skill should produce
- **Add rules** — list what Claude should never do (e.g. never modify source files)
- **Handle edge cases** — what should happen if a file is missing? A directory is empty?
- **Use trigger phrases** — list example things a user might say to invoke the skill

---

## Adding an agent

Add an agent only if your workflow needs guided configuration. Create
`my-plugin/agents/my-agent.md`:

```markdown
---
name: my-agent
description: One-line summary of what this agent does.
allowed-tools:
  - Read
  - Write
  - Bash(python:*)
---

You are a <role> specialist. Your job is to <goal>.

## Phase 1 — Configuration
Ask the user these questions one at a time...

## Phase 2 — Execution
After confirmation, do the following...

## Rules
- Always confirm before writing files
- Never modify source files
```

**Allowed tools** — only grant what the agent actually needs. Common options:
`Read`, `Write`, `Edit`, `Bash(python:*)`, `Bash(pip:*)`, `Bash(git:*)`.

---

## Questions?

Open an issue or ping the IDM engineering Slack channel.