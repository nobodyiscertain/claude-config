---
name: create-prd
description: Generate a detailed Product Requirements Document (PRD) from an initial feature prompt
allowed-tools: Read, Write
---

You are creating a detailed Product Requirements Document (PRD) in Markdown format based on the user's initial feature prompt. The PRD should be clear, actionable, and suitable for a junior developer to understand and implement.

## Your Process

1. **Receive Initial Prompt**
   - Analyze the user's feature description or request

2. **Ask Clarifying Questions**
   - Gather sufficient detail to understand the "what" and "why" of the feature
   - Provide options in letter/number lists for easy selection
   - Focus on understanding user needs, not technical implementation details

### Question Areas to Explore

- **Problem/Goal:** What problem does this feature solve for the user? What is the main goal we want to achieve?
- **Target User:** Who is the primary user of this feature?
- **Core Functionality:** Can you describe the key actions a user should be able to perform with this feature?
- **User Stories:** Could you provide a few user stories? (e.g., As a [type of user], I want to [perform an action] so that [benefit].)
- **Acceptance Criteria:** How will we know when this feature is successfully implemented? What are the key success criteria?
- **Scope/Boundaries:** Are there any specific things this feature should NOT do (non-goals)?
- **Data Requirements:** What kind of data does this feature need to display or manipulate?
- **Design/UI:** Are there any existing design mockups or UI guidelines to follow? Can you describe the desired look and feel?
- **Edge Cases:** Are there any potential edge cases or error conditions we should consider?

3. **Generate PRD**
   - Use the user's answers to create a comprehensive PRD
   - Follow the structure below

## PRD Structure

Your generated PRD must include these sections:

1. **Introduction/Overview** - Briefly describe the feature and the problem it solves. State the goal.
2. **Goals** - List the specific, measurable objectives for this feature.
3. **User Stories** - Detail the user narratives describing feature usage and benefits.
4. **Functional Requirements** - List the specific functionalities the feature must have. Use clear, concise language (e.g., "The system must allow users to upload a profile picture."). Number these requirements.
5. **Non-Goals (Out of Scope)** - Clearly state what this feature will NOT include to manage scope.
6. **Design Considerations (Optional)** - Link to mockups, describe UI/UX requirements, or mention relevant components/styles if applicable.
7. **Technical Considerations (Optional)** - Mention any known technical constraints, dependencies, or suggestions (e.g., "Should integrate with the existing Auth module").
8. **Success Metrics** - How will the success of this feature be measured? (e.g., "Increase user engagement by 10%", "Reduce support tickets related to X").
9. **Open Questions** - List any remaining questions or areas needing further clarification.

## Output

After creating the PRD, save it to:
- **Format:** Markdown (`.md`)
- **Location:** `/plans/`
- **Filename:** `prd-[feature-name].md`

## Important Notes

- Requirements should be explicit, unambiguous, and avoid jargon where possible
- Write for a junior developer as the primary reader
- Provide enough detail for them to understand the feature's purpose and core logic
- DO NOT start implementing the PRD
- Take the user's answers to clarifying questions and improve the PRD
