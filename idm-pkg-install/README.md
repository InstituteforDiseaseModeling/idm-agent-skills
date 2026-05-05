# idm-pkg-install

A Claude Code plugin that installs any IDM repository given a local path or GitHub URL.

## Usage

```
/idm-pkg-install:install                                                     # install current directory
/idm-pkg-install:install /path/to/repo                                       # local path
/idm-pkg-install:install https://github.com/InstituteforDiseaseModeling/emodpy  # GitHub URL
```

## Structure

```
idm-pkg-install/
├── .claude-plugin/
│   └── plugin.json                         ← plugin manifest (name, version, component paths)
├── skills/
│   └── idm-pkg-install/
│       └── SKILL.md                        ← full install logic; also triggers autonomously
├── commands/
│   └── install.md                          ← /install slash command; passes $ARGUMENTS to skill
└── README.md
```

## Install via marketplace

```
/plugin install idm-pkg-install@idm-agent-skills
/idm-pkg-install:idm-pkg-install install https://github.com/EMOD-Hub/emodpy-malaria.git prod
/idm-pkg-install:idm-pkg-install install https://github.com/EMOD-Hub/emodpy-malaria.git dev
/idm-pkg-install:idm-pkg-install install . prod 
/idm-pkg-install:idm-pkg-install install emodpy-malaria
```
