# Agent Skills Collection

A collection of Claude skills for enhanced AI-assisted development workflows.

## Installation

This repository serves as both a Claude Code plugin and a marketplace. You have multiple installation options:

### Option 1: Via Marketplace (Recommended)

```bash
# Add this repository as a marketplace
/plugin marketplace add https://github.com/ohyeh/agent_skill.git

# Install the agent-skill plugin
/plugin install agent-skill@main
```

### Option 2: Direct Plugin Installation

```bash
# Install directly without using marketplace
/plugin install agent-skill@main --source https://github.com/ohyeh/agent_skill.git
```

### Option 3: Local Development

```bash
# Clone the repository
git clone https://github.com/ohyeh/agent_skill.git

# Link it locally in Claude Code
/plugin link /path/to/agent_skill
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
│   ├── plugin.json       # Plugin configuration
│   └── marketplace.json  # Marketplace configuration (dual-purpose repo)
├── skills/
│   └── codex/
│       ├── SKILL.md      # Skill implementation guide
│       └── README.md     # Skill documentation
├── LICENSE
└── README.md             # This file
```

This repository is configured as a **dual-purpose repository**, containing both:
- **Plugin configuration** (`plugin.json`) - allows direct installation as a plugin
- **Marketplace configuration** (`marketplace.json`) - allows use as a marketplace that lists this plugin

## Development

### Adding New Skills

To add new skills to this plugin:

1. Create a new directory under `skills/`
2. Add a `SKILL.md` file with the skill definition
3. Update `.claude-plugin/plugin.json` to include the new skill path
4. Update this README with the skill description

### Extending as Marketplace

To add more plugins to this marketplace:

1. Add the plugin entry to `.claude-plugin/marketplace.json`
2. Specify the plugin's `name`, `source`, and `description`
3. The `source` can be:
   - `"./"` for plugins in this repository
   - A URL to another GitHub repository
   - A local path for development

## License

See [LICENSE](LICENSE) for details.