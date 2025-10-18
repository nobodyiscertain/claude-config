# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is an **AI agent configuration repository** containing:
- Core agent workflow instructions (AGENTS.md)
- Custom Claude Code slash commands for development workflows
- Specialized agent role definitions (implementer, review-architect)
- Language-specific development guidelines (Ruby/Rails, JavaScript, Testing, Git)

This repository is designed to be referenced globally via `~/.claude/CLAUDE.md` or copied into individual projects to guide AI coding assistants.

## Repository Structure

```
claude-config/
├── AGENTS.md                      # Core agent guidelines (can be placed in project roots)
├── Rakefile                       # Automated setup/uninstall via rake tasks
├── .gitignore                     # Ignore system files and generated content
├── agents/                        # Specialized agent role definitions
│   ├── implementer.md            # Task execution agent (follows specs precisely)
│   └── review-architect.md       # Code review and quality assurance agent
├── commands/                      # Claude Code slash commands
│   ├── brainstorm.md             # Interactive idea refinement
│   ├── create-prd.md             # Product requirements generation
│   ├── create-erd.md             # Engineering requirements generation
│   ├── generate-tasks.md         # Task breakdown from requirements
│   ├── task-orchestrator.md      # Automated task execution with AI agents
│   ├── process-task-list.md      # Manual task list workflow
│   ├── smart-commit.md           # Intelligent commit creation
│   ├── create-pr.md              # Draft PR creation
│   └── update-docs.md            # Documentation sync from git history
└── guidelines/                    # Language-specific development guidelines
    ├── git-workflow.md           # Branch naming, commits, PRs
    ├── ruby-rails.md             # Rails conventions and best practices
    ├── javascript.md             # JS/TS guidelines, React, Hotwire
    └── testing.md                # RSpec, Jest, FactoryBot, mocking
```

## Development Workflows

This repository does not have traditional "build" or "test" commands since it contains markdown documentation and configuration files. However, the content defines complete development workflows for other projects.

### Working with This Repository

**To modify agent configurations:**
1. Edit relevant `.md` files in `agents/`, `commands/`, or `guidelines/`
2. Test changes by using the commands/agents in actual projects
3. Commit changes following git-workflow guidelines

**To test setup automation:**
1. Run `rake setup` to verify symlinking works correctly
2. Check `~/.claude/` directory for proper symlinks
3. Run `rake uninstall` to verify cleanup works
4. Ensure only repo-owned symlinks are removed

**To add a new slash command:**
1. Create a new `.md` file in `commands/` directory
2. Include frontmatter with `description` and `allowed-tools`
3. Write clear, step-by-step instructions for the agent
4. Update `commands/README.md` with documentation
5. Test the command in a real project

**To add a new guideline:**
1. Create a new `.md` file in `guidelines/` directory
2. Document language/framework-specific conventions
3. Update `AGENTS.md` to reference the new guideline
4. Include examples and anti-patterns

### Slash Command Architecture

All slash commands follow a consistent structure:

```markdown
---
description: Brief description (shown in command list)
allowed-tools: Bash, Read, Edit, Write, Task, SlashCommand
---

[Agent instructions that execute when command is invoked]
```

**Key command patterns:**

1. **Workflow Commands** (`/task-orchestrator`, `/process-task-list`):
   - Read task list files from `tasks/` directory
   - Use Task tool to spawn specialized agents
   - Update task completion status (`[ ]` → `[x]`)
   - Pause for user approval between steps

2. **Requirements Commands** (`/create-prd`, `/create-erd`):
   - Ask clarifying questions
   - Generate structured documentation
   - Save to `tasks/prd-*.md` or `tasks/erd-*.md`
   - Optionally integrate with Linear

3. **Git Commands** (`/smart-commit`, `/create-pr`):
   - Analyze git state
   - Follow branch naming: `<TICKET>-claude-<feature>` or `claude-<feature>`
   - Enforce proper workflows (never commit to main)

## Specialized Agent Roles

### Implementer Agent (agents/implementer.md)

**Purpose:** Execute implementation tasks with precision, following specifications exactly.

**Key principles:**
- Follow task description exactly (no scope creep)
- Do not refactor unless explicitly required
- Implement minimal changes focused on the task
- Report completion with clear summary

**Used by:** `/task-orchestrator` command

### Review Architect Agent (agents/review-architect.md)

**Purpose:** Provide critical code review and quality assurance.

**Key principles:**
- Review with fresh perspective
- Check correctness, quality, edge cases
- Provide constructive, actionable feedback
- Make recommendation: APPROVE / REQUEST CHANGES / NEEDS DISCUSSION

**Used by:** `/task-orchestrator` command

## Important Conventions

### Branch Naming
- **With ticket:** `<TICKET>-<agent-name>-<feature>` (e.g., `MDZ-10-claude-user-auth`)
- **Without ticket:** `<agent-name>-<feature>` (e.g., `claude-refactor-orders`)
- Check context for Linear/Jira ticket references

### Commit Messages
- Use imperative mood ("Add feature" not "Added feature")
- Do NOT use prefixes like "feat:", "fix:", "chore:"
- Be descriptive and explain "what" and "why"
- Reference ticket numbers when applicable

### Task List Format
Tasks are stored in `tasks/` directory as markdown with this structure:

