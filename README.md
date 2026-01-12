# close-superseded-prs

Reusable GitHub Actions workflow and composite action that automatically close
pull requests superseded by a newer PR.

## Why this exists

Teams often spin up a replacement PR when the original branch gets stale or needs
substantial rework. The original PR stays open, reviewers are unsure which one is
current, and the backlog gets noisy. This keeps the PR list tidy by closing the
superseded PRs as soon as the replacement PR merges.

## When to use it

- You regularly supersede PRs as part of refactors or migrations.
- You want a consistent, automated way to close obsolete PRs.
- You want a comment that explains why the older PR was closed.

## How it works

When a PR is merged, the workflow scans its description for variants of
`supersedes #123` (including misspellings like `supercedes`) and closes the
referenced PRs. It also leaves a comment with a link to the superseding PR and
optionally applies labels.

> [!NOTE]
> GitHub only applies `closes` keywords when the PR is merged into the default branch.
> This workflow mirrors that behavior by acting only on merged PRs, so superseded PRs
> are only closed after mainline merge.

Supported patterns include:

- `supersedes #123`
- `superseded by #123`
- `supersedes: #123`
- `supercedes#123`
- `replaces #123`

## Usage

Add a workflow in your repo that runs on merged PRs and either calls the reusable
workflow or uses the action directly.

### Option 1: Reusable workflow

```yaml
name: Close Superseded PRs

on:
  pull_request:
    types: [closed]

permissions:
  pull-requests: write
  issues: write

jobs:
  close-superseded:
    if: github.event.pull_request.merged == true
    uses: Liam-Deacon/close-superseded-prs/.github/workflows/close-superseded-prs.yml@v1
    with:
      comment_template_path: .github/close-superseded-comment.md
```

Then add a line to the replacement PR description, for example:

```
Supersedes #123
```

### Option 2: Composite action

```yaml
name: Close Superseded PRs

on:
  pull_request:
    types: [closed]

permissions:
  pull-requests: write
  issues: write

jobs:
  close-superseded:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
      - uses: Liam-Deacon/close-superseded-prs@v1
        with:
          comment_template_path: .github/close-superseded-comment.md
```

## Inputs

| Name                     | Description                                                                                 | Default                                                                      |
| ------------------------ | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `superseded_label`       | Label to apply to superseded PRs. Leave blank to skip.                                      | `superseded`                                                                 |
| `superseded_labels`      | Comma-separated labels to apply to superseded PRs. Leave blank to skip.                     | `""`                                                                         |
| `keywords`               | Comma-separated keywords to detect superseded PRs. Leave blank to use defaults.             | `""`                                                                         |
| `mark_duplicate_comment` | Add a `Duplicate of #<new PR>` comment to superseded PRs.                                   | `"false"`                                                                    |
| `ignored_branches`       | Comma-separated branch patterns to ignore (matches base or head).                           | `""`                                                                         |
| `comment_template_path`  | Path to a comment template file in the caller repo. Leave blank to use the default comment. | `""`                                                                         |
| `comment_footer`         | Footer text appended to the auto-comment. Leave blank to omit.                              | `This comment was added automatically by the close-superseded-prs workflow.` |

## Comment templates

If you provide `comment_template_path`, the workflow loads that file and uses it
as the comment body. You can include the following placeholders:

- `{{new_pr_number}}`
- `{{new_pr_title}}`
- `{{new_pr_url}}`
- `{{superseded_pr_number}}`

Example template:

```
## Superseded PR

This PR was superseded by #{{new_pr_number}}.
New PR: {{new_pr_title}} ({{new_pr_url}})

Closing #{{superseded_pr_number}} automatically.
```

## Docs

- GitHub Pages: https://liam-deacon.github.io/close-superseded-prs/

## GitHub App

If you prefer a no-workflow setup, the companion GitHub App lives in
https://github.com/LightBytes/close-superseded-prs-github-app (private).

## License

MIT
