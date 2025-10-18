# Claude Code Commands

This directory contains custom slash commands for Claude Code to streamline development workflows.

## 📚 Full Workflow Guide

See [WORKFLOW.md](WORKFLOW.md) for a complete guide on how to use these commands together, including:
- Decision tree for choosing the right workflow
- Small changes vs. large features
- Real-world examples and best practices

## Command Overview

**Ideation & Exploration:**
- `/brainstorm` - Interactively refine vague ideas through guided questions

**Requirements & Planning:**
- `/create-prd` - Generate Product Requirements Documents from feature prompts
- `/create-erd` - Generate Engineering Requirements Documents from feature descriptions
- `/generate-tasks` - Create detailed task lists from PRDs/ERDs

**Implementation:**
- `/task-orchestrator` - Automated implementation with AI agents and review cycles
- `/process-task-list` - Execute task lists with proper workflow management

**Workspace Management:**
- `/worktree-setup` - Create isolated git worktree for large features
- `/worktree-finish` - Clean up completed worktree after merge

**Git & Commits:**
- `/smart-commit` - Intelligently analyze changes and create focused commits
- `/create-pr` - Create pull request from current branch (defaults to draft)

**Documentation:**
- `/update-docs` - Review recent commits and propose documentation updates

## Available Commands

### Ideation & Exploration

#### `/brainstorm`

Interactively refine a vague feature idea through guided questions and chunked exploration.

**What it does:**
1. Asks clarifying questions to understand your concept
2. Explores the idea in small, digestible 200-300 word sections
3. Seeks confirmation after each section before proceeding
4. Builds a comprehensive understanding suitable for requirements documentation

**Usage:**
```bash
# Start with a vague or unclear idea
/brainstorm

# Then describe your idea
# Example: "I want some kind of dashboard for users..."
```

**The command will:**
- ✅ Ask focused questions about problem, users, and goals
- ✅ Explore in chunks: Problem Space → Solution → UX → Technical → Edge Cases
- ✅ Pause for your confirmation after each section
- ✅ Allow you to adjust or dive deeper at any point
- ✅ Generate a final summary saved to `/tasks/brainstorm-[feature].md`
- ✅ Prepare you for `/create-prd` or `/create-erd`

**When to use:**
- Idea is vague or exploratory
- Requirements are unclear
- You're not sure exactly what to build yet
- Need to think through the problem space

---

### Requirements & Planning

#### `/create-erd`

Generate a detailed Engineering Requirements Document (ERD) from a feature description or brain dump.

**What it does:**
1. Analyzes your codebase to understand existing architecture
2. Asks clarifying questions about technical implementation
3. Creates a comprehensive ERD with engineering objectives, requirements, and implementation approach
4. Optionally saves locally to `/tasks/` or creates a Linear ticket

**Usage:**
```bash
# Describe your feature and let Claude guide you through the ERD creation
/create-erd

# Then describe your feature idea or paste your brain dump
```

**The command will:**
- ✅ Check the codebase for context
- ✅ Ask targeted questions about implementation, architecture, dependencies
- ✅ Generate a complete ERD with 12 sections (Overview, Engineering Objectives, User Stories, etc.)
- ✅ Save to `/tasks/erd-[feature-name].md` or create a Linear ticket
- ✅ Provide a document suitable for both AI task generation and junior developers

---

#### `/create-prd`

Generate a detailed Product Requirements Document (PRD) from an initial feature prompt.

**What it does:**
1. Takes your feature idea or user request
2. Asks clarifying questions about goals, users, and functionality
3. Creates a comprehensive PRD focused on the "what" and "why"
4. Saves to `/tasks/prd-[feature-name].md`

**Usage:**
```bash
# Start with a feature idea
/create-prd

# Then describe what you want to build
```

**The command will:**
- ✅ Ask questions about problem, target users, core functionality
- ✅ Explore user stories, acceptance criteria, and edge cases
- ✅ Generate a complete PRD with 9 sections (Overview, Goals, User Stories, etc.)
- ✅ Write for junior developers - clear, explicit, no jargon
- ✅ Define success metrics and non-goals

---

#### `/generate-tasks`

Create a detailed, step-by-step task list from a Requirements Document (PRD or ERD).

**What it does:**
1. Reads an existing PRD or ERD file
2. Generates high-level parent tasks and waits for approval
3. Breaks down each parent task into actionable sub-tasks
4. Identifies relevant files that need to be created or modified
5. Saves to `/tasks/tasks-[document-name].md`

**Usage:**
```bash
# Point to your requirements document
/generate-tasks

# Then specify the path to your PRD or ERD
# Example: /tasks/prd-user-profile-editing.md
```

