# lsimons-template

Project template for Python CLI tools with standardized tooling.

## Using This Template

1. Click **Use this template** on GitHub (or clone this repo).
2. Clone your new repo locally and run:

   ```bash
   mise trust           # once per clone: trust this repo's .mise.toml
   mise install         # pin + install python + uv
   mise run init        # rename `template` → your project name
   mise run install     # install project deps
   ```

   `mise run init` auto-detects your project name from the git remote
   (or directory name), stripping `lsimons-` / `-mono` / `-py` suffixes.
   Pass `--name foo` to override. See `scripts/init.py` for details.

3. Update `AGENTS.md` (and its `CLAUDE.md` symlink) with
   project-specific instructions, and `README.md` with what the project
   actually is.
4. Replace the placeholder code in `src/<project>/__init__.py` and
   `tests/test_placeholder.py` with your real implementation.
5. Run `/setup` in your agent of choice. Repository settings — issue
   labels, private vulnerability reporting, Dependabot security updates
   — are GitHub state rather than files, so `Use this template` does not
   copy them and nothing in this repo can create them. `/setup`
   configures them against the new repo directly.

## Included Configuration

- **Python 3.14+** required
- **ruff** for linting and formatting (line-length: 100)
- **basedpyright** strict mode for type checking
- **pytest** with an 80% coverage floor
- **GitHub Actions CI** on push/PR to main, with actions pinned to
  full-length commit SHAs, an [actionlint](https://github.com/rhysd/actionlint)
  workflow check and a [zizmor](https://docs.zizmor.sh/) workflow-security
  audit
- **Dependabot** for `uv` and `github-actions`, weekly, with a 7-day
  cooldown; `uv`'s own `exclude-newer` gives the same cooldown locally
- **`.mise.toml`** pins every tool to an exact version — python
  included — and defines every repo task
- **`.editorconfig`** so editors that are not running ruff still agree
  with it

## Project Structure

```
lsimons-template/
├── .github/workflows/ci.yml  # CI pipeline (mise-action + zizmor)
├── .github/dependabot.yml    # Weekly dependency updates
├── .editorconfig             # Editor defaults
├── .mise.toml                # Toolchain pin + task runner
├── docs/spec/                # Feature specifications
├── scripts/init.py           # Rename-to-your-project helper
├── src/template/             # Placeholder package (renamed on init)
│   └── __init__.py
├── tests/                    # Test files
├── AGENTS.md                 # AI agent instructions
├── CLAUDE.md -> AGENTS.md    # Claude Code compatibility
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE                   # Apache-2.0
├── pyproject.toml            # Project configuration
├── uv.lock                   # Committed; never gitignore this
└── README.md
```

`CLAUDE.md` is a git symlink (mode `120000`). A Windows clone needs
`core.symlinks` enabled to get a real link rather than a text file
containing the target path.

## Development Commands

```bash
mise trust            # once per clone
mise install          # one-time: pin + install toolchain
mise run install      # install project deps; may relock
mise run install-frozen  # install, failing on a stale uv.lock (what CI runs)
mise run test         # pytest
mise run lint         # ruff check + format --check + actionlint
mise run typecheck    # basedpyright
mise run format       # ruff format + --fix
mise run ci           # full CI gate
mise run audit        # zizmor audit of workflows + dependabot config
mise run vuln         # osv-scanner scan of every committed lockfile
mise run ci-watch     # watch GitHub Actions for the current branch
```

## License

See [LICENSE](./LICENSE).

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md). AI agents see
[AGENTS.md](./AGENTS.md).
