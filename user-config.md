# User Configuration

This file contains user-specific preferences that agents can reference.
Edit this file to customize agent behavior for your workflow.

## Identity

```yaml
github_username: jordanpartridge
name: Jordan
```

## Repository Categories

Categorize your repos for different workflows:

```yaml
categories:
  WORK:
    description: Client projects, professional work
    rules:
      - Strict commit messages
      - PR required
      - No direct main pushes

  PERSONAL:
    description: Side projects, learning, experiments
    rules:
      - Relaxed, can push to main
      - Experimental branches OK

  OSS:
    description: Open source contributions, packages
    rules:
      - Follow project conventions
      - Detailed PR descriptions

  ARCHIVED:
    description: Inactive, historical
    rules:
      - Read-only reference
```

## Staleness Rules

When to flag items as stale:

```yaml
staleness:
  pr_stale_days: 7
  issue_stale_days: 30
  branch_stale_days: 14
  repo_inactive_days: 90
```

## Priority Signals

What indicates high priority:

```yaml
priority_signals:
  - Review requested from me
  - Issue assigned to me
  - PR blocking other work
  - CI/CD failures
  - Security alerts
```

## Commit Style

```yaml
commits:
  style: semantic
  format: "type(scope): description"
  types:
    - feat
    - fix
    - refactor
    - docs
    - test
    - chore
  include_ai_attribution: false
```

## PR Style

```yaml
pull_requests:
  title_format: semantic  # Same as commit style
  require_description: true
  description_sections:
    - What
    - Why
    - How to test
  link_issues: true
  request_reviewers: true
  add_labels: true
```

## Label System

```yaml
labels:
  types:
    - bug
    - enhancement
    - documentation
    - question
    - task

  priorities:
    - "priority:critical"
    - "priority:high"
    - "priority:medium"
    - "priority:low"

  statuses:
    - "status:triage"
    - "status:in-progress"
    - "status:blocked"
    - "status:review"

  areas:
    - "area:auth"
    - "area:api"
    - "area:ui"
    - "area:infra"
```

## Jira Integration

```yaml
jira:
  enabled: true
  base_url: https://company.atlassian.net
  project_keys:
    - PSTRAX
    - PROJ
```