**The command will:**
- ✅ Analyze the requirements document
- ✅ Generate ~5 high-level parent tasks
- ✅ Wait for your "Go" confirmation
- ✅ Create detailed sub-tasks for implementation
- ✅ List all relevant files with descriptions
- ✅ Format for junior developer implementation

**Example Output:**
```markdown
## Relevant Files
- `app/models/user.rb` - User model with profile fields
- `spec/models/user_spec.rb` - Unit tests for User model

## Tasks
- [ ] 1.0 Set up User model with profile fields
  - [ ] 1.1 Add migration for profile fields
  - [ ] 1.2 Add validations to User model
  - [ ] 1.3 Write model tests
```

---

### Implementation

#### `/task-orchestrator`

Orchestrate task implementation with specialized implementer and architect review agents.

**What it does:**
1. Reads a task list file
2. Identifies the next uncompleted task
3. Spawns an **Implementer Agent** to complete the task
4. Spawns a **Review Architect Agent** to review the implementation
5. Presents review feedback to you
6. Waits for your approval before proceeding
7. Runs `/smart-commit` to commit changes
8. Marks task complete and moves to the next task

**Usage:**
```bash
# Point to your task list
/task-orchestrator tasks/tasks-user-auth.md
```

**The command will:**
- ✅ Work through tasks sequentially
- ✅ Use specialized agents (implementer + reviewer) for each task
- ✅ Present implementation and review for your approval
- ✅ **Pause after each task** - you control the flow
- ✅ Create focused commits with `/smart-commit`
- ✅ Update task list completion status

**Workflow per task:**
1. Implementer completes the task
2. Architect reviews the work
3. You see both reports
4. You approve or request changes
5. Changes are committed
6. Move to next task

**When to use:**
- You want AI to implement with oversight
- You trust the task breakdown
- You have time to review each step
- You want quality checks built-in

---

#### `/process-task-list`

Executes task lists with automated implementation, requiring manual approval between each sub-task.

**What it does:**
1. Implements tasks one sub-task at a time with approval checks
2. Enforces proper completion protocol (test → stage → commit)
3. Maintains task list and relevant files section
4. Ensures clean git workflow

**Usage:**
```bash
# When you're ready to start implementing a task list
/process-task-list

# Claude will follow the task list workflow, asking permission between sub-tasks
```

**The command enforces:**
- ✅ One sub-task at a time (waits for your "yes" or "y")
- ✅ Mark sub-tasks `[x]` immediately upon completion
- ✅ When parent task complete: run tests → stage → clean up → commit
- ✅ Descriptive commit messages with task context
- ✅ Keep "Relevant Files" section updated
- ✅ No surprises - explicit permission required for each step

**Completion Protocol:**
```bash
# For each sub-task:
1. Implement the sub-task
2. Mark it [x] in the task list
3. Stop and ask permission

# When all sub-tasks done:
1. Run relevant tests
2. If tests pass: git add .
3. Clean up temporary files
4. Commit with descriptive message
5. Mark parent task [x]
```

---

### Workspace Management

#### `/worktree-setup`

Create an isolated git worktree with proper feature branch for large features.

**What it does:**
1. Asks for feature name and ticket reference
2. Creates feature branch following naming convention
3. Sets up worktree in `.worktrees/` directory
4. Ensures `.worktrees/` is gitignored
5. Provides next steps and workflow guidance

**Usage:**
```bash
# Start a new large feature in isolation
/worktree-setup
```

**The command will:**
- ✅ Create worktree at `<repo>/.worktrees/<branch-name>/`
- ✅ Use proper branch naming: `<TICKET>-jdw-claude-<feature>` or `jdw-claude-<feature>`
- ✅ Automatically add `.worktrees/` to `.gitignore`
- ✅ Guide you to next steps (`/brainstorm`, `/create-prd`, etc.)

**When to use:**
- Large/complex features (multi-day work)
- Want isolation from main codebase
- Need to work on multiple things in parallel
- Giving AI agent a sandbox

**Benefits:**
- Separate workspace for big features
- No branch switching needed
- Resume work by just `cd`-ing to directory
- Main repo stays clean

---

#### `/worktree-finish`

Clean up a completed worktree after feature is merged.

**What it does:**
1. Detects if you're in a worktree
2. Checks branch and PR status (merged/open/closed)
3. Warns about uncommitted changes or unpushed commits
4. Asks for confirmation
5. Removes worktree and deletes local branch
6. Returns you to main repo on main branch

**Usage:**
```bash
# After your PR is merged
/worktree-finish
```

**The command will:**
- ✅ Verify PR is merged (using `gh` if available)
- ✅ Warn about uncommitted/unpushed changes
- ✅ Navigate back to main repo
- ✅ Update main branch
- ✅ Remove worktree directory
- ✅ Delete local feature branch
- ✅ Confirm success

