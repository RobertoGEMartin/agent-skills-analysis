# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a DeepLearning.AI course repository teaching Agent Skills through progressive lessons (L1-L7). The two primary implementation projects are:

- **L6/**: A Python CLI task manager built with Typer, demonstrating custom skills for CLI development
- **L7/**: A multi-agent research system using the Claude Agent SDK

## Development Commands

### L6 - Task CLI

```bash
cd L6
uv sync                              # Install dependencies
uv run task <command>                # Run CLI commands
uv run pytest                        # Run all tests
uv run pytest -v                     # Verbose test output
uv run pytest tests/test_add.py      # Run specific test file
```

### L7 - Multi-Agent System

```bash
cd L7
uv sync                              # Install dependencies
uv run python agent.py               # Start interactive agent
```

Requires `.env` file with `ANTHROPIC_API_KEY` (and optionally `NOTION_TOKEN` for MCP server).

## Architecture

### L6 Task CLI Structure

```
L6/src/task/
├── main.py           # Entry point
├── commands/         # Typer command modules (add, list, done)
├── models.py         # Task dataclass, Priority enum
├── storage.py        # JSON persistence (~/.task/tasks.json)
├── display.py        # Rich-based terminal output
└── constants.py      # Exit codes (0=success, 1=error, 2=invalid)
```

**Flow**: `main.py` → `commands/__init__.py` → command file → storage/display

**ID Strategy**: Task IDs are derived from array index (1-indexed), not stored. After deletions, remaining tasks reindex.

### L7 Multi-Agent Structure

```
L7/
├── agent.py          # Main orchestration with ClaudeSDKClient
├── utils.py          # Display utilities
└── prompts/          # Agent prompt definitions
    ├── main_agent.md       # Orchestrator (sonnet)
    ├── docs_researcher.md  # Documentation subagent (haiku)
    ├── repo_analyzer.md    # Repository analysis subagent (haiku)
    └── web_researcher.md   # Web search subagent (haiku)
```

## Skills and Agents

The repository demonstrates the `.claude/` directory pattern for defining skills and agents:

- **L6/.claude/skills/**: CLI development skills (adding commands, reviewing, generating tests)
- **L6/.claude/agents/**: Code reviewer and test generator agents
- **L7/.claude/skills/**: Tool learning skill with progressive learning paths

## CLI Development Conventions (L6)

When adding or modifying CLI commands:

- Use `Annotated` type hints for Typer parameters
- All terminal output goes through the `display` module (no raw `print()`)
- Destructive operations require `--force` flag (default=False) or `[y/N]` confirmation
- Exit codes: `EXIT_SUCCESS=0`, `EXIT_ERROR=1`, `EXIT_INVALID_INPUT=2`
- Docstrings in imperative mood, under 60 characters
