# Development Setup

This project uses three tools for code quality:

## Tools

### Black
Code formatter that enforces consistent style.

- **Config**: `pyproject.toml` → `[tool.black]`
- **Run formatting**: `uv run black .`

### Ruff
Fast Python linter for finding and fixing issues.

- **Config**: `pyproject.toml` → `[tool.ruff]`
- **Check**: `uv run ruff check .`
- **Auto-fix**: `uv run ruff check --fix .`

### Pre-commit
Git hooks framework that runs linters and formatters before each commit.

- **Config**: `.pre-commit-config.yaml`
- **Already installed**: `uv run pre-commit install` (done during setup)
- **Test hooks**: `uv run pre-commit run --all-files`

## Installation

```bash
uv sync
```

This installs all dev dependencies and pre-commit hooks.

## Usage

Hooks run automatically on commit. To manually format/lint:

```bash
uv run black .           # Format with black
uv run ruff check --fix  # Lint and fix with ruff
```
