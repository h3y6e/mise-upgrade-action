# mise-upgrade-action

Run `mise upgrade --bump` and create a pull request when changes are detected.

## Usage

```yaml
name: update mise

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  update-mise:
    runs-on: ubuntu-slim
    timeout-minutes: 10

    steps:
      - uses: actions/checkout@v6

      - uses: h3y6e/mise-upgrade-action@v1
        id: mise-upgrade

      - name: Enable auto-merge
        if: steps.mise-upgrade.outputs.pr-number != ''
        run: gh pr merge "${{ steps.mise-upgrade.outputs.pr-number }}" --auto
        env:
          GH_TOKEN: ${{ github.token }}
```

This action runs `mise upgrade --bump` by default. If `tools` is set, it runs `mise upgrade --bump <tools...>`.

## Inputs

| Name | Required | Default | Description |
| --- | --- | --- | --- |
| `token` | no | workflow `github.token` | GitHub token used for mise setup and pull request creation. |
| `working-directory` | no | `.` | Directory where mise commands should run. |
| `tools` | no | all configured tools | Whitespace-separated mise tools to upgrade. |
| `branch` | no | `mise-upgrade-action/patch` | The pull request branch name. |
| `commit-message` | no | `[mise-upgrade-action] automated change` | The message to use when committing changes. |
| `title` | no | `Changes by mise-upgrade-action` | The title of the pull request. |
| `body` | no | action link | The body of the pull request. |

## Outputs

| Name | Description |
| --- | --- |
| `pr-number` | The pull request number. Empty when no pull request was created or updated. |
| `pr-url` | The pull request URL. Empty when no pull request was created or updated. |

## Requirements

The caller workflow must check out the repository before using this action.

The token used by this action must have permissions to push changes and create pull requests. When `token` is omitted, grant the workflow `GITHUB_TOKEN` the required permissions:

```yaml
permissions:
  contents: write
  pull-requests: write
```
