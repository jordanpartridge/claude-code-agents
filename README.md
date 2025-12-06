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

## Workflow Examples

### Complete Feature Flow

**Scenario:** You just finished implementing a new feature.

```
You: "I just finished the user authentication feature"