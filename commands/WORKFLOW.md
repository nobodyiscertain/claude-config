# AI-Assisted Development Workflow

Complete workflow guide showing how to use Claude Code slash commands for feature development, from initial idea to production merge.

## Table of Contents

1. [Quick Reference](#quick-reference)
2. [Workflow Decision Tree](#workflow-decision-tree)
3. [Small Changes Workflow](#small-changes-workflow)
4. [Large Features Workflow](#large-features-workflow)
5. [Command Integration Map](#command-integration-map)
6. [Real-World Examples](#real-world-examples)

---

## Quick Reference

### Available Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/brainstorm` | Refine vague ideas | Idea is unclear or needs exploration |
| `/create-prd` | Generate Product Requirements | Need business/product perspective |
| `/create-erd` | Generate Engineering Requirements | Need technical specification |
| `/generate-tasks` | Create task breakdown | Have requirements, need implementation steps |
| `/process-task-list` | Update task completion | Manually working through task list |
| `/task-orchestrator` | Automated task execution | Want AI to implement with review cycles |
| `/worktree-setup` | Create isolated workspace | Starting a large/complex feature |
| `/worktree-finish` | Clean up completed worktree | Feature is merged, ready to clean up |
| `/smart-commit` | Intelligent commit creation | Need focused, logical commits |

---

## Workflow Decision Tree

```
Start: I have an idea
│
├─── Is the idea clear and well-defined?
│    ├─── No → /brainstorm (explore and refine)
│    └─── Yes → Continue
│
├─── Is this a small change or large feature?
│    │
│    ├─── Small (< 1 day, simple, few files)
│    │    └─── Use: Small Changes Workflow
│    │
│    └─── Large (multi-day, complex, many files)
│         └─── Use: Large Features Workflow
│
└─── Do I want AI to implement or DIY?
     ├─── AI Implementation → /task-orchestrator
     └─── Manual Implementation → /process-task-list
```

---

## Small Changes Workflow

**Use for:** Bug fixes, small features, quick improvements, documentation updates

### Flow

```
1. Create feature branch
   └─ git checkout -b jdw-claude-fix-bug

2. Make changes
   └─ Edit files, write tests

3. Commit intelligently
   └─ /smart-commit

4. Push and create PR
   └─ git push -u origin jdw-claude-fix-bug
   └─ gh pr create --draft

5. Merge and cleanup
   └─ (PR merged on GitHub)
   └─ git checkout main && git pull
   └─ git branch -d jdw-claude-fix-bug
```

### Example Commands

```bash
# Start
git checkout main
git pull origin main
git checkout -b jdw-claude-add-validation

# Work
# ... make changes ...

# Commit
/smart-commit

# PR
git push -u origin jdw-claude-add-validation
gh pr create --draft --title "Add form validation"

# After merge
git checkout main
git pull
git branch -d jdw-claude-add-validation
```

**Key Points:**
- ✅ Stay on main repository (no worktree needed)
- ✅ Use `/smart-commit` for intelligent commits
- ✅ Keep scope small and focused
- ✅ Quick iteration cycles

---

## Large Features Workflow

**Use for:** Major features, complex refactors, multi-day work, parallel development

### Flow

```
1. Set up isolated workspace
   └─ /worktree-setup

2. Refine idea (if needed)
   └─ /brainstorm

3. Create requirements
   └─ /create-prd  OR  /create-erd

4. Generate task breakdown
   └─ /generate-tasks tasks/prd-feature-name.md

5. Execute tasks
   ├─ Option A: AI Implementation
   │  └─ /task-orchestrator tasks/tasks-feature-name.md
   │
   └─ Option B: Manual Implementation
      └─ /process-task-list tasks/tasks-feature-name.md

6. Create PR
   └─ gh pr create --draft

7. Merge and cleanup
   └─ (PR merged)
   └─ /worktree-finish
```

### Detailed Steps

#### Step 1: Workspace Setup

```bash
# From main repo
/worktree-setup
```

**Input:**
- Feature name: `user-authentication`
- Ticket: `MDZ-123` (optional)

**Output:**
- Worktree created at `.worktrees/MDZ-123-jdw-claude-user-authentication/`
- Branch: `MDZ-123-jdw-claude-user-authentication`
- Claude session active in worktree

#### Step 2: Idea Refinement (Optional)

```bash
# If idea is vague
/brainstorm
```

**Process:**
1. Answer clarifying questions
2. Explore in 200-300 word chunks
3. Confirm each section
4. Get final summary saved to `tasks/brainstorm-feature.md`

#### Step 3: Requirements Documentation

```bash
# Product focus
/create-prd

# OR Engineering focus
/create-erd
```

**Output:**
- `tasks/prd-user-authentication.md` (product requirements)
- `tasks/erd-user-authentication.md` (engineering requirements)

#### Step 4: Task Generation

```bash
/generate-tasks tasks/prd-user-authentication.md
```

**Output:**
- `tasks/tasks-user-authentication.md` (detailed task list)

#### Step 5A: AI Implementation (Automated)

```bash
/task-orchestrator tasks/tasks-user-authentication.md
```

**What happens:**
1. Orchestrator reads task list
2. Spawns **Implementer Agent** for next task
3. Implementer completes task
4. Spawns **Review Architect Agent** to review
5. Presents review to you
6. **Waits for your approval**
7. Runs `/smart-commit` to commit changes
8. Marks task complete
9. Repeats for next task

**Your role:**
- Review each task implementation
- Approve to continue or request changes
- Trust but verify

#### Step 5B: Manual Implementation (DIY)

```bash
/process-task-list tasks/tasks-user-authentication.md
```

**What happens:**
- Reads task list
- Guides you through each task
- You implement manually
- Update task list as you go
- Use `/smart-commit` when ready

#### Step 6: Create Pull Request

```bash
git push -u origin MDZ-123-jdw-claude-user-authentication

gh pr create --draft --title "Add user authentication system" --body "$(cat <<'EOF'
## Summary
Implements user authentication with email/password login.

## Changes
- Add User model and authentication logic
- Create login/signup forms
- Add session management
- Include comprehensive tests

## Testing
- [x] Unit tests pass
- [x] Integration tests pass
- [x] Manual testing complete

## Related
Closes MDZ-123
EOF
)"
```

#### Step 7: Cleanup After Merge

```bash
# After PR is merged on GitHub
/worktree-finish
```

**What happens:**
1. Detects you're in a worktree
2. Checks PR status (merged ✅)
3. Warns about uncommitted changes (if any)
4. Asks for confirmation
5. Navigates to main repo
6. Updates main branch
7. Removes worktree
8. Deletes local branch
9. Confirms success

**Key Points:**
- ✅ Isolated workspace prevents conflicts
- ✅ Structured approach with clear documentation
- ✅ AI assistance with human oversight
- ✅ Clean cleanup when done

---

## Command Integration Map

### Brainstorming Phase

```
Vague Idea
    ↓
/brainstorm
    ↓
tasks/brainstorm-feature.md
    ↓
Clear Idea
```

### Requirements Phase

```
Clear Idea
    ↓
/create-prd  OR  /create-erd
    ↓
tasks/prd-feature.md
tasks/erd-feature.md
```

### Planning Phase

```
Requirements Document
    ↓
/generate-tasks tasks/prd-feature.md
    ↓
tasks/tasks-feature.md
```

### Implementation Phase

```
Task List
    ↓
┌─────────────────┬─────────────────┐
│                 │                 │
/task-orchestrator  /process-task-list
(AI implements)     (You implement)
│                 │
└─────────────────┴─────────────────┘
    ↓
Code Changes
    ↓
/smart-commit
    ↓
Focused Commits
```

### Completion Phase

```
All Tasks Done
    ↓
git push
    ↓
gh pr create --draft
    ↓
Code Review
    ↓
gh pr ready
    ↓
PR Merged
    ↓
/worktree-finish
    ↓
Clean Workspace
```

---

## Real-World Examples

### Example 1: Small Bug Fix

**Scenario:** Fix a validation bug in the login form

```bash
# Start
git checkout main
git pull origin main
git checkout -b jdw-claude-fix-login-validation

# Fix
# ... edit LoginForm.tsx ...
# ... update tests ...

# Commit
/smart-commit
# Creates: "Fix email validation in login form"

# PR
git push -u origin jdw-claude-fix-login-validation
gh pr create --draft --title "Fix login validation bug"
gh pr ready

# After merge
git checkout main
git pull
git branch -d jdw-claude-fix-login-validation
```

**Time:** 30 minutes
**Workflow:** Small Changes
**Worktree:** Not needed

---

### Example 2: Medium Feature with Clear Requirements

**Scenario:** Add export functionality (you know what to build)

```bash
# Setup
/worktree-setup
# Feature: export-to-csv
# Ticket: (none)

# Requirements
/create-erd
# Answer questions about export functionality
# Output: tasks/erd-export-to-csv.md

# Tasks
/generate-tasks tasks/erd-export-to-csv.md
# Output: tasks/tasks-export-to-csv.md

# Implement (manual)
/process-task-list tasks/tasks-export-to-csv.md
# Work through tasks, commit as you go

# PR
git push -u origin jdw-claude-export-to-csv
gh pr create --draft --title "Add CSV export functionality"
# (Review, merge)

# Cleanup
/worktree-finish
```

**Time:** 1-2 days
**Workflow:** Large Features
**Worktree:** Yes

---

### Example 3: Complex Feature with Vague Idea

**Scenario:** "I want some kind of analytics dashboard... not sure exactly what yet"

```bash
# Setup
/worktree-setup
# Feature: analytics-dashboard
# Ticket: PROJ-456

# Explore idea
/brainstorm
# Q: What data should it show?
# A: User activity, revenue trends, maybe some charts?
# ... (several rounds of refinement) ...
# Output: tasks/brainstorm-analytics-dashboard.md

# Requirements
/create-prd
# Uses insights from brainstorm
# Output: tasks/prd-analytics-dashboard.md

# Tasks
/generate-tasks tasks/prd-analytics-dashboard.md
# Output: tasks/tasks-analytics-dashboard.md

# Implement (AI-assisted)
/task-orchestrator tasks/tasks-analytics-dashboard.md
# Review each task as orchestrator implements
# Approve/reject as needed

# PR
git push -u origin PROJ-456-jdw-claude-analytics-dashboard
gh pr create --draft --title "Add analytics dashboard"
# (Review, merge)

# Cleanup
/worktree-finish
```

**Time:** 3-5 days
**Workflow:** Large Features
**Worktree:** Yes
**AI Assistance:** High

---

### Example 4: Parallel Work Scenario

**Scenario:** Working on big feature, urgent bug comes in

```bash
# Current state: Working in worktree on feature A
pwd
# /Users/you/Code/myapp/.worktrees/jdw-claude-feature-a

# Urgent bug reported!
# Open NEW terminal (keep Claude running in worktree)

# In new terminal:
cd ~/Code/myapp                    # Main repo
git checkout main
git pull
git checkout -b jdw-claude-hotfix

# Fix bug quickly
# ... edit files ...
git add .
git commit -m "Fix critical bug in payment processing"
git push -u origin jdw-claude-hotfix
gh pr create --title "Hotfix: payment bug"

# Merge immediately, cleanup
# (PR merged)
git checkout main
git pull
git branch -d jdw-claude-hotfix

# Return to original terminal
# Claude still working in feature-a worktree!
# Resume work on feature A
```

**Key benefit:** No stashing, no context switching, both workspaces independent

---

## Best Practices

### When to Use Worktrees

**✅ Use Worktrees For:**
- Features taking > 1 day
- Work you'll return to multiple times
- Parallel development needs
- Risky experiments
- Large refactors
- When you want AI agent isolation

**❌ Don't Use Worktrees For:**
- Quick bug fixes
- Documentation updates
- Small improvements
- One-off changes
- When you're working linearly

### Command Selection

**Start with `/brainstorm` when:**
- Idea is vague or exploratory
- Requirements are unclear
- Stakeholders haven't aligned
- You're not sure what to build

**Skip to `/create-prd` or `/create-erd` when:**
- Requirements are clear
- You know what to build
- Just need to document it

**Use `/task-orchestrator` when:**
- You trust AI to implement
- You want to focus on review
- Task is well-defined
- You have time to supervise

**Use `/process-task-list` when:**
- You want hands-on implementation
- Task is complex/nuanced
- You're learning the codebase
- You prefer full control

### Commit Strategy

**Use `/smart-commit` for:**
- Grouping related changes logically
- Creating focused commits
- Following commit conventions
- When you have multiple changes

**Manual commits when:**
- Single, simple change
- You want specific wording
- Commit is trivial

---

## Troubleshooting

### "I'm lost in a worktree"

```bash
# Where am I?
git worktree list

# Show all worktrees and branches

# Go back to main
cd $(git rev-parse --git-common-dir | xargs dirname)
```

### "I forgot which worktree has what"

```bash
# List all with branches
git worktree list

# Or check each one
for wt in .worktrees/*/; do
  echo "=== $wt ==="
  (cd "$wt" && git branch --show-current && git status -s)
done
```

### "Worktree won't delete"

```bash
# Force removal
git worktree remove .worktrees/branch-name --force

# If that fails, manual cleanup
rm -rf .worktrees/branch-name
git worktree prune
```

### "I committed to main by accident"

```bash
# Move commits to new branch
git branch jdw-claude-accidental-work
git reset --hard origin/main
git checkout jdw-claude-accidental-work
```

---

## Summary

### Complete Large Feature Flow

```
1. /worktree-setup           # Isolate workspace
2. /brainstorm (optional)    # Refine idea
3. /create-prd or /create-erd # Document requirements
4. /generate-tasks           # Break down work
5. /task-orchestrator        # AI implements (or /process-task-list for manual)
   └─ /smart-commit          # (automated by orchestrator)
6. gh pr create --draft      # Create PR
7. gh pr ready               # Mark ready
8. (PR merged)
9. /worktree-finish          # Clean up
```

### Quick Small Fix Flow

```
1. git checkout -b branch-name
2. (make changes)
3. /smart-commit
4. git push && gh pr create
5. (merge)
6. git checkout main && git pull
```

---

**Remember:** These workflows are guidelines, not rules. Adapt to your needs, and use the commands that make sense for your situation.

For more details on specific commands, see their individual documentation in the `commands/` directory.
