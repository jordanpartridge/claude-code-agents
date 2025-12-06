# GitHub Orchestration Agents

A comprehensive agent system for managing GitHub workflows through Claude Code. This collection provides intelligent automation for commits, pull requests, issues, and cross-repository activity tracking.

## Architecture

The system follows an orchestrator pattern where a main agent delegates to specialized sub-agents:

```
                    ┌─────────────────────────┐
                    │  github-orchestrator    │
                    │  (Main Coordinator)     │
                    └───────────┬─────────────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
                ▼               ▼               ▼
    ┌──────────────────┐  ┌──────────────┐  ┌─────────────────┐
    │ github-context   │  │ git-commit   │  │   github-pr     │
    │ (Scan & Track)   │  │  -manager    │  │ (PRs & Reviews) │
    └──────────────────┘  │ (Commits)    │  └─────────────────┘
                          └──────────────┘
                                              ┌─────────────────┐
                                              │  github-issue   │
                                              │ (Issue Mgmt)    │
                                              └─────────────────┘

                                              ┌─────────────────┐
                                              │ github-readme   │
                                              │ (Docs & READMEs)│
                                              └─────────────────┘
```

### How It Works

1. **github-orchestrator** receives your high-level requests
2. It analyzes what needs to be done and delegates to specialized agents
3. Each sub-agent performs its specific task using best practices
4. Results are synthesized and reported back to you

## Agents Overview

### github-orchestrator
**The brain of the system**

- Coordinates all GitHub operations
- Applies user preferences and categorization rules
- Delegates to specialized agents
- Provides daily summaries and activity reports
- Manages staleness detection and cleanup

**Model:** Sonnet

### github-context
**The scout**

- Scans repositories for recent activity
- Detects stale PRs, issues, and branches
- Tracks what needs attention
- Provides cross-repo activity summaries
- Identifies priority work items

**Model:** Sonnet

### git-commit-manager
**The version control specialist**

- Creates atomic, logical commits
- Writes semantic commit messages
- Organizes changes intelligently
- Follows strict commit message conventions
- Never mentions AI in commits

**Model:** Haiku

### github-pr
**The pull request expert**

- Creates PRs with proper descriptions
- Auto-generates titles from commits
- Requests appropriate reviewers
- Adds relevant labels
- Links related issues
- Manages PR lifecycle

**Model:** Haiku

### github-issue
**The issue tracker**

- Creates well-formatted issues
- Applies appropriate templates (bug/feature/task)
- Manages labels and assignments
- Handles stale issue cleanup
- Links issues to PRs

**Model:** Haiku

### github-readme
**The documentation specialist**

- Analyzes codebases for documentation needs
- Creates comprehensive, approachable READMEs
- Follows documentation best practices
- Ensures proper project structure explanation
- Makes technical content accessible

**Model:** Sonnet

## Installation

1. Place all agent files in your Claude Code agents directory:

```bash
# Linux/Mac
~/.claude/agents/

# Windows
C:\Users\<username>\.claude\agents\
```

2. Ensure you have the `gh` CLI installed and authenticated:

```bash
gh auth login
```

3. Restart Claude Code or reload agents

## Usage Examples

### Daily GitHub Check
```
"What's happening in my GitHub projects?"
"Show me what needs my attention"
```
Triggers: **github-orchestrator** → delegates to **github-context**

### Commit Your Work
```
"Commit my changes"
"Create semantic commits for these changes"
```
Triggers: **github-orchestrator** → delegates to **git-commit-manager**

### Create a Pull Request
```
"Create a PR for this branch"
"Open a pull request with proper description"
```
Triggers: **github-orchestrator** → delegates to **github-pr**

### Issue Management
```
"Create a bug report for the authentication timeout"
"Show me all open issues assigned to me"
```
Triggers: **github-orchestrator** → delegates to **github-issue**

### Create Documentation
```
"Create a README for this project"
"Update the documentation to explain the architecture"
```
Triggers: **github-orchestrator** → delegates to **github-readme**

