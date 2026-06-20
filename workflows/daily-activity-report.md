---
name: "Daily Activity Report"
description: "Generates a daily summary of new issues, merged pull requests, and open blockers as a GitHub issue"
on:
  schedule: daily on weekdays
permissions:
  contents: read
  issues: read
  pull-requests: read
tools:
  github:
    mode: gh-proxy
    toolsets: [default]
safe-outputs:
  create-issue:
    title-prefix: "[daily-activity-report] "
    labels: [report]
---

# Daily Activity Report

Create a daily summary of recent activity in the repository for the team.

## Task

Summarize the repository activity from the last 24 hours (or since the last weekday if it's Monday).

### 1. New Issues
- List all issues opened in the last 24 hours.
- Provide a brief summary of each issue.

### 2. Merged Pull Requests
- List all pull requests merged in the last 24 hours.
- Include the PR title and author.

### 3. Open Blockers
- Identify any open issues or pull requests that are currently marked as blockers.
- Search for the keyword "blocker" in titles, descriptions, and labels.
- List these with a clear indication of why they are blocked if available.

### 4. Summary
- Provide a high-level summary of the repository's health based on the above information.

## Safe Outputs

- If there is any activity or any open blockers, use `create-issue` to publish the report.
- If no new issues were opened, no PRs were merged, and no open blockers were found, call `noop` with the message "No recent activity or open blockers found."
