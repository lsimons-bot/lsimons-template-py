# lsimons-template

Project template for Python CLI tools with standardized tooling.

## Using This Template

1. Copy this repository to create a new project
2. Replace placeholders throughout:
   - `$project` - project name (e.g., `myproject`)
   - `$project_pkg` - Python package name with underscores (e.g., `my_project`)
   - `$shortDescription` - one-line description

3. Rename/create your package directory under `src/` or root
4. Update `pyproject.toml`:
   - Change `name` and `description`
   - Update `[project.scripts]` if creating a CLI
   - Update `[tool.setuptools.packages.find]` include pattern
   - Update `[tool.pytest.ini_options]` coverage target

5. Update `AGENTS.md` (and `CLAUDE.md` symlink) with project-specific instructions

## Included Configuration

- **Python 3.14+** required
- **ruff** for linting and formatting (line-length: 100)
- **basedpyright** strict mode for type checking
- **pytest** with 80% coverage requirement
- **GitHub Actions CI** on push/PR to main, with actions pinned to
  full-length commit SHAs (the repo setting *Require actions to be
  pinned to a full-length commit SHA* is enabled)

> **Note:** CI is red on this template repo itself — the `$project`
> placeholder in `pyproject.toml` makes the project name malformed on
> purpose. Once you fork and do the search/replace described above, CI
> turns green.

## Project Structure

```
lsimons-$project/
├── .github/workflows/ci.yml  # CI pipeline
├── docs/spec/                # Feature specifications
├── src/                      # Source code (or use root package)
├── tests/                    # Test files
├── AGENTS.md                 # AI agent instructions
├── CLAUDE.md -> AGENTS.md    # Claude Code compatibility
├── pyproject.toml            # Project configuration
└── README.md
```

## Development Commands

```bash
# Setup
uv venv && uv sync --all-groups

# Run tests
uv run pytest

# Lint and format
uv run ruff check .
uv run ruff format .

# Type check
uv run basedpyright
```

## License

See [LICENSE.md](./LICENSE.md).

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).