```markdown
## Relevant Files
- `path/to/file.rb` - Description of what this file does

## Tasks
- [ ] 1.0 Parent Task
  - [ ] 1.1 Subtask description
  - [ ] 1.2 Subtask description
- [ ] 2.0 Another Parent Task
  - [ ] 2.1 Subtask description
```

Mark completed tasks with `[x]`.

### Directory Structure for Generated Files
- `tasks/brainstorm-*.md` - Output from `/brainstorm`
- `tasks/prd-*.md` - Product requirements from `/create-prd`
- `tasks/erd-*.md` - Engineering requirements from `/create-erd`
- `tasks/tasks-*.md` - Task breakdowns from `/generate-tasks`

## Integration with Other Projects

This repository is designed to be referenced globally or copied into projects:

### Global Setup

**Automated (Recommended):**
```bash
cd ~/Code/claude-config
rake setup
```

**Manual (Alternative):**
Add to `~/.claude/CLAUDE.md`:

```markdown
For all projects, follow the guidelines in ~/Code/claude-config/AGENTS.md

When working on specific technologies, reference:
- Ruby/Rails: ~/Code/claude-config/guidelines/ruby-rails.md
- JavaScript: ~/Code/claude-config/guidelines/javascript.md
- Testing: ~/Code/claude-config/guidelines/testing.md
- Git workflow: ~/Code/claude-config/guidelines/git-workflow.md
```

### Project-Specific Setup
Copy relevant files to `.claude/` in project directory:

```bash
# Copy all commands
cp -r ~/Code/claude-config/commands /path/to/project/.claude/

# Copy agents
cp -r ~/Code/claude-config/agents /path/to/project/.claude/

# Copy AGENTS.md to project root
cp ~/Code/claude-config/AGENTS.md /path/to/project/
```

## Testing Changes

Since this is a configuration/documentation repository, "testing" means:

1. **Validate markdown syntax:** Ensure files render correctly
2. **Test commands in real projects:** Use slash commands in actual development
3. **Verify agent behavior:** Check that implementer/review-architect work as intended
4. **Validate workflows:** Run through complete workflows (brainstorm → PRD → tasks → implementation)
5. **Test Rakefile tasks:** Verify `rake setup` and `rake uninstall` work correctly

## Common Patterns

### When Modifying Commands

1. **Read the existing command** to understand structure
2. **Follow the frontmatter format** (description + allowed-tools)
3. **Use clear, imperative instructions** for the agent
4. **Include examples** of expected behavior
5. **Reference other guidelines** when appropriate (e.g., link to AGENTS.md)
6. **Test in a real project** before committing

### When Updating Guidelines

1. **Keep it focused** - One language/framework per file
2. **Include examples** - Show good and bad patterns
3. **Be explicit** - Don't assume agents know conventions
4. **Update AGENTS.md** - Add reference links if needed
5. **Test with actual code** - Verify guidelines work in practice

## Architecture Notes

### Command Execution Flow

1. User types `/command-name` in Claude Code
2. Claude reads `commands/command-name.md`
3. Frontmatter defines allowed tools
4. Agent follows instructions in command file
5. Agent may spawn sub-agents using Task tool
6. Results presented to user

### Agent Spawning Pattern (Task Orchestrator)

```
Task Orchestrator (main agent)
  ↓
  ├─ Spawns Implementer Agent (via Task tool)
  │  └─ Reads agents/implementer.md
  │  └─ Executes task
  │  └─ Returns completion report
  ↓
  ├─ Spawns Review Architect Agent (via Task tool)
  │  └─ Reads agents/review-architect.md
  │  └─ Reviews implementation
  │  └─ Returns review feedback
  ↓
  └─ Presents results to user and waits for approval
```

### Workflow Orchestration

Large features follow this pattern:

```
Idea (vague)
  ↓ /brainstorm
Clear Idea
  ↓ /create-prd or /create-erd
Requirements Document
  ↓ /generate-tasks
Task List
  ↓ /task-orchestrator (AI) or /process-task-list (manual)
Implementation
  ↓ /smart-commit (automated)
Commits
  ↓ /create-pr
Pull Request
  ↓ (merge on GitHub)
Done
```

## Design Philosophy

This repository embodies several key principles:

1. **Systematic over ad-hoc:** Workflows are structured and repeatable
2. **Human oversight:** AI agents pause for approval (never fully autonomous)
3. **Separation of concerns:** Implementer focuses on execution, architect on review
4. **Documentation-driven:** Requirements → Tasks → Implementation
5. **Convention over configuration:** Strong conventions reduce decisions
6. **Minimal but complete:** Include what's needed, nothing more

## Maintenance Notes

**When adding new commands:**
- Place in `commands/` directory
- Update main `README.md` if it significantly changes workflows

**When updating agent roles:**
- Edit `agents/implementer.md` or `agents/review-architect.md`
- Test with `/task-orchestrator` to verify changes work
- Update `commands/task-orchestrator.md` if interface changes

**When updating guidelines:**
- Edit relevant file in `guidelines/`
- Ensure `AGENTS.md` references are up to date
- Test by using guidelines in real projects

## Related Resources

- Main documentation: `README.md`
- Agent guidelines: `AGENTS.md`
- Individual command files: `commands/*.md`
