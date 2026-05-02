# elijahr/.github

Default workflows and community files for repos under [@elijahr](https://github.com/elijahr).

## What's here

### Reusable workflow: `pr-agent.yml`

Centralized [Qodo PR-Agent](https://github.com/qodo-ai/pr-agent) configuration. Consumer repos call it with a thin stub:

```yaml
# .github/workflows/pr-agent.yml in any repo
name: PR Agent
on:
  pull_request:
    types: [opened, reopened, ready_for_review, synchronize]
  issue_comment:
    types: [created]

jobs:
  call:
    permissions:
      contents: read
      issues: write
      pull-requests: write
    uses: elijahr/.github/.github/workflows/pr-agent.yml@devel
    secrets:
      OPENROUTER_KEY: ${{ secrets.OPENROUTER_KEY }}
```

The consumer repo needs `OPENROUTER_KEY` set as a repository secret. The
explicit `permissions:` block on the caller job is required \xe2\x80\x94 reusable
workflows cannot grant themselves permissions the caller doesn't have.

### Workflow template

`workflow-templates/pr-agent.yml` makes the same workflow available in the GitHub "New workflow" UI for any new repo under `@elijahr`.

## Defaults

- Primary model: `openrouter/deepseek/deepseek-v4-flash`
- Fallback: `openrouter/deepseek/deepseek-v4-pro`, `openrouter/anthropic/claude-sonnet-4.6`
- Auto-review and auto-describe enabled; auto-improve disabled (noisy)

Override per repo via `with:` in the stub:

```yaml
jobs:
  call:
    permissions:
      contents: read
      issues: write
      pull-requests: write
    uses: elijahr/.github/.github/workflows/pr-agent.yml@devel
    with:
      model: openrouter/anthropic/claude-sonnet-4.6
      auto_improve: "true"
    secrets:
      OPENROUTER_KEY: ${{ secrets.OPENROUTER_KEY }}
```
