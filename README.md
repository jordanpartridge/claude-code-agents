# GitHub Orchestration Agents for Claude Code

A comprehensive agent system for managing GitHub workflows through Claude Code. This collection provides intelligent automation for commits, pull requests, issues, and cross-repository activity tracking.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Folder Structure](#folder-structure)
- [Usage](#usage)
  - [Approach 1: Using the Orchestrator (Recommended)](#approach-1-using-the-orchestrator-recommended)
  - [Approach 2: Using Individual Agents](#approach-2-using-individual-agents)
- [Agent Reference](#agent-reference)
- [Customization](#customization)
  - [How to Customize](#how-to-customize)
  - [Commit Message Templates](#commit-message-templates)
  - [PR Style Preferences](#pr-style-preferences)
  - [Issue Template Preferences](#issue-template-preferences)
  - [Label Systems](#label-systems)
  - [Staleness Rules](#staleness-rules)
  - [Repository Categories](#repository-categories)
  - [Full Example Configuration](#full-example-configuration)
  - [How Agents Read Configuration](#how-agents-read-configuration)
- [Workflow Examples](#workflow-examples)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before using these agents, you need to have the GitHub CLI (`gh`) installed and authenticated.

### Installing GitHub CLI

**Windows:**
```bash
winget install --id GitHub.cli
```

**macOS:**
```bash
brew install gh
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install gh
```

**Linux (Fedora):**
```bash
sudo dnf install gh
```

**Linux (Arch):**
```bash
sudo pacman -S github-cli
```

### Authenticating with GitHub

After installation, authenticate with your GitHub account:

```bash
gh auth login
```

You'll be prompted to:
1. Choose GitHub.com or GitHub Enterprise Server (usually GitHub.com)
2. Choose your preferred protocol (HTTPS or SSH)
3. Authenticate via web browser or paste an authentication token

**Verify authentication:**
```bash
gh auth status
```

You should see output like:
```
github.com
  ✓ Logged in to github.com as yourusername (keyring)
  ✓ Git operations for github.com configured to use https protocol.
  ✓ Token: gho_************************************
```

---

## Installation

### Step 1: Locate Your Claude Code Agents Directory

Claude Code automatically discovers agents in the `~/.claude/agents/` directory:

**Linux/macOS:**
```bash
~/.claude/agents/
```

**Windows:**
```
C:\Users\<YourUsername>\.claude\agents\
```

### Step 2: Copy Agent Files

Clone or copy these agent files to your agents directory:

```bash
# Option 1: Clone if it's a git repository
git clone <repository-url> ~/.claude/agents

# Option 2: Copy manually
# Just copy all .md files to ~/.claude/agents/
```

**Windows PowerShell:**
```powershell
# If cloning
git clone <repository-url> C:\Users\<YourUsername>\.claude\agents

# Or copy files manually to:
# C:\Users\<YourUsername>\.claude\agents\
```

### Step 3: Verify Installation

List the agents directory to confirm files are in place:

```bash
# Linux/macOS
ls -la ~/.claude/agents/

# Windows (PowerShell)
dir C:\Users\<YourUsername>\.claude\agents\
```

You should see:
- `git-commit-manager.md` (in root)
- `github/` folder containing:
  - `orchestrator.md`
  - `context.md`
  - `pr.md`
  - `issue.md`
  - `readme.md`
- `README.md` (this file)

### Step 4: Restart or Reload

Claude Code automatically discovers agents. If you're already running Claude Code:

1. Restart the Claude Code session, or
2. The agents should be available on your next prompt

---

## Folder Structure

```
~/.claude/agents/
├── README.md                      # This documentation
├── git-commit-manager.md          # Git commit specialist (git, not GitHub)
└── github/                        # GitHub-specific agents
    ├── orchestrator.md            # Main coordinator
    ├── context.md                 # Activity scanner
    ├── pr.md                      # Pull request manager
    ├── issue.md                   # Issue tracker
    └── readme.md                  # Documentation generator
```

**Why separate `git-commit-manager` from GitHub agents?**
- `git-commit-manager` handles local git operations (commits)
- The `github/` folder contains agents specific to GitHub platform features (PRs, issues, etc.)
- This keeps git version control separate from GitHub's social/collaborative features

**How Claude Code Discovers Agents:**
- Claude Code scans `~/.claude/agents/` for `.md` files with agent frontmatter
- Agents can be in the root or nested in subdirectories
- The `name:` field in frontmatter is how agents are referenced

---

## Usage

There are two ways to use these agents: via the orchestrator (recommended) or by calling individual agents directly.

### Approach 1: Using the Orchestrator (Recommended)

The orchestrator is your main interface. Just talk naturally, and it will delegate to the right agents.

#### What's Happening in My GitHub?

```
"What's happening in my GitHub projects?"
"Show me what needs my attention"
"Give me a summary of my repos"
```

**What happens:**
- Orchestrator delegates to `github/context` agent
- Scans all your repositories for activity
- Identifies PRs needing review, stale work, and recent commits
- Returns a prioritized summary

**Example output:**
```
## GitHub Status

### Needs Attention (Priority)
- [HIGH] PR #123 in my-app: Review requested 3 days ago
- [MED] Issue #45 in api-service: Assigned, no progress

### Active Work
- **my-app** (PERSONAL): 5 commits today, PR open
- **client-project** (WORK): Feature branch active

### Stale Items
- PR #89 in old-project: No activity 12 days
- Branch `feature/old-stuff` in archive-repo: 3 weeks stale

### Quick Stats
- Open PRs: 4 (2 need review)
- Open Issues: 12 (3 assigned)
- Commits this week: 23
```

#### Commit My Changes

```
"Commit my changes"
"Create semantic commits for this work"
```

**What happens:**
- Orchestrator delegates to `git-commit-manager`
- Analyzes all staged/unstaged changes
- Organizes into logical, atomic commits
- Creates semantic commit messages with no AI attribution

**Example output:**
```
Created 2 commits:

1. feat(auth): add JWT token refresh mechanism

   If this commit is applied it will enable automatic token refresh

   • Implements sliding window refresh strategy
   • Adds refresh endpoint at POST /api/auth/refresh

2. test(auth): add token refresh test coverage

   If this commit is applied it will verify refresh behavior

   • Tests expiration edge cases
   • Validates rate limiting
```

#### Create a Pull Request

```
"Create a PR for this branch"
"I'm ready to open a pull request"
"Make a PR that closes issue #45"
```

**What happens:**
- Orchestrator delegates to `github/pr` agent
- Gathers recent commits for context
- Generates title in semantic format
- Creates PR description with summary, changes, and testing notes
- Links related issues
- Requests appropriate reviewers

**Example output:**
```
## PR Created

**Title**: feat(auth): add JWT refresh mechanism
**URL**: https://github.com/user/repo/pull/156
**Base**: main ← feature/jwt-refresh

### Description Preview
Implements automatic JWT token refresh to improve user experience...

### Actions Taken
- Created PR #156
- Added labels: enhancement, auth
- Requested review from: @teammate
- Linked to issue #45

### Next Steps
- [ ] Wait for CI checks
- [ ] Address review feedback
```

#### Full Feature Flow

```
"I just finished the authentication feature - commit it and create a PR"
```

**What happens:**
- Orchestrator coordinates multiple agents
- `git-commit-manager` creates commits
- `github/pr` creates the pull request
- Returns complete summary of both operations

#### Clean Up Stale Work

```
"Show me stale PRs and branches"
"What work has been sitting around too long?"
"Clean up my inactive GitHub work"
```

**What happens:**
- Orchestrator delegates to `github/context`
- Scans for stale PRs (>7 days), issues (>30 days), branches (>14 days)
- Presents recommendations
- Can execute cleanup on your approval

### Approach 2: Using Individual Agents

For more specific control, reference agents directly.

#### Referencing Agents

To use a specific agent, mention it by name in your prompt:

**Format:** `Use the <agent-name> agent to...`

**Examples:**
```
"Use the github/context agent to scan my repositories"
"Have the git-commit-manager create commits for these changes"
"Ask the github/pr agent to create a pull request"
```

#### When to Use Individual Agents

Use individual agents when you:
- Want fine-grained control over a specific operation
- Need to bypass orchestrator logic for edge cases
- Are troubleshooting agent behavior
- Want to focus on one specific capability

---

## Agent Reference

### github/orchestrator

**What it does:**
The main coordinator that delegates to specialized agents and applies user preferences.

**Example prompts:**
- "What's happening in my GitHub?"
- "Commit my work and create a PR"
- "Show me what needs attention"
- "What should I work on today?"
- "Clean up stale PRs"

**What to expect:**
- Cross-agent coordination
- Structured summaries
- Priority-based recommendations
- Category-aware behavior (WORK vs PERSONAL repos)

**Model:** Sonnet (complex reasoning and coordination)

---

### github/context

**What it does:**
Scans repositories for activity, detects stale work, and provides actionable summaries.

**Example prompts:**
- "Scan my GitHub activity"
- "What repos have I been working on?"
- "Show me PRs that need my review"
- "List all open issues assigned to me"
- "What's been inactive lately?"

**What to expect:**
```
Active Work:
- repo-name (WORK): 12 commits this week, PR #45 open
- side-project (PERSONAL): 3 commits, branch feature/new-thing

Needs Attention:
- PR #67 awaiting your review (2 days)
- Issue #23 has new comments

Stale Items:
- PR #89: 14 days no activity
- Branch old-feature: 21 days stale
```

**Model:** Sonnet (pattern recognition across repos)

---

### git-commit-manager

**What it does:**
Creates atomic, well-organized git commits with semantic messages. No AI attribution.

**Example prompts:**
- "Commit these changes"
- "Create commits for my work"
- "Organize these changes into logical commits"

**What to expect:**
```
Created 3 commits:

1. feat(api): add user search endpoint

   If this commit is applied it will enable searching users by name/email

   • Implements fuzzy matching algorithm
   • Adds pagination support
   • Returns max 50 results per query

2. test(api): add search endpoint tests

   If this commit is applied it will verify search functionality

   • Tests exact and fuzzy matches
   • Validates pagination

3. docs(api): document search endpoint

   If this commit is applied it will add API documentation for search
```

**Model:** Haiku (focused task, fast execution)

---

### github/pr

**What it does:**
Creates and manages pull requests with proper descriptions, labels, and reviewers.

**Example prompts:**
- "Create a pull request"
- "Make a PR for this feature"
- "Open a PR that closes issue #45"
- "Add reviewers to PR #123"

**What to expect:**
```
## PR Created

**Title**: fix(auth): prevent session timeout on slow connections
**URL**: https://github.com/owner/repo/pull/234
**Base**: main ← bugfix/auth-timeout

### Description
Fixes authentication timeout issues on slow networks by...

## Changes
- Increased session timeout from 5s to 30s
- Added retry logic for token refresh
- Improved error messages

## Related Issues
Closes #45

## Testing
- [ ] Test on slow network (throttled to 3G)
- [ ] Verify timeout no longer occurs
- [ ] Check error messages are clear

### Labels Added
- bug
- priority:high
- area:auth

### Reviewers Requested
- @teammate1 (auth expert)
- @teammate2 (backend)
```

**Model:** Haiku (focused task, template-based)

---

### github/issue

**What it does:**
Creates, updates, and manages GitHub issues with proper formatting and templates.

**Example prompts:**
- "Create a bug report for the login timeout"
- "Make a feature request issue"
- "Show me all open issues assigned to me"
- "Close issue #45 with a note"
- "Add labels to issue #67"

**What to expect:**
```
## Issue Created

**Title**: Fix authentication timeout on slow connections
**URL**: https://github.com/owner/repo/issues/45
**Type**: Bug

### Description
## Bug Description
Users on slow connections experience authentication timeouts...

## Steps to Reproduce
1. Throttle network to 3G
2. Attempt login
3. Observe timeout after 5 seconds

## Expected Behavior
Login should succeed even on slow networks

## Actual Behavior
Session timeout occurs before auth completes

### Labels Applied
- bug
- priority:high
- area:auth

### Assigned To
@yourusername
```

**Model:** Haiku (template-driven)

---

### github/readme

**What it does:**
Analyzes codebases and creates comprehensive, approachable README documentation.

**Example prompts:**
- "Create a README for this project"
- "Generate documentation for this codebase"
- "Update the README with installation instructions"

**What to expect:**
A complete README with:
- Project overview and features
- Prerequisites and installation steps
- Usage examples (basic and advanced)
- Architecture explanation
- Configuration options
- Development setup
- Contributing guidelines
- Properly formatted code blocks
- Scannable structure

**Model:** Sonnet (analysis and comprehensive writing)

---

## Customization

All agent behavior can be customized through the `user-config.md` file. This is your single source of truth for preferences - agents read this file to understand your workflow, naming conventions, and repository organization.

### How to Customize

**Location:** `~/.claude/agents/user-config.md`

Edit this file to configure:
- Commit message formats and styles
- Pull request title/description templates
- Issue templates and label systems
- Staleness thresholds for PRs, issues, branches
- Repository categorization (WORK, PERSONAL, OSS, etc.)
- Priority signals and workflow preferences

**Changes take effect immediately** - no restart required. Agents read this file each time they run.

---

### Commit Message Templates

The `git-commit-manager` agent supports multiple commit styles. Choose one or create your own.

#### 1. Conventional Commits (Semantic)

Clean, type-prefixed commits for changelog automation.

**Example config:**
```yaml
commits:
  style: semantic
  format: "type(scope): description"
  types:
    - feat      # New features
    - fix       # Bug fixes
    - refactor  # Code improvements
    - docs      # Documentation
    - test      # Test additions/changes
    - chore     # Maintenance tasks
  include_ai_attribution: false
```

**Example commits:**
```
feat(auth): add JWT token refresh mechanism
fix(api): prevent null pointer in user search
refactor(db): optimize query performance
docs(readme): add installation instructions
test(auth): add token expiration tests
chore(deps): update dependencies
```

---

#### 2. Gitmoji Style

Emoji-prefixed commits for visual scanning.

**Example config:**
```yaml
commits:
  style: gitmoji
  format: "emoji type(scope): description"
  emojis:
    feat: "✨"
    fix: "🐛"
    docs: "📝"
    refactor: "♻️"
    test: "✅"
    chore: "🔧"
  include_ai_attribution: false
```

**Example commits:**
```
✨ feat(auth): add JWT token refresh
🐛 fix(api): prevent null pointer in search
♻️ refactor(db): optimize query performance
📝 docs(readme): add installation guide
✅ test(auth): add token expiration tests
🔧 chore(deps): update dependencies
```

---

#### 3. Angular Convention

Detailed commits with optional body and footer.

**Example config:**
```yaml
commits:
  style: angular
  format: "type(scope): subject\n\nbody\n\nfooter"
  types:
    - feat
    - fix
    - docs
    - style
    - refactor
    - perf
    - test
    - build
    - ci
    - chore
  max_subject_length: 50
  wrap_body_at: 72
  include_ai_attribution: false
```

**Example commits:**
```
feat(auth): add JWT token refresh mechanism

Implements sliding window refresh strategy to maintain
user sessions without requiring re-authentication.

- Adds refresh endpoint at POST /api/auth/refresh
- Implements 15-minute sliding window
- Includes rate limiting (max 10 refreshes/hour)

Closes #234
```

---

#### 4. Simple Descriptive

No prefixes, just clear descriptions.

**Example config:**
```yaml
commits:
  style: simple
  format: "Clear description of what changed"
  capitalization: sentence  # or "lowercase"
  include_ai_attribution: false
```

**Example commits:**
```
Add JWT token refresh mechanism
Fix null pointer exception in user search
Optimize database query performance
Update README with installation instructions
Add tests for token expiration handling
```

---

#### 5. Issue-First Style

Lead with issue numbers for traceability.

**Example config:**
```yaml
commits:
  style: issue-first
  format: "#issue: description"
  require_issue_number: true
  include_ai_attribution: false
```

**Example commits:**
```
#234: Add JWT token refresh mechanism
#235: Fix null pointer in user search
#236: Optimize database query performance
#237: Add installation instructions to README
#238: Add token expiration tests
```

---

#### 6. Signed Commits

Include sign-off for DCO compliance.

**Example config:**
```yaml
commits:
  style: semantic
  format: "type(scope): description"
  include_signoff: true
  signoff_name: "Jordan Partridge"
  signoff_email: "jordan@example.com"
  include_ai_attribution: false
```

**Example commits:**
```
feat(auth): add JWT token refresh mechanism

Signed-off-by: Jordan Partridge <jordan@example.com>
```

---

### PR Style Preferences

Customize pull request titles, descriptions, labels, and reviewers.

#### Basic PR Configuration

**Example config:**
```yaml
pull_requests:
  title_format: semantic  # Match commit style
  require_description: true
  description_sections:
    - What      # What changed
    - Why       # Why this change
    - Testing   # How to test
  link_issues: true
  request_reviewers: true
  add_labels: true
```

**Resulting PR:**
```markdown
Title: feat(auth): add JWT token refresh

## What
Implements automatic JWT token refresh to maintain sessions.

## Why
Users were experiencing frequent logouts on slow connections.

## Testing
- [ ] Test on throttled network (3G)
- [ ] Verify session persists across refreshes
- [ ] Check rate limiting works

Closes #234
```

---

#### Custom PR Description Templates

**Example config:**
```yaml
pull_requests:
  title_format: semantic
  description_template: |
    ## Summary
    {summary}

    ## Changes
    {changes_list}

    ## Screenshots
    {screenshots_if_ui}

    ## Related Issues
    {linked_issues}

    ## Checklist
    - [ ] Tests added/updated
    - [ ] Documentation updated
    - [ ] No breaking changes (or documented)

  auto_request_reviewers:
    - username: teammate1
      areas: [auth, api]
    - username: teammate2
      areas: [ui, frontend]
```

---

#### PR Labels by Type

**Example config:**
```yaml
pull_requests:
  auto_labels:
    feat: [enhancement, needs-review]
    fix: [bug, priority:high]
    docs: [documentation]
    refactor: [maintenance]
    test: [testing]

  size_labels:
    small: 1-50      # lines changed
    medium: 51-200
    large: 201-500
    xlarge: 501+
```

**Resulting PR:**
```
Labels: enhancement, needs-review, size:medium
Reviewers: @teammate1, @teammate2
```

---

### Issue Template Preferences

Customize how the `github/issue` agent creates issues.

#### Bug Report Template

**Example config:**
```yaml
issues:
  bug_template: |
    ## Bug Description
    {description}

    ## Steps to Reproduce
    {steps}

    ## Expected Behavior
    {expected}

    ## Actual Behavior
    {actual}

    ## Environment
    - OS: {os}
    - Version: {version}

  auto_labels:
    bug: [bug, needs-triage]

  auto_assign: true  # Assign to issue creator
```

---

#### Feature Request Template

**Example config:**
```yaml
issues:
  feature_template: |
    ## Feature Description
    {description}

    ## Problem It Solves
    {problem}

    ## Proposed Solution
    {solution}

    ## Alternatives Considered
    {alternatives}

  auto_labels:
    feature: [enhancement, needs-discussion]
```

---

#### Task/Checklist Template

**Example config:**
```yaml
issues:
  task_template: |
    ## Objective
    {objective}

    ## Tasks
    {task_list}

    ## Acceptance Criteria
    {criteria}

  auto_labels:
    task: [task, sprint]
```

---

### Label Systems

Define your label taxonomy for consistent organization.

#### Multi-Dimensional Labeling

**Example config:**
```yaml
labels:
  # Type labels
  types:
    - bug
    - enhancement
    - documentation
    - question
    - task

  # Priority labels
  priorities:
    - "priority:critical"  # Production down
    - "priority:high"      # Impacts users
    - "priority:medium"    # Should fix soon
    - "priority:low"       # Nice to have

  # Status labels
  statuses:
    - "status:triage"      # Needs review
    - "status:in-progress" # Being worked on
    - "status:blocked"     # Waiting on something
    - "status:review"      # Ready for review
    - "status:done"        # Completed

  # Area labels
  areas:
    - "area:auth"
    - "area:api"
    - "area:ui"
    - "area:database"
    - "area:infra"
    - "area:docs"
```

**Usage example:**
```
Issue #45: Login timeout on slow connections
Labels: bug, priority:high, area:auth, status:in-progress
```

---

#### Custom Label Colors

**Example config:**
```yaml
labels:
  custom:
    - name: "breaking-change"
      color: "d73a4a"  # Red
      description: "Introduces breaking API changes"

    - name: "good-first-issue"
      color: "7057ff"  # Purple
      description: "Good for newcomers"

    - name: "help-wanted"
      color: "008672"  # Green
      description: "Extra attention needed"
```

---

### Staleness Rules

Define when PRs, issues, branches, and repos are considered stale.

**Example config:**
```yaml
staleness:
  # Pull requests
  pr_stale_days: 7
  pr_warning_days: 5      # Warn before marking stale
  pr_close_days: 30       # Auto-close if no activity

  # Issues
  issue_stale_days: 30
  issue_warning_days: 21
  issue_close_days: 90

  # Branches
  branch_stale_days: 14
  branch_warning_days: 10
  branch_delete_days: 30  # Suggest deletion

  # Repositories
  repo_inactive_days: 90
  repo_archive_days: 180  # Suggest archiving

  # Exceptions
  never_stale_labels:
    - "priority:critical"
    - "help-wanted"
    - "good-first-issue"

  never_stale_branches:
    - main
    - master
    - develop
    - staging
    - production
```

**Agent behavior:**
```
github/context will report:

Stale Items:
- PR #89: No activity for 12 days (stale threshold: 7 days)
  Recommendation: Review or close

- Branch feature/old-stuff: 21 days inactive (delete threshold: 30 days)
  Recommendation: Merge or delete soon

- Repo old-project: 95 days inactive (archive threshold: 180 days)
  Status: Approaching archive recommendation
```

---

### Repository Categories

Organize repos by purpose to apply different workflows.

**Example config:**
```yaml
categories:
  WORK:
    description: Client projects, professional work
    repos:
      - client-app
      - company-api
      - internal-tools
    rules:
      commit_style: angular       # Strict conventional commits
      require_pr: true            # Never push to main
      require_reviews: 2          # Need 2 approvals
      auto_labels: [work]
      staleness_multiplier: 0.5   # Flag stale faster

  PERSONAL:
    description: Side projects, experiments
    repos:
      - my-blog
      - learning-rust
      - weekend-project
    rules:
      commit_style: simple        # Relaxed style
      allow_main_push: true       # Can push directly
      require_reviews: 0          # No review needed
      auto_labels: [personal]
      staleness_multiplier: 2.0   # More lenient

  OSS:
    description: Open source contributions
    repos:
      - laravel/*                 # Wildcard matching
      - symfony/*
    rules:
      commit_style: semantic      # Follow project conventions
      require_pr: true
      require_signoff: true       # DCO compliance
      auto_labels: [oss, contribution]
      follow_project_conventions: true

  ARCHIVED:
    description: Historical reference, read-only
    repos:
      - old-project-2020
      - deprecated-tool
    rules:
      readonly: true
      skip_staleness_checks: true
      auto_labels: [archived]
```

**Agent behavior with categories:**
```
github/context will show:

Active Work by Category:

WORK (strict mode):
- client-app: 12 commits this week, PR #45 open
  Status: 2 approvals needed before merge

PERSONAL (relaxed mode):
- my-blog: 3 commits, pushed to main
  Status: All good, no PR required

OSS (following conventions):
- laravel/framework: PR #1234 awaiting maintainer review
  Status: Signed-off commits verified
```

---

### Full Example Configuration

Here's a complete `user-config.md` combining multiple preferences:

```yaml
# User Configuration

## Identity
github_username: jordanpartridge
name: Jordan
timezone: America/New_York

## Repository Categories
categories:
  WORK:
    description: Professional client work
    rules:
      commit_style: angular
      require_pr: true
      require_reviews: 2
      staleness_multiplier: 0.5

  PERSONAL:
    description: Side projects and learning
    rules:
      commit_style: semantic
      allow_main_push: true
      staleness_multiplier: 2.0

  OSS:
    description: Open source contributions
    rules:
      commit_style: semantic
      require_signoff: true
      follow_project_conventions: true

## Staleness Rules
staleness:
  pr_stale_days: 7
  pr_close_days: 30
  issue_stale_days: 30
  issue_close_days: 90
  branch_stale_days: 14
  branch_delete_days: 30
  repo_inactive_days: 90

  never_stale_labels:
    - "priority:critical"
    - "help-wanted"

## Commit Style
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
  max_subject_length: 50

## PR Style
pull_requests:
  title_format: semantic
  require_description: true
  description_sections:
    - What
    - Why
    - Testing
  link_issues: true
  request_reviewers: true
  add_labels: true

  auto_request_reviewers:
    - username: teammate1
      areas: [auth, api]
    - username: teammate2
      areas: [ui, frontend]

  auto_labels:
    feat: [enhancement, needs-review]
    fix: [bug, priority:high]
    docs: [documentation]

## Issue Templates
issues:
  bug_template: |
    ## Bug Description
    {description}

    ## Steps to Reproduce
    {steps}

    ## Expected vs Actual
    Expected: {expected}
    Actual: {actual}

  auto_labels:
    bug: [bug, needs-triage]
  auto_assign: true

## Label System
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

## Priority Signals
priority_signals:
  - Review requested from me
  - Issue assigned to me
  - PR blocking other work
  - CI/CD failures
  - Security alerts
  - Label: priority:critical
```

**To use this configuration:**

1. Copy the entire config above
2. Paste into `~/.claude/agents/user-config.md`
3. Edit values to match your preferences
4. Agents will automatically use these settings

---

### How Agents Read Configuration

Agents follow this priority system when reading preferences:

#### 1. User Config (Highest Priority)
`~/.claude/agents/user-config.md` - Your explicit preferences always win.

```yaml
# If you set this:
commits:
  style: gitmoji

# All commits will use gitmoji, regardless of defaults
```

#### 2. Repository-Specific Config
`.github/agent-config.yaml` in the repository - Overrides for specific repos.

```yaml
# In my-project/.github/agent-config.yaml
commits:
  style: angular  # This repo uses Angular style

# Overrides user config only for this repo
```

#### 3. Category Defaults
Based on repository category (WORK, PERSONAL, OSS, ARCHIVED).

```yaml
# If repo is in WORK category:
categories:
  WORK:
    rules:
      commit_style: angular

# Used if no user or repo config exists
```

#### 4. Agent Defaults (Lowest Priority)
Built-in defaults when nothing else is specified.

```yaml
# Agent's built-in default
commits:
  style: semantic

# Only used if no other config exists
```

---

#### Configuration Resolution Example

**Scenario:** You're working in `client-app` (WORK category)

**User config:**
```yaml
commits:
  style: semantic

categories:
  WORK:
    rules:
      require_pr: true
```

**Repository config** (`.github/agent-config.yaml`):
```yaml
commits:
  style: angular  # Override for this repo
```

**Result:**
- Commit style: `angular` (repo config wins)
- Require PR: `true` (from category rules)
- Everything else: User config or agent defaults

---

#### How to Check What Config Is Active

Ask the orchestrator:

```
"What configuration are you using for commits?"
"Show me the active PR settings"
"What are my staleness rules?"
```

**Example response:**
```
Active Configuration for client-app:

Commit Style: angular (from repo config)
  Format: type(scope): subject

PR Requirements: (from WORK category)
  - PR required: true
  - Reviews needed: 2
  - Auto-labels: [work, needs-review]

Staleness Rules: (from user config)
  - PR stale: 7 days
  - Issue stale: 30 days
  - Branch stale: 14 days
```

---

## Workflow Examples

### Complete Feature Flow

**Scenario:** You just finished implementing a new feature.

```
You: "I just finished the user authentication feature"