**Safety features:**
- ⚠️ Warns if PR not merged
- ⚠️ Warns if uncommitted changes exist
- ⚠️ Warns if unpushed commits exist
- ⚠️ Requires explicit confirmation

---

### Git & Commits

#### `/smart-commit`

Intelligently analyzes your changes and creates focused, meaningful commits.

**What it does:**
1. Checks if you're on `main` - if so, automatically creates a feature branch
2. Analyzes all staged and unstaged changes
3. Groups changes by topic/feature
4. Creates separate commits for each logical change
5. Writes meaningful commit messages following git guidelines

**Usage:**
```bash
# After making changes, simply run:
/smart-commit
```

**The command will:**
- ✅ Automatically create feature branch if on main
  - With ticket: `<TICKET>-jdw-claude-<feature>` (e.g., `MDZ-10-jdw-claude-add-comments`)
  - Without ticket: `jdw-claude-<feature>` (e.g., `jdw-claude-refactor-orders`)
- ✅ Analyze git diff to understand what changed
- ✅ Split unrelated changes into separate commits
- ✅ Write descriptive commit messages (no "feat:", "fix:" prefixes)
- ✅ Follow imperative mood ("Add" not "Added")
- ✅ Show final git status

**Example Scenarios:**

**Scenario 1: Single feature**
```bash
# You modified: app/models/user.rb, app/controllers/users_controller.rb
/smart-commit
# Result: 1 commit - "Add user authentication with Devise"
```

**Scenario 2: Multiple features**
```bash
# You modified:
# - app/models/comment.rb (new feature)
# - app/controllers/comments_controller.rb (new feature)
# - app/models/article.rb (bug fix)
/smart-commit
# Result: 2 commits
#   1. "Add Comment model and controller"
#   2. "Fix article validation for published status"
```

**Scenario 3: On main branch**
```bash
# Current branch: main
# With ticket in context (e.g., working on MDZ-10)
/smart-commit
# Result:
#   1. Creates branch "MDZ-10-jdw-claude-add-comments"
#   2. Commits changes with proper messages

# Without ticket
/smart-commit
# Result:
#   1. Creates branch "jdw-claude-add-comments"
#   2. Commits changes with proper messages
```

---

#### `/create-pr`

Create a pull request from the current branch, defaulting to draft mode.

**What it does:**
1. Analyzes all changes since the branch diverged from the base branch
2. Examines all commits (not just the latest) to understand the complete feature
3. Creates a descriptive PR title and summary
4. Pushes to remote if needed
5. Creates a draft PR with `gh pr create --draft`
6. Returns the PR URL

**Usage:**
```bash
# Create a draft PR (default)
/create-pr

# Create a ready PR
/create-pr --ready

# Specify a different base branch
/create-pr --base develop
```

**The command will:**
- ✅ Analyze ALL commits in the branch (not just the latest)
- ✅ Create comprehensive PR summary based on all changes
- ✅ Include a test plan checklist
- ✅ Push to remote with `-u` flag if needed
- ✅ Create PR in draft mode by default
- ✅ Return the PR URL for easy access

**PR Format:**
```markdown
## Summary
- Bullet point 1 covering key changes
- Bullet point 2 covering key changes
- Bullet point 3 covering key changes

## Test plan
- [ ] Test item 1
- [ ] Test item 2
- [ ] Test item 3

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

**Options:**
- `--ready` or `--no-draft` - Create PR in ready state instead of draft
- `--base <branch>` - Specify base branch (defaults to main)

**When to use:**
- After completing work on a feature branch
- When changes are ready for review (or close to ready)
- To quickly create a draft PR for early feedback

**Safety features:**
- ⚠️ Always analyzes complete branch history
- ⚠️ Pushes to remote if not already pushed
- ⚠️ Defaults to draft to prevent accidental ready PRs

---

### Documentation

#### `/update-docs`

Review recent commits and propose documentation updates for outdated or missing content. Works generically across any repository.

**What it does:**
1. **Discovers documentation structure** - Finds README, CLAUDE.md, AGENTS.md, docs/, nested READMEs
2. Analyzes recent git commits (default: last 20)
3. Identifies new features, APIs, dependencies, or structural changes
4. Cross-references changes against discovered documentation
5. **Proposes specific updates** with file names and line numbers
6. On first run, can propose adding a "Documentation" index to README
7. Waits for your approval
8. Makes approved changes

**Usage:**
```bash
# Review last 20 commits (default)
/update-docs

# Review last 50 commits
/update-docs 50

