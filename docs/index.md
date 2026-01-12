---
title: Close Superseded PRs Workflow
---

# Close Superseded PRs Workflow

This repository provides a reusable workflow and composite action to close
superseded pull requests when a replacement PR is merged.

## Quick start

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
```

Add a line to the merged PR description:

```
Supersedes #123
```

## Configuration

You can customize labels, keywords, and comment templates via workflow inputs.
See `README.md` for the full input list and placeholder tokens.

## Default branch behavior

GitHub only applies `closes` keywords after merge to the default branch. This
workflow follows the same rule by acting only on merged PRs.
