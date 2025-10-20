---
name: smart-commit
description: Intelligently commit changes by analyzing diffs and creating focused commits
allowed-tools: Bash, Read
---

You are about to create smart, focused git commits following the repository's git workflow guidelines.

## Your Task

1. **Check Current Branch**
   - Run `git branch --show-current` to check the current branch
   - If on `main` or `master`:
     - Check conversation context for ticket references (Linear issues like MDZ-10, Jira tickets, etc.)
     - Analyze the staged/unstaged changes to understand what feature/fix is being worked on
     - Create a descriptive feature branch:
       - **With ticket**: `<TICKET>-claude-<feature-description>` (e.g., `MDZ-10-claude-add-comments`)
       - **Without ticket**: `claude-<feature-description>` (e.g., `claude-refactor-orders`)
     - Use kebab-case for the feature description
     - Switch to the new branch

2. **Analyze All Changes**
   - Run `git status` to see what files have been modified
   - Run `git diff` to see unstaged changes
   - Run `git diff --staged` to see staged changes
   - Analyze ALL changes (both staged and unstaged) to understand:
     - What is the overall goal of these changes?
     - Are there multiple distinct topics/features being changed?
     - What files are related to each topic?

3. **Create Focused Commits**
   - **If changes are a single topic/feature:**
     - Stage all related files
     - Write ONE meaningful commit message that:
       - Uses imperative mood ("Add feature" not "Added feature")
       - Explains WHAT and WHY (not just what files changed)
       - Is descriptive but concise
       - Does NOT use prefixes like "feat:", "fix:", "bug:"
     - Create the commit

   - **If changes span multiple topics/features:**
     - Group related files by topic/feature
     - For EACH topic, create a separate commit:
       - Stage only files related to that topic
       - Write a focused commit message for that specific change
       - Create the commit
       - Move to the next topic
     - Process all topics until all changes are committed

4. **Commit Message Guidelines**
   - Use imperative mood: "Add user authentication" (not "Added" or "Adding")
   - Be specific: "Fix cart calculation for tax rates" (not "Fix bug")
   - Explain the why when helpful: "Refactor order processing for better performance"
   - NO prefixes: Don't use "feat:", "fix:", "bug:", "chore:", etc.
   - First line should be 50 chars or less
   - Add detailed explanation in body if needed (wrapped at 72 chars)

5. **Examples of Good Commits**

   **Single topic - simple:**
   ```
   Add user authentication with Devise
   ```

   **Single topic - with explanation:**
   ```
   Update order calculation to include tax

   Previously orders only calculated subtotal. Now properly calculates
   tax based on user's location and adds it to the final total.
   ```

   **Multiple topics - separate commits:**
   ```
   # Commit 1
   Add Comment model with validations

   # Commit 2
   Add comments controller and routes

   # Commit 3
   Add comment form to article show page

   # Commit 4
   Add tests for comment functionality
   ```

6. **Final Status**
   - After all commits are created, run `git status` to confirm all changes are committed
   - Show a summary of what was committed

## Important Notes

- Always analyze the FULL diff before deciding how to split commits
- Each commit should be focused on ONE logical change
- If unsure whether to split, err on the side of MORE focused commits
- Never include unrelated changes in the same commit
- Commit messages should be meaningful to someone reading git history months later

Now, execute this workflow with the current repository state.
