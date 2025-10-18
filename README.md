# AI Agents Repository

A centralized collection of instructions, guidelines, procedures, and configurations for AI coding agents. This repository serves as a comprehensive guide for AI agents (like Claude, GitHub Copilot, Cursor, etc.) when working on software development projects.

## Purpose

This repository provides:
- **AGENTS.md** - Core agent workflow and instructions following the [AGENTS.md standard](https://github.com/openai/agents.md)
- **Detailed Guidelines** - Language-specific conventions, testing practices, and git workflows
- **Reusable Configurations** - Agent configs, slash commands, and guardrails for consistent AI-assisted development

## Repository Structure

```
ai-config/
├── README.md                      # This file
├── AGENTS.md                      # Core agent instructions
├── agents/                        # Specialized agent role definitions
│   ├── implementer.md            # Focused task execution agent
│   └── review-architect.md       # Quality assurance and review agent
├── commands/                      # Custom Claude Code slash commands
│   ├── smart-commit.md           # Intelligent commit creation command
│   ├── task-orchestrator.md      # Task workflow orchestration command
│   └── README.md                 # Commands documentation
└── guidelines/
    ├── ruby-rails.md             # Ruby on Rails conventions and best practices
    ├── javascript.md             # JavaScript/TypeScript guidelines
    ├── testing.md                # Comprehensive testing guide (RSpec, Jest, etc.)
    └── git-workflow.md           # Git workflow and commit standards
```

## Quick Start

### For AI Agents

1. **Read [AGENTS.md](../AGENTS.md) first** - This contains core workflow instructions
2. **Reference language-specific guidelines** in the `guidelines/` directory as needed
3. **Follow the implementation checklist** before completing any work

### For Developers

1. Copy this repository structure to your projects
2. Customize the guidelines to match your team's conventions
3. Place `AGENTS.md` in your project root to guide AI assistants
4. Reference these files in your `.claude/CLAUDE.md` or similar AI configuration

## Using AGENTS.md

The [AGENTS.md](../AGENTS.md) file follows the standard proposed by OpenAI for providing context to AI coding agents. Place this file in the root of any project where you want AI agents to follow specific guidelines.

### What AGENTS.md Contains

- **Core Philosophy** - Fundamental principles for code quality
- **Agent Workflow** - Step-by-step process for starting, working, and completing tasks
- **Git Workflow** - Branching, commits, and PR guidelines
- **Development Server Guidelines** - How to run projects without breaking hot-reload
- **Security Guidelines** - Best practices for secure development
- **Implementation Checklist** - Pre-flight checks before completing work

### Linking to Detailed Guidelines

AGENTS.md references detailed guidelines for:
- **[Ruby on Rails](guidelines/ruby-rails.md)** - Models, controllers, services, routing, migrations
- **[JavaScript](guidelines/javascript.md)** - Vanilla JS, React, Hotwire/Stimulus, TypeScript
- **[Testing](guidelines/testing.md)** - RSpec, Jest, FactoryBot, mocking strategies
- **[Git Workflow](guidelines/git-workflow.md)** - Commits, branches, PRs, rebasing

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

## Custom Commands

This repository includes custom Claude Code slash commands in the [`commands/`](commands/) directory.

### `/smart-commit` - Intelligent Commit Creation

Automatically creates focused, well-structured commits by:
1. Checking if you're on main (creates feature branch if needed)
2. Analyzing all changes (staged and unstaged)
3. Grouping related changes by topic
4. Creating separate commits for each logical change
5. Writing meaningful commit messages following guidelines

**Usage:**
```bash
# After making changes
/smart-commit
```

### `/task-orchestrator` - Structured Task Workflow

Coordinates structured task implementation using specialized implementer and review agents.

**Workflow:**
1. Reads a task list markdown file
2. Identifies the next uncompleted task
3. Spawns an Implementer Agent to execute the task
4. Spawns a Review Architect Agent to review the implementation
5. Presents results and pauses for user approval
6. Runs `/smart-commit` to commit changes
7. Marks task complete and moves to next task

**Usage:**
```bash
# Start orchestrated workflow
/task-orchestrator path/to/tasks.md
```

**Task list format:**
```markdown
## Tasks

- [ ] 1.0 Parent Task Title
  - [ ] 1.1 Subtask description
  - [ ] 1.2 Subtask description
- [ ] 2.0 Another Parent Task
  - [ ] 2.1 Subtask description
```

**Benefits:**
- Systematic task execution with built-in code review
- Automatic commit creation after each task
- Clear separation between implementation and review
- User approval at each step ensures control
- Progress tracking via task list updates

See [commands/README.md](commands/README.md) for full documentation and examples.

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

## Integration with AI Tools

### Claude Code / Claude Desktop

Add this to your `~/.claude/CLAUDE.md`:

```markdown
## Agent Instructions

For all projects, follow the guidelines in ~/Code/dotfiles/AGENTS.md

When working on specific technologies, reference:
- Ruby/Rails: ~/Code/dotfiles/ai-config/guidelines/ruby-rails.md
- JavaScript: ~/Code/dotfiles/ai-config/guidelines/javascript.md
- Testing: ~/Code/dotfiles/ai-config/guidelines/testing.md
- Git workflow: ~/Code/dotfiles/ai-config/guidelines/git-workflow.md
```

### Project-Specific AGENTS.md

For individual projects, create an `AGENTS.md` in the project root:

```markdown
# Project Name - Agent Guidelines

## Project-Specific Setup
[Project-specific instructions]

## General Guidelines
For general coding guidelines, see: ~/Code/dotfiles/AGENTS.md

## Tech Stack
- Ruby on Rails 7.x
- Hotwire (Turbo + Stimulus)
- PostgreSQL
- RSpec for testing
```

## Customization

Feel free to customize these guidelines to match your team's preferences:

1. **Fork this structure** for your organization
2. **Modify guidelines** to match your coding standards
3. **Add project-specific sections** as needed
4. **Create additional guideline files** for other languages/frameworks

## Contributing

To add or update guidelines:

1. Create a feature branch following the naming convention:
   - With ticket: `<TICKET>-jdw-<agent-name>-<description>` (e.g., `MDZ-10-jdw-claude-update-guidelines`)
   - Without ticket: `jdw-<agent-name>-<description>` (e.g., `jdw-claude-update-guidelines`)
2. Make your changes
3. Follow the commit guidelines in [guidelines/git-workflow.md](guidelines/git-workflow.md)
4. Submit a pull request

## Why This Matters

AI coding agents are powerful tools, but they work best with clear, comprehensive instructions. This repository:

- **Reduces repetition** - Write guidelines once, reference everywhere
- **Ensures consistency** - All AI agents follow the same conventions
- **Improves quality** - Agents know to test, lint, and follow best practices
- **Saves time** - Agents understand your workflow from the start
- **Makes onboarding easier** - New team members (human or AI) have clear documentation

## References

- [AGENTS.md Standard](https://github.com/openai/agents.md) - Original specification from OpenAI
- [AGENTS.md Website](https://agents.md/) - Documentation and examples
- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code) - Claude Code specific features

---

**Remember**: These guidelines are living documents. Update them as your practices evolve, and always prioritize clarity and maintainability.
