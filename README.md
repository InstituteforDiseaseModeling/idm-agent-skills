# idm-agent-skills

Agent Skills for the [Institute for Disease Modeling](https://www.idmod.org), compatible with Claude Code, Cursor, VS Code, GitHub Copilot, and other agents that support the SKILL.md format.

## Available Skills

| Plugin | Skill / Command | Description |
|---|---|---|
| [idm-pkg-install](./idm-pkg-install/) | `/idm-pkg-install:install` | Set up a virtual environment and install any IDM package from PyPI, GitHub, or a local path |
| [python-code-reviewer](./python-code-reviewer/) | `/python-code-reviewer:code-review` | Pre-PR Python code review: correctness, style, and IDM standards |
| [eng-quality](./eng-quality/) | `/eng-quality:eng-quality-checker` | Score scientific research code against IDM Software Engineering Quality Guidelines |
| [eng-quality](./eng-quality/) | `/eng-quality:eng-quality-fixer` | Apply improvements based on a prior `eng-quality-checker` report |


## Installation (Claude Code)

**Step 1: Register this repo as a marketplace (once)**
```bash
claude plugin marketplace add https://github.com/InstituteforDiseaseModeling/idm_standards
```

**Step 2: Install the plugins you need**
```bash
/plugin install python-code-reviewer@idm-agent-skills
/plugin install eng-quality@idm-agent-skills
/plugin install idm-pkg-install@idm-agent-skills
```

**Step 3: Reload plugins**
```bash
/reload-plugins
```

## Local Development & Testing

Clone the repo and test locally without installing:
```bash
git clone https://github.com/InstituteforDiseaseModeling/idm-agent-skills.git
claude --plugin-dir ./idm-agent-skills/python-code-reviewer
claude --plugin-dir ./idm-agent-skills/eng-quality
claude --plugin-dir ./idm-agent-skills/idm-pkg-install
```

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to add or improve skills.

Pull requests welcome. All skills are validated automatically via GitHub Actions on every PR.
