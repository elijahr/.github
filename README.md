# elijahr/.github

Default workflows and community files for repos under [@elijahr](https://github.com/elijahr).

## What's here

### Reusable workflow: `momus.yml`

Centralized [Momus](https://github.com/axiomantic/momus) AI PR-review configuration. Consumer repos call it with a thin stub:

```yaml
# .github/workflows/momus.yml in any repo
name: Momus

on:
  pull_request:
    types: [opened, reopened, ready_for_review, synchronize]
  issue_comment:
    types: [created]
  workflow_dispatch:
    inputs:
      pr_number:
        description: PR number to review
        required: true
        type: string

jobs:
  call:
    permissions:
      contents: read
      pull-requests: write
      issues: write
    uses: elijahr/.github/.github/workflows/momus.yml@devel
    with:
      pr_number: ${{ github.event.pull_request.number || github.event.issue.number || github.event.inputs.pr_number }}
      event_name: ${{ github.event_name }}
      trigger_command: /ai-review
      trigger_mention: "@axiomantic-momus[bot]"
    secrets:
      LLM_API_KEY: ${{ secrets.LLM_API_KEY }}
      MOMUS_APP_ID: ${{ secrets.MOMUS_APP_ID }}
      MOMUS_APP_PRIVATE_KEY: ${{ secrets.MOMUS_APP_PRIVATE_KEY }}
```

The consumer repo needs `LLM_API_KEY` set as a repository (or org) secret.
`MOMUS_APP_ID` and `MOMUS_APP_PRIVATE_KEY` are optional — without them the bot
falls back to the default `GITHUB_TOKEN` and any `APPROVE` verdict is downgraded
to `COMMENT` (GitHub rejects approvals from `github-actions[bot]`). See
[axiomantic/momus SETUP.md](https://github.com/axiomantic/momus/blob/main/SETUP.md)
for App setup.

### Workflow template

`workflow-templates/momus.yml` makes the same workflow available in the GitHub
"New workflow" UI for any new repo under `@elijahr`.

## Defaults

- Provider: OpenRouter (`https://openrouter.ai/api/v1`)
- Model: `deepseek/deepseek-v4-pro`
- Trigger: `/ai-review` slash command and `@axiomantic-momus[bot]` mention

Override per repo via `with:` in the stub:

```yaml
    with:
      model: anthropic/claude-sonnet-4-6
      base_url: https://api.anthropic.com/v1
      trigger_command: /review
```

Per-repo behavior also tunable via `.momus.yaml` at the repo root — see
[`config-defaults.yaml`](https://github.com/axiomantic/momus/blob/main/momus/config-defaults.yaml).