### Cleanup Stale Work
```
"Show me stale PRs and branches"
"What work has been inactive for a while?"
```
Triggers: **github-orchestrator** → delegates to **github-context**

## Workflow Examples

### Feature Development Flow

1. **Start Work**
   ```
   "What should I work on today?"
   → github-context scans and prioritizes
   ```

2. **Make Changes**
   (You code your feature)

3. **Commit**
   ```
   "Commit these changes"
   → git-commit-manager creates atomic commits
   ```

4. **Create PR**
   ```
   "Create a PR for this feature"
   → github-pr builds description from commits
   ```

5. **Track Progress**
   ```
   "What's the status of my PRs?"
   → github-context provides summary
   ```

### Bug Fix Flow

1. **Create Issue**
   ```
   "Create a bug report for the login timeout"
   → github-issue uses bug template
   ```

2. **Fix Bug**
   (You implement the fix)

3. **Commit**
   ```
   "Commit this bug fix"
   → git-commit-manager creates fix commit
   ```

4. **Create PR with Issue Link**
   ```
   "Create a PR that closes issue #45"
   → github-pr links PR to issue
   ```

### Weekly Cleanup Flow

1. **Scan for Stale Work**
   ```
   "Show me stale PRs and branches"
   → github-context identifies stale items
   ```

2. **Review and Clean**
   ```
   "Close stale PR #89 and delete its branch"
   → github-pr handles closure
   ```

3. **Issue Triage**
   ```
   "Show me issues needing attention"
   → github-issue provides filtered list
   ```

## Configuration

### Repository Categories

The orchestrator categorizes repos as:
- **WORK**: Client projects (strict rules)
- **PERSONAL**: Side projects (relaxed)
- **OSS**: Open source contributions (follow project conventions)
- **ARCHIVED**: Inactive projects

### Staleness Rules

- **PR stale**: No activity > 7 days
- **Issue stale**: No activity > 30 days
- **Branch stale**: No commits > 14 days
- **Repo inactive**: No pushes > 90 days

### Commit Style

All commits use semantic format:
```
<type>(<scope>): <description>

If this commit is applied it will <what it does>

• Context point 1
• Context point 2
```

Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `build`, `ci`

## Benefits

### For You
- Consistent commit history across all repos
- Never forget to link PRs to issues
- Automatic staleness detection
- Clear visibility into all GitHub activity
- Reduced mental overhead for git operations

### For Your Team
- Professional, semantic commit messages
- Well-documented PRs with proper context
- Organized issue tracking
- Easier code review process
- Better project history

### For Your Projects
- Clean git history
- Comprehensive documentation
- Reduced technical debt
- Better organization
- Easier onboarding for contributors

## Best Practices

1. **Let the Orchestrator Decide**: Start with high-level requests to the orchestrator
2. **Trust the Specialists**: Each agent is optimized for its specific task
3. **Review Before Push**: Always review commits and PRs before pushing
4. **Maintain Categories**: Keep your repo categorization updated
5. **Regular Cleanups**: Run weekly staleness checks

## Troubleshooting

### Commits Not Created
- Ensure git-commit-manager is being delegated to (not direct commits)
- Check that you have unstaged changes
- Verify git is initialized in the directory

### PRs Failing to Create
- Ensure `gh` CLI is authenticated
- Check that your branch has commits
- Verify remote repository exists

### Context Not Showing Activity
- Confirm `gh auth status` shows authenticated user
- Check that repos are accessible to your account
- Verify repositories have recent activity

## Contributing

To extend these agents:

1. Follow the existing agent structure with frontmatter
2. Use appropriate model (Sonnet for complex, Haiku for focused tasks)
3. Document trigger patterns in the description
4. Keep agents focused on single responsibilities
5. Use the orchestrator for coordination

## License

These agents are part of your personal Claude Code configuration. Use and modify as needed.

---

**Happy automating!** Let the agents handle the git busywork while you focus on building great software.
