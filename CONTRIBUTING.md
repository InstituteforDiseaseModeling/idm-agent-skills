# Contributing to idm-agent-skills

## Adding a New Skill

Each skill lives inside its own plugin folder at the repo root. The structure looks like this:

```
<plugin-name>/
├── .claude-plugin/
│   └── plugin.json        ← required plugin manifest
├── skills/
│   └── <skill-name>/
│       └── SKILL.md       ← skill instructions
└── README.md              ← usage examples (recommended)
```

### Steps

1. Create a top-level folder with a short kebab-case name (e.g. `my-workflow/`)
2. Add `.claude-plugin/plugin.json` using the template below
3. Create `skills/<skill-name>/SKILL.md` using the template below
4. Add a `README.md` with usage examples
5. Open a pull request — at least one reviewer required before merging

## plugin.json Template

```json
{
  "name": "my-workflow",
  "description": "One sentence describing what this plugin does",
  "version": "1.0.0",
  "author": { "name": "Your Name" }
}
```

## SKILL.md Template

```markdown
---
name: my-skill-name
description: One sentence describing what this skill does and when the agent should use it
license: MIT
metadata:
  version: "1.0.0"
  author: IDM
---

## Overview
(What this skill does and why it exists)

## Prerequisites
(What must be true before running this skill)

## Steps
(The actual instructions)

## Verification
(How to confirm it worked)

## Troubleshooting
(Common problems and fixes)

## References
(Links to docs, repos, internal resources)
```

## Tips for Good Skills

- **Description matters most.** The agent uses it to decide whether to load the skill. Be specific and action-oriented.
- **Start with the boring stuff.** Repeated, error-prone, undocumented workflows make the best first skills.
- **Test locally first.** Pass the plugin folder to Claude Code directly before submitting.
- **Keep scope narrow.** One skill per workflow. Don't try to cover everything in one file.

## Local Testing

Test your plugin locally without installing it from the marketplace:

```bash
claude --plugin-dir ./my-new-plugin
```

Then invoke the skill by name in a Claude Code session to verify it loads and behaves correctly.

## Review Checklist

- [ ] `.claude-plugin/plugin.json` is present with `name`, `description`, and `version`
- [ ] YAML frontmatter in `SKILL.md` is valid (`name`, `description`, `license`, `metadata.version`)
- [ ] Description is specific enough for an agent to know when to use it
- [ ] Steps are complete and accurate (tested on a real machine)
- [ ] Troubleshooting section covers the most common failure modes
- [ ] References link to up-to-date documentation
