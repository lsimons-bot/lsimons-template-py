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
| `mise install`       | Install the pinned toolchain (python, uv)             |
| `mise run init`      | Rename the `template` placeholder to the project name |
| `mise run install`   | `uv sync --all-groups`                                |
| `mise run lint`      | `ruff check` + `ruff format --check`                  |
| `mise run format`    | `ruff format` + `ruff check --fix`                    |
| `mise run typecheck` | `basedpyright` (strict)                               |
| `mise run test`      | `pytest` with coverage                                |
| `mise run ci`        | Full gate: lint + typecheck + test                    |
| `mise run audit`     | `zizmor` audit of workflows + dependabot config       |
| `mise run labels`    | Create/refresh the triage labels on the GitHub remote |
| `mise run ci-watch`  | Watch GitHub Actions for the current branch           |

## Structure

```
.github/workflows/ci.yml  CI: mise run lint/typecheck/test + zizmor audit
.github/dependabot.yml    Weekly uv + github-actions updates, 7-day cooldown
.mise.toml                Pinned toolchain + every repo task
pyproject.toml            Package metadata, ruff, basedpyright, pytest config
scripts/init.py           Rename-to-your-project helper (`mise run init`)
src/template/             Placeholder package (renamed by `mise run init`)
tests/                    pytest suite
docs/agents/              Agent-facing process docs (issue tracker)
docs/spec/                Feature specifications
```

## Guidelines

**Code quality:**

- Full type annotations; `basedpyright` strict must report 0 errors.
- Tests for all functionality; the coverage floor is 80%
  (`--cov-fail-under=80` in `pyproject.toml`, enforced by `mise run test`).
- `ruff` for linting and formatting; do not hand-format around it.
- Never silence a check to make it pass (`# noqa`, `# type: ignore`,
  lowering the coverage floor, unpinning an action). Fix the cause.

**Supply chain:**

- `uv.lock` is committed and must stay in the tree.
- GitHub Actions are pinned to full-length commit SHAs with a `# vX.Y.Z`
  comment. The repo setting *Require actions to be pinned to a
  full-length commit SHA* is enabled, and `zizmor` enforces it in CI.
- Tool versions in `.mise.toml` are pinned and are **not** covered by
  dependabot; refresh them deliberately with `mise up`.

## Agent skills

### Git remote

Use GitHub with the `gh` CLI. `origin` is the upstream repo; `bot` is a
fork used for bot-authored pull requests.

### Issue tracker

Use GitHub issues via `gh issue`. See [docs/agents/issue-tracker.md](docs/agents/issue-tracker.md).

### Triage labels

Use `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human` and
`wontfix`, alongside `bug`, `documentation` and `enhancement`. See
[docs/agents/issue-tracker.md](docs/agents/issue-tracker.md). Run
`mise run labels` if a repo is missing them.

## Commit Message Convention

Follow [Conventional Commits](https://conventionalcommits.org/):

**Format:** `type(scope): description`

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `build`, `ci`, `perf`, `revert`, `improvement`, `chore`

## Session Completion

Work is NOT complete until every change is committed, pushed, and CI passes.

1. **Quality gates** (if code changed):

   ```bash
   mise run ci
   ```

2. **Commit**: stage and commit every change from this session. Do not leave the working tree dirty.

   ```bash
   git status              # review untracked and unstaged files
   git add <files>
   git commit -m "<type>(<scope>): <description>"
   ```

3. **Push**:

   ```bash
   git pull --rebase && git push
   git status  # must show "up to date with origin"
   ```

4. **Verify CI**:

   ```bash
   mise run ci-watch
   ```

   On failure, inspect with `gh run view --log-failed`, fix, commit, push, and re-watch.

Never stop before CI is green. If anything fails, resolve and retry.