# Review last 10 commits
/update-docs 10
```

**The command will:**
- ✅ Automatically discover all documentation files in any repo
- ✅ Analyze git history to understand what changed
- ✅ Check documentation files for gaps and outdated info
- ✅ Identify missing features, APIs, new dependencies, structural changes
- ✅ **Propose specific updates** before making any changes
- ✅ Show file names, line numbers, and what needs updating
- ✅ Wait for explicit approval (y/n)
- ✅ Only make changes after you approve

**Files it discovers and checks:**
- `README.md` or `README` - Main repository documentation
- `CLAUDE.md` - Project-specific AI instructions (if present)
- `AGENTS.md` - Core agent guidelines (if present)
- `docs/` or `documentation/` directories - All markdown files
- Nested READMEs in subdirectories (e.g., `src/components/README.md`)
- Common files: `CONTRIBUTING.md`, `CHANGELOG.md`, `ARCHITECTURE.md`, `API.md`

**Example Output:**
```markdown
## Documentation Updates Needed

### README.md

**Section: Installation (Line 15-30)**
- Issue: Still references Node v16, project now requires Node v20
- Proposed change: Update minimum Node version to v20.x
- Reason: Commit abc123 updated package.json engines field

**Section: API Reference (Line 85-120)**
- Issue: Missing new /api/v2/users endpoint
- Proposed change: Add documentation for new users API endpoint
- Reason: Commits def456, ghi789 added user management API

### docs/architecture.md

**Section: Data Layer (Line 45-60)**
- Issue: No mention of Redis caching layer
- Proposed change: Document Redis integration and caching strategy
- Reason: Commit jkl012 added Redis for session/query caching

### NEW: Proposed Documentation Section

**File: README.md**
**Location: After "Installation" section**
- Proposal: Add "Documentation" section listing all discovered docs
- Reason: No central documentation index currently exists

### Summary
- Total files needing updates: 3
- New features to document: 2
- Outdated sections to fix: 2
- New sections to add: 1

Would you like me to proceed with these updates? (y/n)
```

**When to use:**
- Before landing a PR (catch documentation drift)
- After adding new features or commands
- When you suspect docs are out of sync with code
- After major refactoring or restructuring
- Periodically to maintain documentation quality

**Safety features:**
- ⚠️ Never makes changes without approval
- ⚠️ Shows exactly what will change
- ⚠️ Preserves formatting and style
- ⚠️ Minimal, focused updates only

---

## Installing Commands

### Project-Level (This Repository)
Commands in this directory are specific to this project.

**To use in other projects:**
```bash
# Copy to your project
cp -r commands /path/to/your/project/.claude/commands
```

### Global (All Projects)
**To use across all projects:**
```bash
# Copy to global commands directory
cp commands/*.md ~/.claude/commands/
```

## Creating Your Own Commands

Create a new `.md` file in this directory (or `~/.claude/commands/` for global commands).

**Basic structure:**
```markdown
---
description: Brief description of what this command does
allowed-tools: Bash, Read, Edit, Write
---

Your command instructions here.

You can use:
- $ARGUMENTS for all arguments
- $1, $2, etc. for specific arguments
- Bash commands with !git status
- File references with @filename
```

**Example: Simple command**
```markdown
---
description: Run tests and show results
---

Run the test suite and show any failures:

!rspec

If there are failures, explain what might be causing them.
```

## Command Best Practices

1. **Be specific**: Clearly define what the command should do
2. **Include examples**: Show expected behavior
3. **Handle edge cases**: Think about what could go wrong
4. **Follow guidelines**: Reference repository guidelines when appropriate
5. **Test thoroughly**: Try the command in different scenarios

## Available Tools

Commands can use these tools (specify in frontmatter):
- `Bash` - Execute shell commands
- `Read` - Read file contents
- `Write` - Create new files
- `Edit` - Modify existing files
- `Grep` - Search in files
- `Glob` - Find files by pattern

## Tips

- Use `!` prefix for bash commands: `!git status`
- Use `@` for file references: `@app/models/user.rb`
- Break complex commands into steps
- Provide clear success/failure feedback
- Reference other guidelines when relevant

## More Command Ideas

Here are additional commands you might want to create:

- `/review-pr [pr-number]` - Review a pull request for code quality and issues
- `/write-tests [file-path]` - Generate tests for untested code
- `/check-security` - Scan for security vulnerabilities
- `/optimize-queries` - Find and fix N+1 queries in Rails
- `/pre-push` - Run all pre-push checks (tests, linter, etc.)
- `/refactor [file-path]` - Refactor code for better readability and maintainability
- `/add-feature` - Shortcut to run `/create-erd`, `/generate-tasks`, and `/process-task-list` in sequence

## Resources

- [Claude Code Slash Commands Documentation](https://docs.claude.com/en/docs/claude-code/slash-commands.md)
- [Git Workflow Guidelines](../guidelines/git-workflow.md)
- [AGENTS.md](../AGENTS.md) - Core agent instructions
