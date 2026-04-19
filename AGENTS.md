# Agent Instructions for lsimons-$project

> This file (`AGENTS.md`) is the canonical agent configuration. `CLAUDE.md` is a symlink to this file.

<!-- Replace $project with your project name and update this description -->
Brief project description.

## Quick Reference

<!-- Update these commands for your project -->
- **One-time**: `mise install`
- **Setup**: `mise run install` (or `uv sync --all-groups`)
- **Test**: `mise run test` (or `uv run pytest`)
- **Lint**: `mise run lint` (or `uv run ruff check . && uv run ruff format --check .`)
- **Typecheck**: `mise run typecheck` (or `uv run basedpyright`)
- **Format**: `mise run format` (or `uv run ruff format . && uv run ruff check --fix .`)
- **Full CI gate**: `mise run ci`

## Structure

<!-- Document your project structure here -->

## Guidelines

**Code quality:**
- Full type annotations (basedpyright: 0 errors)
- Tests for all functionality
- ruff for linting and formatting

## Commit Message Convention

Follow [Conventional Commits](https://conventionalcommits.org/):

**Format:** `type(scope): description`

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `build`, `ci`, `perf`, `revert`, `improvement`, `chore`

## Session Completion

Work is NOT complete until `git push` succeeds.

1. **Quality gates** (if code changed):
   ```bash
   mise run ci
   ```

2. **Push**:
   ```bash
   git pull --rebase && git push
   git status  # must show "up to date with origin"
   ```

Never stop before pushing. If push fails, resolve and retry.
