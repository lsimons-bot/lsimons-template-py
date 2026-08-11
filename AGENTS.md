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
- Do not silence a check without a written justification on the same
  line — a bare `# noqa` or `# type: ignore` is not acceptable, a
  narrow `# type: ignore[reportUnknownMemberType]  # <lib> ships no
  stubs` is. Prefer fixing the cause; suppress when the cause is
  outside this repo.
- Never weaken a control to make a check pass: do not lower the
  coverage floor, unpin an action, or delete a failing test.

**Supply chain — enforced by files in this repo:**

- `uv.lock` is committed and must stay in the tree.
- GitHub Actions are pinned to full-length commit SHAs with a `# vX.Y.Z`
  comment, and `zizmor` enforces that in CI.
- Tool versions in `.mise.toml` are pinned and are **not** covered by
  dependabot; refresh them deliberately with `mise up`.

**Supply chain — repo settings, which this repo cannot enforce:**

`Use this template` copies files, not settings. Do not assume any of
these is on; check, and see the checklist in
[README.md](README.md#per-repo-settings). Verify with `gh api` rather
than trusting this file:

- *Require actions to be pinned to a full-length commit SHA* must be
  enabled — `gh api repos/{owner}/{repo}/actions/permissions`.
- Private vulnerability reporting must be enabled, because
  [SECURITY.md](SECURITY.md) directs reporters to it — `gh api
  repos/{owner}/{repo}/private-vulnerability-reporting`.
- Dependabot security updates must be enabled — `gh api
  repos/{owner}/{repo}/automated-security-fixes`.

## Agent skills

### Git remote

Use GitHub with the `gh` CLI. `origin` is the upstream repo. Remotes are
clone-local and are not copied by `Use this template`, so read
`git remote -v` rather than assuming: some clones also carry a `bot`
remote pointing at a fork used for bot-authored pull requests.

### Issue tracker

Use GitHub issues via `gh issue`. See [docs/agents/issue-tracker.md](docs/agents/issue-tracker.md).

### Triage labels

Use `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human` and
`wontfix`, alongside `bug`, `documentation` and `enhancement`. See
[docs/agents/issue-tracker.md](docs/agents/issue-tracker.md). Labels are
repo state, not repo content: check `gh label list` and run
`mise run labels` if any are missing. At the time of writing they are
missing on `lsimons/lsimons-template-py` itself.

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
