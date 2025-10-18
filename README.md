# Claude Code Configuration Repository

A centralized collection of instructions, guidelines, and custom slash commands for AI coding agents, specifically optimized for Claude Code. This repository provides structured workflows for feature development from ideation through production.

## Purpose

This repository provides:
- **AGENTS.md** - Core agent workflow and instructions following the [AGENTS.md standard](https://github.com/openai/agents.md)
- **Custom Slash Commands** - Complete workflow commands for Claude Code (brainstorming, requirements, task orchestration, git workflows)
- **Specialized Agent Roles** - Focused implementer and review-architect agents for structured development
- **Development Guidelines** - Language-specific conventions, testing practices, and git workflows

## Repository Structure

```
claude-config/
├── README.md                      # This file - complete guide and workflows
├── CLAUDE.md                      # Repository-specific Claude Code instructions
├── AGENTS.md                      # Core agent guidelines (use in project roots)
├── agents/                        # Specialized agent role definitions
│   ├── implementer.md            # Task execution agent (no scope creep)
│   └── review-architect.md       # Code review and quality assurance agent
├── commands/                      # Custom Claude Code slash commands
│   ├── brainstorm.md             # Interactive idea refinement
│   ├── create-prd.md             # Product requirements generation
│   ├── create-erd.md             # Engineering requirements generation
│   ├── generate-tasks.md         # Task breakdown from requirements
│   ├── task-orchestrator.md      # Automated task execution with AI agents
│   ├── process-task-list.md      # Manual task list workflow guidance
│   ├── smart-commit.md           # Intelligent commit creation
│   ├── create-pr.md              # Draft PR creation
│   └── update-docs.md            # Documentation sync from git history
└── guidelines/                    # Language-specific development guidelines
    ├── git-workflow.md           # Branch naming, commits, PRs, rebasing
    ├── ruby-rails.md             # Rails conventions and best practices
    ├── javascript.md             # JS/TS, React, Hotwire/Stimulus guidelines
    └── testing.md                # RSpec, Jest, FactoryBot, mocking strategies
```

## Quick Start

### For Claude Code Users

**Global Setup (Recommended):**
```bash
# Reference this repo in your global Claude Code config
echo "For all projects, follow guidelines in ~/Code/claude-config/AGENTS.md" >> ~/.claude/CLAUDE.md
```

**Project-Specific Setup:**
```bash
# Copy commands to use in a specific project
cp -r ~/Code/claude-config/commands /path/to/project/.claude/

# Copy AGENTS.md to project root for project-specific context
cp ~/Code/claude-config/AGENTS.md /path/to/project/
```

### For AI Agents Reading This

1. **Read [AGENTS.md](AGENTS.md) first** - Contains core workflow instructions and philosophy
2. **Check [CLAUDE.md](CLAUDE.md)** - Repository-specific architecture and conventions
3. **Reference the Workflows section below** - Complete workflow decision trees and examples
4. **Use language-specific guidelines** in `guidelines/` as needed

## Core Components

### AGENTS.md - Universal Agent Guidelines

The [AGENTS.md](AGENTS.md) file follows the [AGENTS.md standard](https://github.com/openai/agents.md) proposed by OpenAI. It contains:

- **Core Philosophy** - YAGNI, convention over configuration, clarity over cleverness
- **Agent Workflow** - Starting work, during development, pre-completion checklist
- **Git Workflow** - Branch naming (`<TICKET>-claude-<feature>`), commit practices
- **Language-Specific Guides** - Links to Rails, JavaScript, and testing guidelines
- **Tools & Integrations** - Context7, Linear, GitHub CLI usage patterns
- **Learning & Documentation** - Knowledge capture and decision records

**Usage:** Place in project roots or reference globally via `~/.claude/CLAUDE.md`

### Complete Development Workflows

This repository provides end-to-end workflows for feature development:

**Ideation → Requirements → Implementation → Review → Merge**

```
/brainstorm (vague idea)
    ↓
/create-prd or /create-erd (clear requirements)
    ↓
/generate-tasks (task breakdown)
    ↓
/task-orchestrator (AI implements) or /process-task-list (manual)
    ↓
/smart-commit (focused commits)
    ↓
/create-pr (pull request)
    ↓
/update-docs (keep docs in sync)
```

See the **Workflows** section below for complete workflow guide with decision trees.

## Specialized Agents

This repository includes specialized agent role definitions in the [`agents/`](agents/) directory. These agents are designed to work together in structured workflows.

### Implementer Agent ([agents/implementer.md](agents/implementer.md))

A focused task execution agent that follows specifications precisely without scope creep.

**Key characteristics:**
- Executes tasks step-by-step as specified
- Does not add features beyond the scope
- Does not refactor code unless required
- Provides clear completion reports
- Focuses on implementation, not exploration

**When to use:**
- Implementing specific tasks from a task list
- Following detailed specifications
- Working within a larger orchestrated workflow

### Review Architect Agent ([agents/review-architect.md](agents/review-architect.md))

A quality assurance agent that provides critical oversight and code review.

**Key characteristics:**
- Reviews implementations with a fresh perspective
- Checks for correctness, quality, and best practices
- Provides constructive, actionable feedback
- Makes recommendations (APPROVE, REQUEST CHANGES, NEEDS DISCUSSION)
- Focuses on requirements alignment

**When to use:**
- Reviewing completed implementations
- Ensuring quality before merging
- Catching edge cases and potential bugs
- Working within a larger orchestrated workflow

## Custom Slash Commands

This repository includes 10+ custom Claude Code slash commands. See [commands/README.md](commands/README.md) for complete documentation.

### Command Categories

**Ideation & Exploration:**
- `/brainstorm` - Interactively refine vague ideas through guided questions

**Requirements & Planning:**
- `/create-prd` - Generate Product Requirements Documents
- `/create-erd` - Generate Engineering Requirements Documents
- `/generate-tasks` - Create detailed task breakdowns from requirements

**Implementation:**
- `/task-orchestrator` - Automated task execution with implementer + reviewer agents
- `/process-task-list` - Manual task list workflow with approval gates

**Git & Commits:**
- `/smart-commit` - Intelligently analyze changes and create focused commits
- `/create-pr` - Create draft pull requests with comprehensive summaries

**Documentation:**
- `/update-docs` - Review git history and propose documentation updates

### Key Command: `/task-orchestrator`

Coordinates structured task implementation using specialized agents:

```bash
/task-orchestrator tasks/tasks-feature-name.md
```

**Process:**
1. Reads task list, finds next uncompleted task
2. Spawns **Implementer Agent** to execute task (no scope creep)
3. Spawns **Review Architect Agent** to review implementation
4. Presents both reports to you
5. **Pauses for your approval** before proceeding
6. Runs `/smart-commit` to commit changes
7. Marks task complete, moves to next task

**Benefits:** Systematic execution, built-in code review, human-in-the-loop control

### Key Command: `/smart-commit`

Analyzes all changes and creates focused, logical commits:

```bash
/smart-commit
```

**Features:**
- Auto-creates feature branch if on main
- Groups related changes by topic
- Splits unrelated changes into separate commits
- Writes clear commit messages (no "feat:" prefixes)
- Follows git-workflow.md conventions

## Guidelines Overview

### Ruby on Rails ([guidelines/ruby-rails.md](guidelines/ruby-rails.md))

Comprehensive Rails conventions covering:
- Code structure (controllers, models, services, concerns)
- Routing and RESTful design
- Database migrations and ActiveRecord queries
- Testing with RSpec
- Security best practices
- Performance optimization
- Code style with RuboCop

### JavaScript ([guidelines/javascript.md](guidelines/javascript.md))

JavaScript best practices including:
- Framework detection (React, Vue, Hotwire/Stimulus)
- Modern vanilla JavaScript patterns
- Hotwire/Stimulus for Rails applications
- React component patterns (if applicable)
- TypeScript guidelines (when needed)
- Security (XSS prevention, CSRF tokens)
- Performance optimization

### Testing ([guidelines/testing.md](guidelines/testing.md))

Complete testing guide with:
- Test categories (unit, integration, system/feature)
- RSpec setup and configuration
- Model, controller, and request specs
- System tests with Capybara
- JavaScript testing (Jest, React Testing Library)
- Test data management with FactoryBot
- Mocking and stubbing strategies
- Performance optimization for test suites

### Git Workflow ([guidelines/git-workflow.md](guidelines/git-workflow.md))

Git best practices covering:
- Branch management and naming conventions
- Commit message guidelines
- Pre-commit requirements (linting, tests, whitespace)
- Pull request workflows
- Rebasing and merging strategies
- Useful git commands and aliases
- Emergency procedures

## Integration Patterns

### Global Setup (All Projects)

Add to `~/.claude/CLAUDE.md`:

```markdown
# Global Agent Instructions

For all projects, follow the guidelines in ~/Code/claude-config/AGENTS.md

When working on specific technologies, reference:
- Ruby/Rails: ~/Code/claude-config/guidelines/ruby-rails.md
- JavaScript: ~/Code/claude-config/guidelines/javascript.md
- Testing: ~/Code/claude-config/guidelines/testing.md
- Git workflow: ~/Code/claude-config/guidelines/git-workflow.md
```

### Project-Specific Setup

**Option 1: Reference globally**
```markdown
# my-project/AGENTS.md

## General Guidelines
Follow universal guidelines: ~/Code/claude-config/AGENTS.md

## Project-Specific
- Tech: Rails 7 + Hotwire + PostgreSQL
- Testing: RSpec with FactoryBot
- Deployment: Heroku
```

**Option 2: Copy commands locally**
```bash
# Use slash commands in specific project
cp -r ~/Code/claude-config/commands /path/to/project/.claude/
cp ~/Code/claude-config/agents/* /path/to/project/.claude/agents/
```

## Customization & Extension

### For Teams

Fork this repository and customize:

1. **Modify guidelines** - Update language-specific conventions in `guidelines/`
2. **Add new commands** - Create custom slash commands in `commands/`
3. **Adjust workflows** - Update `commands/WORKFLOW.md` for your process
4. **Extend agent roles** - Add specialized agents in `agents/`

### Adding New Commands

Create a new `.md` file in `commands/` (or `~/.claude/commands/` for global use):

```markdown
---
description: Brief description of what this command does
allowed-tools: Bash, Read, Edit, Write, Task, SlashCommand
---

Your command instructions here.

You can use:
- $ARGUMENTS for all arguments passed to the command
- $1, $2, etc. for specific positional arguments
- Reference other files with full paths
- Call other slash commands with SlashCommand tool
```

**Simple Example:**
```markdown
---
description: Run tests and show results
allowed-tools: Bash
---

Run the test suite and show any failures.

Execute the test command appropriate for this project and explain any failures.
```

**Available Tools:**
- `Bash` - Execute shell commands
- `Read` - Read file contents
- `Write` - Create new files
- `Edit` - Modify existing files
- `Grep` - Search in files
- `Glob` - Find files by pattern
- `Task` - Spawn sub-agents for complex work
- `SlashCommand` - Execute other slash commands

**Best Practices:**
1. **Be specific** - Clearly define expected behavior
2. **Include examples** - Show what success looks like
3. **Handle edge cases** - Think about error conditions
4. **Reference guidelines** - Link to AGENTS.md or guidelines when relevant
5. **Test thoroughly** - Try in different scenarios before committing

### Adding New Guidelines

Create a new `.md` file in `guidelines/`:

```markdown
# Language/Framework Name Guidelines

## Core Principles
[Fundamental patterns]

## Code Structure
[Conventions and patterns]

## Common Patterns
[Examples with code]

## Anti-Patterns
[What to avoid]
```

Update `AGENTS.md` to reference the new guideline.

## Why This Repository Exists

AI coding agents are powerful, but they work best with structured guidance. This repository provides:

- **Systematic Workflows** - From vague idea to production-ready code with clear steps
- **Quality Gates** - Built-in review processes prevent scope creep and bugs
- **Human Control** - Approval gates ensure AI never acts fully autonomously
- **Consistency** - All agents follow the same conventions across projects
- **Knowledge Capture** - Document decisions, patterns, and learnings as you work
- **Rapid Onboarding** - New team members (human or AI) understand your workflow immediately

### Design Philosophy

1. **Systematic over ad-hoc** - Repeatable workflows beat one-off solutions
2. **Human oversight** - AI assists, humans decide
3. **Separation of concerns** - Implementer focuses on execution, architect on review
4. **Documentation-driven** - Requirements define tasks, tasks define code
5. **Convention over configuration** - Strong conventions reduce cognitive load

## Workflows

### Decision Tree

```
Start: I have an idea
│
├─── Is the idea clear and well-defined?
│    ├─── No → /brainstorm
│    └─── Yes → Continue
│
├─── Is this a small change or large feature?
│    ├─── Small → Quick Fix Workflow
│    └─── Large → Feature Development Workflow
│
└─── Implementation approach?
     ├─── AI-assisted → /task-orchestrator
     └─── Manual → /process-task-list
```

### Quick Fix Workflow

**For:** Bug fixes, small features, quick improvements

```bash
# 1. Create branch
git checkout -b claude-fix-validation

# 2. Make changes & tests

# 3. Commit
/smart-commit

# 4. Create PR
/create-pr

# 5. After merge
git checkout main && git pull
git branch -d claude-fix-validation
```

### Feature Development Workflow

**For:** Major features, complex changes, multi-day work

```bash
# 1. Refine idea (if vague)
/brainstorm

# 2. Create requirements
/create-prd  # or /create-erd

# 3. Generate tasks
/generate-tasks tasks/prd-feature-name.md

# 4. Implement
/task-orchestrator tasks/tasks-feature-name.md  # or /process-task-list

# 5. Create PR
/create-pr

# 6. Update docs
/update-docs

# 7. After merge
git checkout main && git pull
```

## Real-World Examples

### Example 1: Small Bug Fix

```bash
git checkout -b claude-fix-login-validation
# ... make fixes ...
/smart-commit
/create-pr
# ... after merge ...
git checkout main && git pull
git branch -d claude-fix-login-validation
```

**Time:** 30 minutes | **Complexity:** Low

### Example 2: Medium Feature (Clear Requirements)

```bash
git checkout -b claude-export-csv
/create-erd
/generate-tasks tasks/erd-export-csv.md
/process-task-list tasks/tasks-export-csv.md
/create-pr
/update-docs
# ... after merge ...
git checkout main && git pull
git branch -d claude-export-csv
```

**Time:** 1-2 days | **Complexity:** Medium

### Example 3: Complex Feature (Vague Idea)

```bash
git checkout -b claude-analytics
/brainstorm
/create-prd
/generate-tasks tasks/prd-analytics.md
/task-orchestrator tasks/tasks-analytics.md
/create-pr
/update-docs
# ... after merge ...
git checkout main && git pull
git branch -d claude-analytics
```

**Time:** 3-5 days | **Complexity:** High | **AI Assistance:** High

## Command Usage Tips

### Task List Format

Tasks are stored in `tasks/` directory:

```markdown
## Relevant Files
- `app/models/user.rb` - User model
- `spec/models/user_spec.rb` - Tests

## Tasks
- [ ] 1.0 Parent Task
  - [ ] 1.1 Subtask description
  - [ ] 1.2 Subtask description
- [x] 2.0 Completed Task
```

### Generated File Locations

- `tasks/brainstorm-*.md` - Brainstorm outputs
- `tasks/prd-*.md` - Product requirements
- `tasks/erd-*.md` - Engineering requirements
- `tasks/tasks-*.md` - Task breakdowns

### Command Chaining

Commands are designed to chain together:

```bash
/create-erd                                    # → tasks/erd-feature.md
/generate-tasks tasks/erd-feature.md          # → tasks/tasks-feature.md
/task-orchestrator tasks/tasks-feature.md     # → implements & commits
```

## References & Resources

- **[AGENTS.md Standard](https://github.com/openai/agents.md)** - Original specification from OpenAI
- **[Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)** - Official Claude Code docs
- **Individual command files** in `commands/` - Detailed command documentation

---

**Remember**: This is a living configuration repository. Update guidelines, commands, and workflows as your practices evolve. Prioritize clarity and maintainability over cleverness.
