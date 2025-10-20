---
name: create-pr
description: Create a pull request for the current branch with proper analysis and formatting
allowed-tools: Bash, Read
---

# Create Pull Request

You are tasked with creating a pull request for the current branch.

## Process

1. **Analyze the branch changes** - Run these commands in parallel:
   - `git status` to see current state
   - `git diff $(git merge-base HEAD main)...HEAD` to see all changes since diverging from main
   - `git log --oneline $(git merge-base HEAD main)..HEAD` to see all commits in this branch
   - Check if branch tracks remote and is up to date

2. **Draft PR summary** - Based on ALL commits and changes (not just the latest):
   - Create a descriptive PR title that summarizes the overall feature/fix
   - Write a summary section with 1-3 bullet points covering the key changes
   - Include a test plan section with a bulleted checklist of TODOs for testing

3. **Create the PR**:
   - Push to remote with `-u` flag if needed
   - Use `gh pr create` with `--draft` flag (draft by default)
   - Use heredoc format for the body:
   ```bash
   gh pr create --draft --title "PR title" --body "$(cat <<'EOF'
   ## Summary
   - Bullet point 1
   - Bullet point 2

   ## Test plan
   - [ ] Test item 1
   - [ ] Test item 2

   🤖 Generated with [Claude Code](https://claude.com/claude-code)
   EOF
   )"
   ```

4. **Return the PR URL** so the user can access it

## Options

The user may specify:
- `--ready` or `--no-draft` - Create PR in ready state instead of draft
- `--base <branch>` - Specify base branch (defaults to main)

## Important Notes

- ALWAYS analyze ALL commits in the branch, not just the latest one
- The PR summary should reflect the complete set of changes
- Default to draft mode unless explicitly told otherwise
- Never skip pushing if the branch isn't on remote yet
