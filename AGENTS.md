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
| `mise run install`   | `uv sync --all-groups`                                |
| `mise run lint`      | `ruff check` + `ruff format --check` + `actionlint`   |
| `mise run format`    | `ruff format` + `ruff check --fix`                    |
| `mise run typecheck` | `basedpyright` (strict)                               |
| `mise run test`      | `pytest` with coverage                                |
| `mise run ci`        | Full gate: lint + typecheck + test                    |
| `mise run audit`     | `zizmor` audit of workflows + dependabot config       |
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
docs/spec/                Feature specifications
```

## Guidelines

**Code quality:**

- Full type annotations; `basedpyright` strict must report 0 errors.
- Tests for all functionality; the coverage floor is 80%
  (`--cov-fail-under=80` in `pyproject.toml`, enforced by `mise run test`).
- `ruff` for linting and formatting; do not hand-format around it.
- Do not silence a check without a written justification on the same
  line — a bare `# noqa` or `# type: ignore` is not acceptable, a
  narrow `# type: ignore[reportUnknownMemberType]  # <lib> ships no
  stubs` is. Prefer fixing the cause; suppress when the cause is
  outside this repo.
- Never weaken a control to make a check pass: do not lower the
  coverage floor, unpin an action, or delete a failing test.

**Supply chain:**

- `uv.lock` is committed and must stay in the tree.
- GitHub Actions are pinned to full-length commit SHAs with a `# vX.Y.Z`
  comment, and `zizmor` enforces that in CI.
- Every tool in `.mise.toml` is pinned to an exact version, python
  included. Nothing here is covered by dependabot, so refresh it
  deliberately with `mise up` and read the diff.

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
