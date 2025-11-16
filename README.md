# Agent Skills Collection

A collection of Claude skills for enhanced AI-assisted development workflows.

## Installation

This is a Claude Code plugin. Install it via the Marketplace:

```bash
/plugin marketplace add https://github.com/ohyeh/agent_skill.git
/plugin install agent-skill@main
```

Or clone it directly:

```bash
git clone https://github.com/ohyeh/agent_skill.git
```

## Skills

### Codex Skill

A comprehensive skill for running Codex CLI commands with intelligent model selection and configuration management.

**Location**: `skills/codex/`

**Features**:
- Smart model selection (gpt-5.1-codex-mini, gpt-5.1-codex, gpt-5.1, gpt-5)
- Configurable reasoning effort levels
- Safety controls with `--yolo` and sandbox modes
- Session management (exec and resume)
- Comprehensive error handling

**Use Cases**:
- Code analysis and refactoring
- Large-scale code migrations
- Automated editing across multiple files
- Framework upgrades and restructuring

See [skills/codex/README.md](skills/codex/README.md) for detailed documentation.

## Usage

After installation, reference the skill name in your Claude Code requests:

```
"Use codex to refactor this module"
"Run codex exec for code analysis"
"Resume the last Codex session"
```

## Plugin Structure

```
agent_skill/
├── .claude-plugin/
│   └── plugin.json       # Plugin configuration
├── skills/
│   └── codex/
│       ├── SKILL.md      # Skill implementation guide
│       └── README.md     # Skill documentation
├── LICENSE
└── README.md             # This file
```

## Development

To add new skills to this plugin:

1. Create a new directory under `skills/`
2. Add a `SKILL.md` file with the skill definition
3. Update `.claude-plugin/plugin.json` to include the new skill path
4. Update this README with the skill description

## License

See [LICENSE](LICENSE) for details.