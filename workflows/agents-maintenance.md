---
name: Agents Maintenance
description: 'Review merged pull requests and updated source files since the last run, then open a pull request that keeps AGENTS.md accurate and current.'
on:
  schedule: weekly
permissions:
  contents: read
  pull-requests: read
  issues: read
tools:
  github:
    mode: gh-proxy
    toolsets: [default]
safe-outputs:
  create-pull-request:
    allowed-files: ['AGENTS.md']
---

# Agents Maintenance

## Task

1. Review all merged pull requests and changes to source files (agents, instructions, skills, hooks, workflows, plugins) since the last time this workflow ran.
2. Analyze whether the current content of `AGENTS.md` accurately reflects the project's overview, repository structure, setup commands, and development workflow.
3. If updates are needed to keep `AGENTS.md` current and accurate (e.g., new file patterns, updated commands, or structural changes), propose the necessary changes.
4. Use `create-pull-request` to open a pull request with the updated `AGENTS.md`.
5. If no changes are needed, call `noop` with a brief explanation.

## Safe Outputs

- Use `create-pull-request` to propose updates to `AGENTS.md`.
- Use `noop` if `AGENTS.md` is already up to date.
