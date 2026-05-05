# idm-agent-skills

Agent Skills for the [Institute for Disease Modeling](https://www.idmod.org), compatible with Claude Code, Cursor, VS Code, GitHub Copilot, and other agents that support the SKILL.md format.

## Available Skills

| Plugin | Skill / Command | Description |
|---|---|---|
| [idm-pkg-install](./idm-pkg-install/) | `/idm-pkg-install:install` | Set up a virtual environment and install any IDM package from PyPI, GitHub, or a local path |
| [python-code-reviewer](./python-code-reviewer/) | `/python-code-reviewer:code-review` | Pre-PR Python code review: correctness, style, and IDM standards |
| [experiment-dashboard](./experiment-dashboard/) | `/experiment-dashboard` | Use this skill when the user wants to create, generate, or deploy a self-hosted website to display or visualize simulation or experiment results |



## Installation (Claude Code)

**Step 1: Add the marketplace (once per person)**
```bash
claude plugin marketplace add https://github.com/InstituteforDiseaseModeling/idm-agent-skills --scope user
```

**Step 2: Install any plugin from it**
```bash
/plugin install idm-pkg-install@idm-agent-skills
/plugin install experiment-dashboard@idm-agent-skills
/plugin install python-code-reviewer@idm-agent-skills
```

**Step 3: Reload plugins**
```bash
/reload-plugins
```

**Step 4 — Invoke the skill**
```bash
/experiment-dashboard:results-dashboard
/python-code-reviewer:code-review
/idm-pkg-install:install [github_repo|local_repo] [prod|dev]
/idm-pkg-install:install pypi emodpy
````

## Local Development & Testing

Clone the repo and test locally without installing:
```bash
git clone https://github.com/InstituteforDiseaseModeling/idm-agent-skills.git
claude --plugin-dir ./idm-agent-skills/python-code-reviewer
claude --plugin-dir ./idm-agent-skills/experiment-dashboard
claude --plugin-dir ./idm-agent-skills/idm-pkg-install
```

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to add or improve skills.

Pull requests welcome. All skills are validated automatically via GitHub Actions on every PR.
