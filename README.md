# Claude Code Agent Library

A comprehensive collection of specialized agents for software development workflows. This library provides intelligent automation for architecture planning, code quality, git operations, GitHub workflows, platform-specific tasks, and daily productivity.

## Quick Reference

| Agent | Category | Purpose |
|-------|----------|---------|
| **advisor** | architecture | Strategic architecture assessment for multi-repo planning and system integration |
| **pr-review** | code | Comprehensive code review for security, performance, quality, and standards |
| **test-writer** | code | Generate comprehensive test suites with proper mocking and coverage |
| **troubleshoot** | code | Systematic debugging of errors, exceptions, and unexpected behavior |
| **commit-manager** | git | Create atomic, semantic commits with proper organization |
| **orchestrator** | github | Main coordinator for GitHub workflows and cross-agent automation |
| **context** | github | Scan repositories for activity, detect stale work, provide summaries |
| **pr** | github | Create and manage pull requests with proper descriptions and labels |
| **issue** | github | Create and manage GitHub issues with templates and organization |
| **readme** | github | Generate comprehensive, approachable project documentation |
| **conduit-compatibility** | platform | Test and fix cross-platform compatibility issues in Conduit packages |
| **pest-migration** | platform | Migrate PHPUnit tests to Pest format in Laravel projects |
| **daily-standup** | workflow | Morning report of PRs, issues, CI failures, and priorities |

## Folder Structure

```
├── user-config.md           # Personal preferences  
├── architecture/            # Multi-repo planning (advisor)
├── code/                    # Code quality (troubleshoot, pr-review, test-writer)
├── git/                     # Git operations (commit-manager)
├── github/                  # GitHub workflow (orchestrator, context, pr, issue, readme)
├── platform/                # Platform-specific (conduit, laravel/pest-migration)
└── workflow/                # Daily productivity (daily-standup)
```

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

You will be prompted to:
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

## Agent Catalog

### Architecture

#### advisor
**Purpose:** Strategic architecture assessment and multi-repo planning  
**Use when:** Auditing codebase ecosystems, planning integrations, designing how services work together  
**Model:** Sonnet

**Example prompts:**
- "Analyze how these 3 repos should integrate"
- "What is the best architecture for this microservices setup?"
- "Audit the current system and recommend improvements"

---

### Code Quality

#### troubleshoot
**Purpose:** Systematic debugging and root cause analysis  
**Use when:** Encountering bugs, exceptions, test failures, or unexpected behavior  
**Model:** Sonnet

**Example prompts:**
- "Debug this authentication timeout error"
- "Why is this test failing?"
- "Trace through this execution path and find the issue"

**What to expect:**
- Stack trace analysis
- Recent change examination
- Root cause identification with confidence levels
- Actionable next steps

---

#### pr-review
**Purpose:** Comprehensive pull request code review  
**Use when:** Reviewing PRs for security, performance, quality, or standards compliance  
**Model:** Sonnet

**Example prompts:**
- "Review PR #123 for security issues"
- "Check this PR for performance problems"
- "Do a complete code review"

**What to expect:**
- Security vulnerability detection
- Performance issue identification
- Code quality assessment
- Logic error detection
- Test coverage analysis
- Standards compliance check

---

#### test-writer
**Purpose:** Generate comprehensive test suites  
**Use when:** Writing tests, improving coverage, or generating tests for new/existing code  
**Model:** Sonnet

**Example prompts:**
- "Write tests for this authentication module"
- "Generate test coverage for these components"
- "Create integration tests for the API"

**What to expect:**
- Framework detection (Pest, PHPUnit, Jest, pytest)
- Happy path coverage
- Edge case testing
- Error condition handling
- Proper mocking strategies
- Maintainable, non-brittle tests

---

### Git Operations

#### commit-manager
**Purpose:** Create atomic, semantic commits with proper organization  
**Use when:** Committing changes with professional, well-structured messages  
**Model:** Haiku

