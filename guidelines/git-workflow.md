# Git Workflow and Commit Guidelines

Comprehensive git workflow practices and commit standards for maintainable version control.

## Core Git Principles

- **Never commit to main**: Always work on feature branches
- **Small, focused commits**: Each commit should represent a single logical change
- **Meaningful messages**: Commit messages should explain what and why, not just what
- **Clean history**: Keep git history readable and useful for future developers

---

## Branch Management

### Main Branch Protection
- **Never commit directly to `main`** (or `master`)
- Main branch should only receive changes via pull requests
- Main branch should always be in a deployable state

### Feature Branch Naming Convention

**With ticket reference:**
`<TICKET>-jdw-<agent-name>-<feature-description>`

**Without ticket reference:**
`jdw-<agent-name>-<feature-description>`

**Examples**:
```bash
# With ticket (Linear, Jira, etc.)
MDZ-10-jdw-claude-user-authentication
PROJ-123-jdw-claude-payment-integration

# Without ticket
jdw-claude-fix-search-bug
jdw-claude-refactor-orders
```

**Pattern Breakdown**:
- `<TICKET>` - Optional ticket ID from Linear, Jira, etc. (e.g., `MDZ-10`, `PROJ-123`)
- `jdw` - My initials
- `<agent-name>` - Name of AI agent or developer (e.g., `claude`, `cursor`, `github-copilot`)
- `<feature-description>` - Clear, concise description using kebab-case

**When to include ticket:**
- Check conversation context for ticket references
- If working on a specific Linear/Jira issue, include the ticket ID
- If general maintenance or no ticket, omit the ticket prefix

### Creating Feature Branches

**From clean main**:
```bash
# Ensure you're on main and up to date
git checkout main
git pull origin main

# Create and switch to new feature branch
git checkout -b jdw-claude-add-comments-feature
```

**AI agents should auto-create branches**:
```bash
# If on main, automatically create feature branch
current_branch=$(git branch --show-current)
if [ "$current_branch" = "main" ]; then
  git checkout -b jdw-claude-<feature-name>
fi
```

---

## Commit Practices

### Commit Message Format

**Structure**:
```
Brief summary of change in imperative mood (50 chars or less)

Optional detailed explanation of what changed and why. Wrap at 72
characters. Focus on the motivation for the change and contrast with
previous behavior.

- Bullet points are acceptable
- Use a hyphen or asterisk for bullets
```

**Examples**:

**Good commits**:
```
Add user authentication with Devise

Implements user registration, login, and logout functionality using
the Devise gem. This provides a secure foundation for user management
and will allow us to add authorization in future commits.

Update order calculation to include tax

Previously orders only calculated subtotal. Now properly calculates
tax based on user's location and adds it to the final total. This
fixes the discrepancy customers were seeing at checkout.

Refactor article search for better performance

Replaced N+1 queries with eager loading and added database indexes
on frequently searched columns. Search response time improved from
~800ms to ~50ms in testing.
```

**Bad commits**:
```
fix bug                    # Too vague
Updated stuff             # Meaningless
wip                      # Not descriptive
Fixed the thing          # What thing?
feat: add login feature  # Avoid prefixes per guidelines
```

### Commit Message Guidelines

**DO**:
- Use imperative mood ("Add feature" not "Added feature" or "Adding feature")
- Capitalize first letter of summary
- No period at end of summary line
- Explain *why* the change was made in the body
- Reference issue numbers when applicable

**DON'T**:
- Use prefixes like "feat:", "fix:", "bug:", "chore:" (per your project guidelines)
- Include file names in summary (git already tracks that)
- Write vague messages like "updates" or "changes"
- Commit commented-out code
- Mix multiple unrelated changes in one commit

### Commit Size and Scope

**Small, Focused Commits**:
```bash
# Bad - one massive commit
git add .
git commit -m "Add entire shopping cart feature"

# Good - multiple focused commits
git add app/models/cart.rb
git commit -m "Add Cart model with item management"

git add app/controllers/carts_controller.rb
git commit -m "Add CartController with CRUD actions"

git add app/views/carts/
git commit -m "Add cart views for show and update"

git add spec/models/cart_spec.rb spec/controllers/carts_controller_spec.rb
git commit -m "Add tests for cart functionality"
```

**Each commit should**:
- Represent a single logical change
- Be able to be reverted independently
- Pass all tests (ideally)
- Be understandable without looking at the code

---

## Pre-commit Requirements

### 1. Remove Trailing Whitespace
```bash
# Check for trailing whitespace
git diff --check

# Remove trailing whitespace (various methods)
# Using sed (macOS/Linux)
find . -type f -name '*.rb' -exec sed -i '' 's/[[:space:]]*$//' {} +

# Using your editor's trim whitespace feature
# Most editors have this built-in
```

### 2. Run Linter
```bash
# Ruby/Rails
rubocop
rubocop -a  # Auto-fix issues

# JavaScript
npm run lint
npm run lint -- --fix  # Auto-fix issues

# Only commit if linter passes
rubocop && git commit -m "Your message"
```

