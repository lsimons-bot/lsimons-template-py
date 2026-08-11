# Issue tracker: GitHub

Issues for this project are managed as GitHub issues.

The issues live in the same remote as the source code (the GitHub default).

Use the `gh` CLI for all operations.

You can learn about the `gh` issue CLI with `gh issue --help`.

## Labels

Labels are repository *state*, not repository content: no file in this
repo can create them, and `Use this template` does not copy them.
**Always check `gh label list` before relying on a label existing.**

The `labels` task in `.mise.toml` is the authoritative definition —
names and colours live there, so that they cannot drift from this file.
What each label means:

| Label           | Use it when                                        |
| --------------- | -------------------------------------------------- |
| bug             | Something isn't working                             |
| documentation   | The change is to documentation                      |
| enhancement     | New feature or request                              |
| needs-triage    | A maintainer still has to evaluate this issue       |
| needs-info      | Blocked waiting on the reporter                     |
| ready-for-agent | Fully specified; an autonomous agent can pick it up |
| ready-for-human | Requires human judgement or access to implement     |
| wontfix         | Deliberately not being worked on                    |

Create or refresh the whole set with:

```bash
mise run labels
```

That task is idempotent (`gh label create --force`), so it is safe to
re-run, but it mutates the GitHub remote.

**Current state of `lsimons/lsimons-template-py` itself:** `bug`,
`documentation`, `enhancement` and `wontfix` exist; `needs-triage`,
`needs-info`, `ready-for-agent` and `ready-for-human` do **not**. Run
`mise run labels` to fix that. Any repo will also carry GitHub's stock
labels (`duplicate`, `good first issue`, `help wanted`, `invalid`,
`question`) and dependabot's (`dependencies`, `github_actions`); those
are harmless and the table above is the set this project's workflow
relies on.