**Example prompts:**
- "Commit these changes"
- "Create semantic commits for this work"
- "Organize these changes into logical commits"

**What to expect:**
```
Created 3 commits:

1. feat(api): add user search endpoint
   If this commit is applied it will enable searching users by name/email
   • Implements fuzzy matching algorithm
   • Adds pagination support

2. test(api): add search endpoint tests
   If this commit is applied it will verify search functionality
   • Tests exact and fuzzy matches

3. docs(api): document search endpoint
   If this commit is applied it will add API documentation
```

**Features:**
- No AI attribution
- Atomic commit organization
- Semantic commit format
- Clear, professional messages

---

### GitHub Workflow

#### orchestrator
**Purpose:** Main coordinator for GitHub workflows and cross-agent automation  
**Use when:** You want natural language interaction with the full GitHub system  
**Model:** Sonnet

**Example prompts:**
- "What is happening in my GitHub?"
- "Commit my work and create a PR"
- "Show me what needs attention"
- "Clean up stale PRs"

**What it does:**
- Delegates to specialized agents
- Applies user preferences from config
- Coordinates multi-step workflows
- Provides structured summaries

---

#### context
**Purpose:** Repository activity scanning and stale work detection  
**Use when:** You need visibility into GitHub activity and priorities  
**Model:** Sonnet

**Example prompts:**
- "Scan my GitHub activity"
- "What repos have I been working on?"
- "Show me PRs that need my review"
- "What has been inactive lately?"

**What to expect:**
```
Active Work:
- repo-name (WORK): 12 commits this week, PR #45 open
- side-project (PERSONAL): 3 commits

Needs Attention:
- PR #67 awaiting your review (2 days)
- Issue #23 has new comments

Stale Items:
- PR #89: 14 days no activity
- Branch old-feature: 21 days stale
```

---

#### pr
**Purpose:** Create and manage pull requests  
**Use when:** Opening PRs, adding reviewers, linking issues  
**Model:** Haiku

**Example prompts:**
- "Create a pull request"
- "Make a PR that closes issue #45"
- "Add reviewers to PR #123"

**What to expect:**
```
## PR Created

Title: fix(auth): prevent session timeout on slow connections
URL: https://github.com/owner/repo/pull/234
Base: main ← bugfix/auth-timeout

### Description
Fixes authentication timeout issues...

### Labels Added
- bug
- priority:high
- area:auth

### Reviewers Requested
- @teammate1 (auth expert)
- @teammate2 (backend)
```

---

#### issue
**Purpose:** Create and manage GitHub issues  
**Use when:** Creating bug reports, feature requests, or tasks  
**Model:** Haiku

**Example prompts:**
- "Create a bug report for the login timeout"
- "Make a feature request issue"
- "Show me all open issues assigned to me"
- "Close issue #45 with a note"

**What to expect:**
```
## Issue Created

Title: Fix authentication timeout on slow connections
URL: https://github.com/owner/repo/issues/45
Type: Bug

### Description
## Bug Description
Users on slow connections experience timeouts...

## Steps to Reproduce
1. Throttle network to 3G
2. Attempt login
3. Observe timeout after 5 seconds

### Labels Applied
- bug
- priority:high
- area:auth
```

---

#### readme
**Purpose:** Generate comprehensive project documentation  
**Use when:** Creating or updating README files for repositories  
**Model:** Sonnet

**Example prompts:**
- "Create a README for this project"
- "Generate documentation for this codebase"
- "Update the README with installation instructions"

**What to expect:**
- Project overview and features
- Prerequisites and installation steps
- Usage examples (basic and advanced)
- Architecture explanation
- Configuration options
- Development setup
- Contributing guidelines
- Scannable, well-structured format

---

### Platform-Specific

#### conduit-compatibility
**Purpose:** Test and fix cross-platform compatibility in Conduit packages  
**Use when:** Testing on Windows or fixing platform-specific issues  
**Model:** Sonnet

