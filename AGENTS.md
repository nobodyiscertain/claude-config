# AGENTS.md - AI Agent Guidelines

Core workflow guidance for AI agents working on software development projects.

## Foundational Principles

- **Correctness over Speed**: Prioritize doing things right over doing them quickly. Rushing leads to technical debt and bugs
- **Systematic Work**: Approach problems methodically and thoroughly. Follow established processes and workflows
- **Honest Feedback**: Provide truthful technical assessments, even when the news is difficult. Don't validate incorrect assumptions
- **Ask for Clarification**: When requirements are unclear or ambiguous, always ask before making assumptions. It's better to clarify than to build the wrong thing

---

## Communication & Partnership

- **Collaborative Approach**: Work as a partner, not just a tool. Engage in technical discussions and offer suggestions
- **Communicate Limitations**: Proactively inform about constraints, risks, or technical limitations you identify
- **Request Context**: When uncertain about architecture, priorities, or requirements, explicitly ask for guidance
- **Explain Reasoning**: Share the "why" behind technical decisions and recommendations

---

## Core Philosophy

- **YAGNI (You Aren't Gonna Need It)**: Build only what is needed now, not what might be needed later. Avoid premature optimization and speculative features
- **Convention over Configuration**: Follow established patterns and frameworks' conventions rather than creating novel solutions
- **Clarity over Cleverness**: Prioritize readable, maintainable code over clever implementations
- **Test-Driven Reliability**: Always include comprehensive tests with any code implementation
- **Progressive Enhancement**: Check existing codebase patterns and follow them consistently

---

## Agent Workflow

### When Starting Work
1. Check current git branch and status
2. Examine existing codebase patterns and conventions
3. Review package.json/Gemfile for existing dependencies
4. Understand the testing framework in use

### During Development
1. Follow established code patterns in the codebase
2. Write tests alongside implementation
3. Run linter frequently
4. Make small, focused commits
5. Keep development server running (avoid production builds)

### Before Completing Work
1. Ensure all tests pass
2. Run linter and fix all issues
3. Remove trailing whitespace
4. Review changes for security issues
5. Write clear commit messages
6. Create pull request in draft mode if requested (mark ready when complete)

---

## Git Workflow

### Branch Management
- **Never commit to main**: Always work on feature branches; create one automatically if not already on a branch
- **Feature branch naming**:
  - With ticket: `<TICKET>-<agent-name>-<feature-description>` (e.g., `MDZ-10-claude-user-authentication`)
  - Without ticket: `<agent-name>-<feature-description>` (e.g., `claude-refactor-orders`)
  - Check context for ticket references (Linear, Jira issues) and include if present

### Commit Practices
- **Small, focused commits**: Break work into logical, incremental commits rather than large, monolithic ones
- **Meaningful commit messages**: Write clear, descriptive messages that explain what and why
- **Avoid commit prefixes**: Do not use prefixes like "feat:", "fix:", "bug:" in commit messages

### Pre-commit Requirements
1. Run configured linter (RuboCop, ESLint, etc.) and fix all issues
2. Verify all tests pass

See [guidelines/git-workflow.md](guidelines/git-workflow.md) for detailed git practices.

---

## Language-Specific Guidelines

### Ruby on Rails
- Follow Rails conventions strictly (RESTful routes, MVC pattern)
- Keep controllers lightweight - business logic belongs in models/services
- Always write tests (RSpec) with FactoryBot
- Check Gemfile before adding new dependencies

See [guidelines/ruby-rails.md](guidelines/ruby-rails.md) for detailed Rails conventions.

### JavaScript
- Check package.json to understand the stack (React, Vue, vanilla JS, etc.)
- Default to vanilla JavaScript unless project uses a framework
- For Rails apps, prefer Hotwire/Stimulus over heavy JS frameworks
- Avoid TypeScript unless project already uses it

See [guidelines/javascript.md](guidelines/javascript.md) for detailed JavaScript conventions.

### Testing
- Write tests for all new features and bug fixes
- **Never delete failing tests**: Fix the test or fix the code, but don't remove tests to make the suite pass
- Unit tests should hit the database (avoid excessive mocking)
- Mock only external APIs, payment processors, email services
- Keep tests simple - focus on happy path for system/feature tests

See [guidelines/testing.md](guidelines/testing.md) for comprehensive testing guidance.

---

## Dependency Management

### General Principles
- **Check existing dependencies first**: Before adding new packages, check if functionality already exists
- **Update lockfiles**: Always update appropriate lockfile when adding/updating dependencies
- **Minimal dependencies**: Add new dependencies only when absolutely necessary

### Ruby/Rails
- Check the Gemfile for existing gems before adding new ones
- Run `bundle install` after updating Gemfile and commit Gemfile.lock

### JavaScript/Node.js
- Maintain lockfiles (package-lock.json, pnpm-lock.yaml, or yarn.lock)
- Use the project's package manager (npm, pnpm, or yarn)

---

## Security Guidelines

### General Security Practices
- **Never commit secrets**: Do not commit API keys, passwords, or sensitive credentials
- **Validate all inputs**: Always sanitize and validate user inputs
- **Use parameterized queries**: Prevent SQL injection with proper query parameterization
- **Follow OWASP guidelines**: Be aware of common vulnerabilities (XSS, CSRF, etc.)

### Credential Management
- Use environment variables for secrets (stored in `.env` files, gitignored)
- Never log sensitive data (passwords, tokens, PII)
- Warn before committing files like `.env`, `credentials.json`

---

## Debugging & Error Handling

### Systematic Debugging Process
1. **Identify Root Cause**: Read error messages carefully to understand the actual problem, not just symptoms
2. **Check Recent Changes**: Errors are often related to recent modifications - review what changed
3. **Hypothesis-Driven Testing**: Form a specific hypothesis about the cause, then test it with minimal changes
4. **One Fix at a Time**: Never make multiple simultaneous changes. Fix one thing, test, then move to the next
5. **Search for Patterns**: Look for similar issues in existing bug reports or documentation

### When Encountering Errors
1. Avoid speculation - work from evidence (error messages, stack traces, logs)
2. Make minimal, targeted changes to test hypotheses
3. Document what you've tried to avoid repeating failed approaches
4. Update dependencies cautiously - ensure compatibility and test thoroughly

### Common Issues and Solutions

#### Rails Issues
- **Spring not reloading**: Run `spring stop` and restart server
- **Migration errors**: Check for pending migrations with `rails db:migrate:status`
- **Asset issues**: Clear cache with `rails assets:clobber`

---

## Tools and Integrations

### Recommended MCP Tools
- **Context7**: For library documentation and code examples
- **Linear**: For issue tracking and project management
- **GitHub CLI (`gh`)**: For PR and issue management

### When to Use Tools
- **Context7 (ALWAYS prefer first)**: Use for all library documentation, code examples, and setup steps. Always check Context7 before searching the web
- **Issue tracking**: Check available MCPs (Linear, Jira). If both available, ask user which to use
- **GitHub CLI (`gh`)**: Use for PR and issue management operations

### Code Comments and Logging
- **Prefer self-documenting code**: Variable and function names should clearly express intent
- **Minimal comments**: Only add comments to explain "why" decisions were made, not "what" code does
- **Minimal logging**: Avoid excessive console.log, puts, or debug statements. Only log critical information or errors

---

## Learning & Documentation

### Capture Knowledge
- **Keep a Journal**: Track insights, decisions, and learnings throughout development
- **Document Architecture Decisions**: Record why architectural choices were made, not just what was built
- **Technical Learnings**: Note lessons learned from debugging, performance issues, or complex implementations
- **Pattern Recognition**: Document recurring patterns or anti-patterns discovered in the codebase

### Decision Records
- When making significant technical decisions, document:
  - The problem or requirement
  - Options considered
  - Trade-offs evaluated
  - Final decision and rationale
- Keep records in project documentation or comments for future reference

### Knowledge Sharing
- Update README and documentation when adding features
- Add comments for complex business logic or non-obvious implementations
- Create or update runbooks for operational procedures

---

## Implementation Checklist

Before completing any code change:
- [ ] Working on a feature branch (never commit to main)
- [ ] Tests are written and passing
- [ ] Code follows existing project conventions
- [ ] External dependencies are mocked appropriately
- [ ] Error handling is implemented
- [ ] Code is self-documenting with minimal necessary comments
- [ ] Linter has been run and issues resolved
- [ ] Commit message is clear and descriptive

---

For detailed language-specific guidelines, see the [guidelines/](guidelines/) directory.
