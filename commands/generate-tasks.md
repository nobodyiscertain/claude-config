---
name: generate-tasks
description: Generate a detailed, step-by-step task list from a Requirements Document (PRD or ERD)
allowed-tools: Read, Write, Grep, Glob
---

You are creating a detailed, step-by-step task list in Markdown format based on an existing Requirements Document (PRD or ERD). The task list will guide a junior developer through implementation.

## Your Process

1. **Receive Document Reference**
   - The user will point you to a specific PRD or ERD file

2. **Analyze Document**
   - Read and analyze the functional requirements, user stories, and other sections
   - Understand the full scope of the feature

3. **Phase 1: Generate Parent Tasks**
   - Create the task file in `/plans/` with filename `tasks-[document-name].md`
   - Generate the main, high-level tasks required to implement the feature
   - Use your judgment on how many high-level tasks (likely around 5)
   - Present these tasks in the specified format (without sub-tasks yet)
   - Inform the user: "I have generated the high-level tasks based on the document. Ready to generate the sub-tasks? Respond with 'Go' to proceed."

4. **Wait for Confirmation**
   - Pause and wait for the user to respond with "Go"

5. **Phase 2: Generate Sub-Tasks**
   - Once confirmed, break down each parent task into smaller, actionable sub-tasks
   - Ensure sub-tasks logically follow from the parent task
   - Cover all implementation details implied by the document

6. **Identify Relevant Files**
   - Based on the tasks and document, identify potential files that will need to be created or modified
   - Include corresponding test files if applicable
   - List these under the `Relevant Files` section

7. **Save Task List**
   - Save the generated document in `/plans/` directory
   - Use filename: `tasks-[document-name].md`
   - Example: If input was `prd-user-profile-editing.md`, output is `tasks-prd-user-profile-editing.md`

## Task List Format

Your generated task list MUST follow this structure:

```markdown
## Relevant Files

- `path/to/potential/file1.ts` - Brief description of why this file is relevant (e.g., Contains the main component for this feature).
- `path/to/file1.test.ts` - Unit tests for `file1.ts`.
- `path/to/another/file.tsx` - Brief description (e.g., API route handler for data submission).
- `path/to/another/file.test.tsx` - Unit tests for `another/file.tsx`.
- `lib/utils/helpers.ts` - Brief description (e.g., Utility functions needed for calculations).
- `lib/utils/helpers.test.ts` - Unit tests for `helpers.ts`.

## Tasks

- [ ] 1.0 Parent Task Title
  - [ ] 1.1 [Sub-task description 1.1]
  - [ ] 1.2 [Sub-task description 1.2]
- [ ] 2.0 Parent Task Title
  - [ ] 2.1 [Sub-task description 2.1]
- [ ] 3.0 Parent Task Title (may not require sub-tasks if purely structural or configuration)
```

## Interaction Model

- Explicitly pause after generating parent tasks
- Get user confirmation ("Go") before proceeding to detailed sub-tasks
- This ensures the high-level plan aligns with user expectations before diving into details

## Important Notes

- Write for a junior developer who will implement the feature
- Make tasks clear, actionable, and complete
- Ensure all aspects of the requirements document are covered
