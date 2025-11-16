# Codex Skill

A Claude skill for running Codex CLI commands for advanced code analysis, refactoring, and automated editing.

## Overview

This skill enables Claude to intelligently invoke the Codex CLI with appropriate model selection, reasoning effort levels, and safety configurations based on the task at hand.

## Features

- **Smart Model Selection**: Automatically chooses the right GPT model (gpt-5.1-codex-mini, gpt-5.1-codex, gpt-5.1, or gpt-5) based on task complexity
- **Configurable Reasoning Effort**: Adjusts reasoning depth (high/medium/low) to match the task requirements
- **Safety Controls**: Manages automation levels with `--yolo` flag and sandbox modes
- **Session Management**: Supports both new executions and resuming previous sessions
- **Error Handling**: Provides clear feedback and recovery options

## When to Use

Trigger this skill when:
- User explicitly asks to run `codex`, "Codex CLI", or `codex exec` / `codex resume`
- Non-trivial code analysis, refactoring, or migration is needed
- Large-scale automated editing across multiple files
- Resuming or continuing a previous Codex session

## Usage

Simply reference Codex in your request to Claude:

```
"Can you use Codex to refactor this module?"
"Run codex to analyze the authentication flow"
"Resume the last Codex session"
```

## Model Selection Guide

- **gpt-5.1-codex-mini** (default): Quick refactors, localized edits
- **gpt-5.1-codex**: Large-scale refactors, complex migrations
- **gpt-5.1**: Mixed design + code tasks, API design
- **gpt-5**: High-level architecture, trade-off analysis

## Requirements

- Codex CLI must be installed and accessible in your PATH
- Recommended shell alias setup for optimal workflow

## Documentation

See [skill.md](./skill.md) for the complete skill guide and detailed usage instructions.
