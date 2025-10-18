---
description: Execute task lists with automated implementation and manual approval between each sub-task
allowed-tools: Read, Edit, Bash
---

You are managing a task list in a markdown file to track progress on completing a Requirements Document. Follow these strict guidelines:

## Task Implementation

**One sub-task at a time:**
- Do NOT start the next sub-task until you ask the user for permission and they say "yes" or "y"

**Completion protocol:**

1. When you finish a **sub-task**, immediately mark it as completed by changing `[ ]` to `[x]`

2. If **all** subtasks underneath a parent task are now `[x]`, follow this sequence:
   - **First**: Run the relevant tests
   - **Only if all tests pass**: Stage changes (`git add .`)
   - **Clean up**: Remove any temporary files and temporary code before committing
   - **Commit**: Use a descriptive commit message that:
     - Summarizes what was accomplished in the parent task
     - Lists key changes and additions
     - References the task number and Requirements Document context
     - **Formats the message as a single-line command using `-m` flags**, e.g.:
       ```
       git commit -m "Add payment validation logic" -m "- Validates card type and expiry" -m "- Adds unit tests for edge cases" -m "Related to T123 in Requirements Document"
       ```

3. Once all the subtasks are marked completed and changes have been committed, mark the **parent task** as completed

4. Stop after each sub-task and wait for the user's go-ahead

## Task List Maintenance

**Update the task list as you work:**
- Mark tasks and subtasks as completed (`[x]`) per the protocol above
- Add new tasks as they emerge

**Maintain the "Relevant Files" section:**
- List every file created or modified
- Give each file a one-line description of its purpose

## Your Instructions

When working with task lists, you must:

1. Regularly update the task list file after finishing any significant work
2. Follow the completion protocol:
   - Mark each finished **sub-task** `[x]`
   - Mark the **parent task** `[x]` once **all** its subtasks are `[x]`
3. Add newly discovered tasks
4. Keep "Relevant Files" accurate and up to date
5. Before starting work, check which sub-task is next
6. After implementing a sub-task, update the file and then pause for user approval

## Important Notes

- NEVER run additional commands to read or explore code, besides what's needed for the current sub-task
- NEVER use the TodoWrite tool
- DO NOT proceed to the next sub-task without explicit user permission
