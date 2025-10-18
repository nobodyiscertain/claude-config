---
description: Generate a detailed Engineering Requirements Document (ERD) from a feature description
allowed-tools: Read, Write, Grep, Glob
---

You are creating a detailed Engineering Requirements Document (ERD) in Markdown format based on the engineer's feature description or brain dump. The ERD should be clear, actionable, and suitable for both AI agents (for task generation) and junior developers to understand and implement.

## Your Process

1. **Build Context**
   - Check the codebase to understand the existing system
   - Review relevant files, architecture, and patterns

2. **Ask Clarifying Questions**
   - Adapt questions based on what's missing or unclear in the initial description
   - Provide options in letter/number lists for easy selection
   - Focus on areas lacking detail:
     - If technical implementation is missing, dig deeper there
     - If feature behavior is unclear, focus on that

### Question Areas to Explore

**Feature Behavior & Requirements:**
- What specific problem does this feature solve?
- What engineering objective are we trying to achieve?
- Can you describe the key technical behaviors this feature must exhibit?
- What are the specific conditions that must be met for this feature to be considered complete?
- What edge cases, error conditions, or failure scenarios should we handle?

**Technical Implementation:**
- How should this feature fit into the existing system architecture?
- Do you have preferences for the technical approach or specific technologies to use?
- What data does this feature need to read, write, or transform?
- What APIs, endpoints, or interfaces need to be created or modified?

**Integration & Dependencies:**
- What existing systems, modules, or services does this feature depend on?
- Are there any third-party services, libraries, or tools required?
- How should this feature integrate with existing functionality?

**Performance & Constraints:**
- Are there specific performance, scalability, or load requirements?
- Are there any technical limitations, platform constraints, or compliance requirements?

**Testing & Validation:**
- What types of testing are needed (unit, integration, E2E)?
- How should we validate that the feature works correctly?

3. **Generate ERD**
   - Use the engineer's answers to create a comprehensive ERD
   - Follow the structure below

## ERD Structure

Your generated ERD must include these sections:

1. **Overview** - Brief description of the feature and the engineering problem it solves. State the primary objective.
2. **Engineering Objectives** - List specific, measurable technical goals for this feature.
3. **User Stories** - Detail user narratives describing feature usage and benefits (when applicable).
4. **Functional Requirements** - List specific functionalities the feature must have. Use clear, concise language. Number these requirements.
5. **Technical Requirements** - Specify technical behaviors, constraints, and implementation requirements.
6. **Implementation Approach** - Describe the recommended technical approach, architecture considerations, and key design decisions.
7. **Dependencies and Integration Points** - List system dependencies, external services, and integration requirements.
8. **Performance and Scalability Requirements** - Define performance expectations, load requirements, and scalability considerations.
9. **Testing Requirements** - Specify testing strategy, types of tests needed, and validation criteria.
10. **Non-Goals (Out of Scope)** - Clearly state what this feature will NOT include to manage scope.
11. **Implementation Milestones** - Define key checkpoints or phases for development (if applicable).
12. **Open Questions** - List any remaining technical questions or areas needing further investigation.

## Output

After creating the ERD, ask the user if they would like it:

**Locally:**
- Format: Markdown (`.md`)
- Location: `/plans/`
- Filename: `erd-[feature-name].md`

**Project Management Ticket:**
- Create in Linear with the ERD as the description

## Important Notes

- Requirements should be explicit, unambiguous, and provide enough technical detail for implementation planning
- Keep it accessible to junior developers
- DO NOT start implementing the ERD
- Focus questions on areas needing more detail
- Use the engineer's answers to create a comprehensive ERD suitable for AI-driven task generation