### 3. Verify Tests Pass
```bash
# Ruby/Rails
rspec

# JavaScript
npm test

# Only commit if tests pass
rspec && git commit -m "Your message"
```

### Pre-commit Automation
Consider using git hooks to automate checks:

**.git/hooks/pre-commit**:
```bash
#!/bin/bash

echo "Running pre-commit checks..."

# Run linter
if command -v rubocop &> /dev/null; then
  echo "Running RuboCop..."
  rubocop
  if [ $? -ne 0 ]; then
    echo "RuboCop failed. Please fix errors before committing."
    exit 1
  fi
fi

# Run tests
if command -v rspec &> /dev/null; then
  echo "Running tests..."
  rspec
  if [ $? -ne 0 ]; then
    echo "Tests failed. Please fix before committing."
    exit 1
  fi
fi

echo "Pre-commit checks passed!"
exit 0
```

---

## Working with Commits

### Staging Changes

**Stage specific files**:
```bash
git add app/models/user.rb
git add app/controllers/users_controller.rb
```

**Stage specific hunks (interactive)**:
```bash
git add -p app/models/user.rb
# Interactively choose which changes to stage
```

**Review staged changes**:
```bash
git diff --staged
```

### Amending Commits

**When to amend**:
- Fixing typos in last commit message
- Adding forgotten files to last commit
- Making small fixes to last commit
- **Only if commit hasn't been pushed**

**How to amend**:
```bash
# Amend last commit message
git commit --amend -m "New commit message"

# Add files to last commit
git add forgotten_file.rb
git commit --amend --no-edit

# Amend and edit message
git add forgotten_file.rb
git commit --amend
```

**⚠️ WARNING**: Never amend commits that have been pushed to shared branches!

### Undoing Changes

**Unstage files**:
```bash
git reset HEAD app/models/user.rb
# or
git restore --staged app/models/user.rb
```

**Discard working directory changes**:
```bash
git checkout -- app/models/user.rb
# or
git restore app/models/user.rb
```

**Undo last commit (keep changes)**:
```bash
git reset --soft HEAD~1
```

**Undo last commit (discard changes)**:
```bash
git reset --hard HEAD~1
```

---

## Pull Requests

### Creating Pull Requests

**Default to draft mode**:
- Create PRs in draft mode when available
- Allows for early feedback without signaling "ready to merge"
- Mark as "Ready for review" once complete and CI passes

**Before creating PR**:
1. Ensure all tests pass
2. Run linter and fix all issues
3. Remove all trailing whitespace
4. Rebase on latest main (if needed)
5. Review your own changes first

**PR Title Format**:
```
Add user authentication system
Fix cart calculation bug for tax rates
Refactor order processing for better performance
```

**PR Description Template**:
```markdown
## Summary
Brief description of what this PR does and why.

## Changes
- List of specific changes made
- Another change
- One more change

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Related Issues
Closes #123
Relates to #456
```

### Using GitHub CLI (`gh`)

**Create PR from command line**:
```bash
# Push branch
git push -u origin jdw-claude-add-comments

# Create PR in draft mode (recommended)
gh pr create --draft --title "Add comments feature" --body "$(cat <<'EOF'
## Summary
Adds commenting functionality to articles.

## Changes
- Add Comment model with validations
- Add comments controller and routes
- Add comment form to article show page
- Include tests for all new functionality

## Testing
- [x] Unit tests added
- [x] Integration tests pass
- [x] Manual testing completed
EOF
)"

# Mark as ready for review when complete
gh pr ready
```

**Review PR**:
```bash
# View PR details
gh pr view

# View diff
gh pr diff

# Check status
gh pr status

# List PRs
gh pr list
```

---

## Rebasing and Merging

### Rebasing on Main

**Why rebase**:
- Keep linear history
- Avoid merge commits
- Make history easier to understand

**How to rebase**:
```bash
# Ensure main is up to date
git checkout main
git pull origin main

# Rebase your feature branch
git checkout jdw-claude-my-feature
git rebase main

# If conflicts occur, resolve them
git add <resolved-files>
git rebase --continue

# Force push (since history was rewritten)
git push --force-with-lease origin jdw-claude-my-feature
```

### Interactive Rebase

**Clean up commits before merging**:
```bash
# Rebase last 3 commits
git rebase -i HEAD~3

# Options:
# pick   - keep commit as is
# reword - keep commit but edit message
# squash - combine with previous commit
# drop   - remove commit
```

**Example**:
```
pick abc123 Add user model
squash def456 Fix typo in user model
pick ghi789 Add user controller
reword jkl012 Add user tests
```

### Merge Strategies

**Squash merge (recommended for feature branches)**:
```bash
# Creates single commit on main
gh pr merge --squash
```

**Merge commit**:
```bash
# Preserves all commits
gh pr merge --merge
```

**Rebase and merge**:
```bash
# Replays commits on top of main
gh pr merge --rebase
```

---

## Working with Remote

### Syncing with Remote

