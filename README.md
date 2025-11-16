# Agent Skills Collection

A collection of Claude skills for enhanced AI-assisted development workflows.

## Skills

### Codex Skill

A comprehensive skill for running Codex CLI commands with intelligent model selection and configuration management.

**Location**: `codex/`

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

See [codex/README.md](codex/README.md) for detailed documentation.

## Usage

To use these skills with Claude, reference the skill name or specific functionality in your requests.

## Structure

```
agent_skill/
├── codex/
│   ├── skill.md          # Skill implementation guide
│   └── README.md         # Skill documentation
└── README.md             # This file
```

## License

See [LICENSE](LICENSE) for details.