**Example prompts:**
- "Test this Conduit package on Windows"
- "Fix cross-platform path issues"
- "Submit a PR to fix Windows compatibility"

**What it handles:**
- Cross-platform path issues
- Shell command differences
- PHP compatibility problems
- Windows-specific bugs

---

#### pest-migration (Laravel)
**Purpose:** Migrate PHPUnit tests to Pest format  
**Use when:** Converting Laravel test suites to use Pest  
**Model:** Sonnet

**Example prompts:**
- "Migrate these PHPUnit tests to Pest"
- "Convert this test class to Pest format"
- "Update Laravel test assertions to Pest syntax"

**What it handles:**
- Test class conversion
- Assertion syntax updates
- Laravel-specific testing patterns
- Maintains test coverage and behavior

---

### Workflow & Productivity

#### daily-standup
**Purpose:** Morning report of work items needing attention  
**Use when:** Starting your workday to prioritize tasks  
**Model:** Haiku

**Example prompts:**
- "What should I work on today?"
- "Give me my morning standup"
- "What needs my attention?"

**What to expect:**
```
## Your Daily Standup

### High Priority
- PR #123 awaiting your review (3 days)
- CI failing on main branch
- Issue #45 assigned to you (deadline today)

### Your Open PRs
- PR #156: 2 approvals, ready to merge
- PR #134: Awaiting review from @teammate

### Yesterday Activity
- 12 commits across 3 repos
- 2 PR reviews completed
- 1 issue closed

### Blockers
- None identified
```

---

## Customization

All agent behavior can be customized through the `user-config.md` file.

**Location:** `~/.claude/agents/user-config.md`

**What you can configure:**
- Commit message formats and styles
- Pull request templates
- Issue templates and label systems
- Staleness thresholds
- Repository categorization (WORK, PERSONAL, OSS)
- Priority signals and workflow preferences

**Changes take effect immediately** - no restart required.

### Configuration Examples

#### Commit Message Styles
- **Conventional Commits:** `feat(auth): add JWT token refresh`
- **Gitmoji:** `✨ feat(auth): add JWT token refresh`
- **Angular Convention:** Detailed commits with body and footer
- **Simple Descriptive:** Clear descriptions without prefixes
- **Issue-First:** `#234: Add JWT token refresh mechanism`

#### Repository Categories
```yaml
categories:
  WORK:
    rules:
      commit_style: angular
      require_pr: true
      require_reviews: 2

  PERSONAL:
    rules:
      commit_style: semantic
      allow_main_push: true

  OSS:
    rules:
      require_signoff: true
      follow_project_conventions: true
```

#### Staleness Rules
```yaml
staleness:
  pr_stale_days: 7
  pr_close_days: 30
  issue_stale_days: 30
  branch_stale_days: 14
  never_stale_labels:
    - "priority:critical"
    - "help-wanted"
```

---

## Usage Patterns

### Daily Workflow

**Morning:**
```
"Give me my morning standup"
```

**During work:**
```
"Commit these changes"
"Create a PR for this feature"
"Review PR #123"
```

**End of day:**
```
"What is still open in my GitHub?"
```

---

### Feature Development Flow

```
"I finished the authentication feature - commit it and create a PR"
```

**What happens:**
1. `commit-manager` creates semantic commits
2. `pr` agent creates pull request
3. Returns complete summary

---

## Tips for Best Results

1. **Be specific about context** - "Review PR #123" vs "Review this PR"
2. **Use agent names for control** - "Use the troubleshoot agent to debug this error"
3. **Leverage the orchestrator** - "What is happening in my GitHub?"
4. **Configure once** - Set up `user-config.md` with your preferences
5. **Check standup daily** - Start each day with "Give me my morning standup"

---

## Contributing

When adding new agents:

1. Place in appropriate category folder
2. Include clear frontmatter with description
3. Specify appropriate model (Haiku/Sonnet)
4. Update Quick Reference table
5. Add usage examples

---

## License

[Specify your license here]

## Support

For issues, questions, or contributions, please [specify your support channels].