**Fetch changes**:
```bash
git fetch origin
```

**Pull changes**:
```bash
git pull origin main
# or
git pull --rebase origin main  # Preferred
```

**Push changes**:
```bash
# First push of new branch
git push -u origin jdw-claude-my-feature

# Subsequent pushes
git push

# Force push (after rebase, use with caution)
git push --force-with-lease origin jdw-claude-my-feature
```

### Managing Remotes

**View remotes**:
```bash
git remote -v
```

**Add remote**:
```bash
git remote add upstream https://github.com/original/repo.git
```

**Update from upstream**:
```bash
git fetch upstream
git rebase upstream/main
```

---

## Useful Git Commands

### Inspecting History

```bash
# View commit history
git log
git log --oneline
git log --graph --oneline --all

# View changes in commits
git log -p
git log -p app/models/user.rb

# Search commits
git log --grep="authentication"
git log --author="Claude"

# View specific commit
git show abc123

# View file history
git log --follow app/models/user.rb
```

### Finding Issues

```bash
# Find when bug was introduced (binary search)
git bisect start
git bisect bad  # Current version has bug
git bisect good abc123  # This commit was good
# Git will checkout commits for you to test
git bisect good  # or git bisect bad
# Repeat until bug is found

# Find who changed a line
git blame app/models/user.rb

# Search codebase history
git grep "TODO" $(git rev-list --all)
```

### Stashing Changes

```bash
# Stash current changes
git stash

# Stash with message
git stash save "WIP: working on feature"

# List stashes
git stash list

# Apply last stash
git stash apply

# Apply and remove last stash
git stash pop

# Apply specific stash
git stash apply stash@{2}

# Drop stash
git stash drop stash@{0}
```

---

## Git Configuration

### User Configuration

```bash
# Set user info
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor "vim"
```

### Useful Aliases

```bash
# Add to ~/.gitconfig
[alias]
  st = status
  co = checkout
  br = branch
  ci = commit
  unstage = reset HEAD --
  last = log -1 HEAD
  lg = log --graph --oneline --all
  amend = commit --amend --no-edit
```

### Git Ignore

**.gitignore essentials**:
```gitignore
# Dependencies
node_modules/
vendor/bundle/

# Environment files
.env
.env.local
.env.*.local

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
.idea/
*.swp
*.swo
*~

# Build artifacts
/dist
/build
/.next
/tmp
/log/*.log

# Test coverage
/coverage

# Credentials
config/credentials/*.key
*.pem
```

---

## Common Workflows

### Starting New Feature

```bash
git checkout main
git pull origin main
git checkout -b jdw-claude-new-feature

# Make changes
git add .
git commit -m "Add new feature"

# More changes
git add .
git commit -m "Add tests for new feature"

# Push and create PR
git push -u origin jdw-claude-new-feature
gh pr create --title "Add new feature"
```

### Updating Feature Branch

```bash
# Get latest main
git checkout main
git pull origin main

# Update feature branch
git checkout jdw-claude-my-feature
git rebase main

# Resolve conflicts if any
# Then force push
git push --force-with-lease
```

### Fixing Bug on Main

```bash
git checkout main
git pull origin main
git checkout -b jdw-claude-fix-critical-bug

# Fix the bug
git add .
git commit -m "Fix critical bug in payment processing"

# Push and create PR
git push -u origin jdw-claude-fix-critical-bug
gh pr create --title "Fix critical bug"
```

---

## Emergency Procedures

### Accidentally Committed to Main

```bash
# Don't panic! Just move to a branch
git branch jdw-claude-accidental-work
git reset --hard origin/main
git checkout jdw-claude-accidental-work
```

### Accidentally Pushed to Main

```bash
# If no one has pulled yet
git reset --hard HEAD~1
git push --force origin main  # ⚠️ Dangerous! Coordinate with team

# If others have pulled, revert instead
git revert HEAD
git push origin main
```

### Lost Commits

```bash
# Find lost commits
git reflog

# Restore lost commit
git checkout <commit-hash>
git checkout -b jdw-claude-recovered-work
```

---

## Best Practices Summary

**Before committing**:
- [ ] Review changes with `git diff`
- [ ] Remove trailing whitespace
- [ ] Run linter and fix all issues
- [ ] Ensure all tests pass
- [ ] Write clear, meaningful commit message
- [ ] Verify you're on the correct branch (not main!)

**Before creating PR**:
- [ ] Rebase on latest main
- [ ] Clean up commit history if needed
- [ ] Review your own changes
- [ ] Ensure CI passes
- [ ] Write good PR description

**General rules**:
- Never commit directly to main
- Always work on feature branches
- Keep commits small and focused
- Write meaningful commit messages
- Clean up before pushing
- Communicate with team on force pushes

---

## References

- [Git Documentation](https://git-scm.com/doc)
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [Conventional Commits](https://www.conventionalcommits.org/) (for reference, but not required per guidelines)
- [Git Flight Rules](https://github.com/k88hudson/git-flight-rules)
