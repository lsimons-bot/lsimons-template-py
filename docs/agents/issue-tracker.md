# Issue tracker: GitHub

Issues for this project are managed as GitHub issues.

The issues live in the same remote as the source code (the GitHub default).

Use the `gh` CLI for all operations.

You can learn about the `gh` issue CLI with `gh issue --help`.

## Labels

The following issue labels are used:

| Label           | Description                                    | Color   |
| --------------- | ---------------------------------------------- | ------- |
| bug             | Something isn't working                        | #d73a4a |
| documentation   | Improvements or additions to documentation     | #0075ca |
| enhancement     | New feature or request                         | #a2eeef |
| needs-triage    | Maintainer needs to evaluate this issue        | #e6e6fa |
| needs-info      | Waiting on reporter for more information       | #e6e6fa |
| ready-for-agent | Fully specified, ready for an autonomous agent | #e6e6fa |
| ready-for-human | Requires human implementation                  | #e6e6fa |
| wontfix         | This will not be worked on                     | #ffffff |

GitHub's default label set does not include the four `needs-*` /
`ready-*` triage labels, and a repo created from this template starts
without them. Create or refresh the whole set with:

```bash
mise run labels
```

That task is idempotent (`gh label create --force`), so it is safe to
re-run. Check what a repo actually has with `gh label list`.

Repos created from this template may also carry GitHub's other stock
labels (`duplicate`, `good first issue`, `help wanted`, `invalid`,
`question`) and dependabot's (`dependencies`, `github_actions`). Those
are harmless; the table above is the set this project's workflow
relies on.
