# Agent Instructions for lsimons-template

> This file (`AGENTS.md`) is the canonical agent configuration. `CLAUDE.md` is a symlink to this file.

> **If this repo still says "template" everywhere:** run
> `mise run init` once to rename the placeholder package to your
> project name. See `scripts/init.py` for details.

Project template for Python CLI tools with standardized tooling.
See [README.md](README.md) for the user-facing description.

## Quick Reference

Every repo task lives in `.mise.toml`; `mise tasks` lists them.

| Task                 | What it does                                          |
| -------------------- | ----------------------------------------------------- |
| `mise install`       | Install the pinned toolchain                          |
| `mise run init`      | Rename the `template` placeholder to the project name |
| `mise run install`   | `uv sync --all-groups`; may relock                    |
| `mise run install-frozen` | `uv sync --all-groups --locked`; what CI runs    |
| `mise run lint`      | `ruff check` + `ruff format --check` + `actionlint`   |
| `mise run format`    | `ruff format` + `ruff check --fix`                    |
| `mise run typecheck` | `basedpyright` (strict)                               |
| `mise run test`      | `pytest` with coverage                                |
| `mise run ci`        | Full gate: lint + typecheck + test                    |
| `mise run audit`     | `zizmor` audit of workflows + dependabot config       |
| `mise run vuln`      | `osv-scanner` scan of every committed lockfile       |
| `mise run ci-watch`  | Watch GitHub Actions for the current branch           |

## Structure

```
.github/workflows/ci.yml  CI: lint/typecheck/test + osv-scanner + zizmor
.github/dependabot.yml    Weekly uv + github-actions updates, 7-day cooldown
.mise.toml                Pinned toolchain + every repo task
pyproject.toml            Package metadata, ruff, basedpyright, pytest config
scripts/init.py           Rename-to-your-project helper (`mise run init`)
src/template/             Placeholder package (renamed by `mise run init`)
tests/                    pytest suite
docs/spec/                Feature specifications
```

## Guidelines

**Code quality:**

- Full type annotations; `basedpyright` strict at 0 errors.
- Tests for all functionality; coverage floor 80%.
- `ruff` for lint and format; do not hand-format around it.
- No bare `# noqa` or `# type: ignore`. Narrow it and name the reason on
  the same line. Prefer fixing the cause.
- Never weaken a control to make a check pass: no lowered coverage floor,
  no unpinned actions or tools, no deleted tests.

**Supply chain:**

- `uv.lock` is committed and must stay in the tree.
- CI installs with `install-frozen`. Use plain `mise run install` when
  deliberately changing dependencies.
- `mise run vuln` must be clean. It also asserts every committed lockfile
  was scanned, so a new package manager needs adding to the task's glob
  list.
- Pin GitHub Actions to full-length commit SHAs; `zizmor` enforces it.
- Every `.mise.toml` tool is exact-pinned and invisible to dependabot;
  refresh with `mise up` and read the diff.

## Commit Message Convention

Follow [Conventional Commits](https://conventionalcommits.org/):

**Format:** `type(scope): description`

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `build`, `ci`, `perf`, `revert`, `improvement`, `chore`

## Session Completion

Work is not complete until every change is committed, pushed, and CI passes.

1. `mise run ci` (or the tasks that changed)
2. Commit everything — do not leave the working tree dirty
3. `git pull --rebase && git push`
4. `mise run ci-watch`; on failure `gh run view --log-failed`, fix, repeat

Never stop before CI is green.
