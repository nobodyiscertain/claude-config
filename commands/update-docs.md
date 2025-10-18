---
description: Review recent commits and propose documentation updates for outdated or missing content
allowed-tools: Bash, Read, Edit, Grep, Glob
---

You are about to review recent code changes and propose updates to documentation files.

## Your Task

### 1. Discover Documentation Structure

**First, identify all documentation files in the repository:**

```bash
# Find main documentation files (case-insensitive)
find . -maxdepth 1 -iname 'readme*' -o -iname 'claude.md' -o -iname 'agents.md'

# Find docs directory if it exists
find . -type d -name 'docs' -o -name 'documentation'

# Find nested READMEs in subdirectories (excluding node_modules, .git, vendor, etc.)
find . -type f -iname 'readme*' -not -path '*/node_modules/*' -not -path '*/.git/*' -not -path '*/vendor/*' -not -path '*/dist/*' -not -path '*/build/*'

# Look for other common doc files
find . -maxdepth 2 -type f \( -iname 'contributing*' -o -iname 'changelog*' -o -iname 'architecture*' -o -iname 'api.md' \)
```

**On first run in this repository:**
- If the main README doesn't have a "Documentation" section, propose adding one that lists all discovered documentation files
- This helps future runs know what to check
- Structure it as a reference guide to the repo's documentation

### 2. Analyze Recent Changes

**Get recent commit history:**
```bash
git log --oneline -$1  # Use $1 argument if provided, default to 20
```

If no argument provided, use 20 commits as default.

**Review what changed:**
```bash
# Get detailed diff for each commit to understand scope
git log -$1 --stat
git log -$1 --name-status
```

**Identify patterns in changes:**
- New features, APIs, or functionality added
- New files, directories, or modules created
- Renamed, moved, or deleted components
- Dependency updates (package.json, Gemfile, requirements.txt, go.mod, etc.)
- Configuration changes
- Workflow or process changes
- New scripts or commands
- Architecture or structural changes

### 3. Review Discovered Documentation Files

**For each documentation file found, check if it needs updates:**

**Common Documentation Files:**

1. **README.md** - Main repository documentation
   - Project overview and purpose
   - Installation/setup instructions
   - Dependencies and prerequisites
   - Usage examples and getting started
   - Repository structure/organization
   - Available commands or scripts
   - Contributing guidelines (if not separate)
   - Links to other documentation

2. **CLAUDE.md** - Project-specific AI agent instructions
   - Repository overview and context
   - Development workflow
   - Key files and structure
   - Testing and deployment
   - Project-specific conventions

3. **AGENTS.md** - Core agent guidelines (if present)
   - Workflow instructions
   - Tool and process references

4. **docs/** or **documentation/** directory
   - API documentation
   - Architecture guides
   - Feature documentation
   - Tutorials and guides
   - Deployment procedures

5. **Nested READMEs** - Module/component-specific docs
   - Purpose and scope of the module
   - API or interface documentation
   - Usage examples specific to that module

6. **Other common files:**
   - CONTRIBUTING.md
   - CHANGELOG.md
   - ARCHITECTURE.md
   - API.md

### 4. Identify Documentation Gaps

For each documentation file, check if it needs updates based on recent commits:

- **Missing features**: New functionality, APIs, or commands not documented
- **Outdated instructions**: Setup/installation steps that no longer work or have changed
- **Incorrect paths**: File/directory paths that have changed
- **Missing dependencies**: New packages, tools, or system requirements not listed
- **Removed features**: Documentation for features/files that no longer exist
- **Missing examples**: New usage patterns not demonstrated
- **Broken links**: References to moved/renamed/deleted files
- **Outdated architecture**: Structural changes not reflected in docs

### 5. Propose Specific Updates

**DO NOT make any changes yet!**

Present a clear proposal showing:

```markdown
## Documentation Updates Needed

### README.md (or path/to/README.md)

**Section: [Section Name] (Line X-Y)**
- Issue: [What's outdated or missing]
- Proposed change: [What should be updated]
- Reason: [Commit/change that requires this update]

**Section: [Another Section] (Line X-Y)**
- Issue: [What's outdated or missing]
- Proposed change: [What should be updated]
- Reason: [Commit/change that requires this update]

### CLAUDE.md

**Section: [Section Name] (Line X-Y)**
- Issue: [What's outdated or missing]
- Proposed change: [What should be updated]
- Reason: [Commit/change that requires this update]

### docs/api.md

**Section: [Section Name] (Line X-Y)**
- Issue: [What's outdated or missing]
- Proposed change: [What should be updated]
- Reason: [Commit/change that requires this update]

### NEW: Proposed Documentation Section

**File: README.md**
**Location: After [existing section name]**
- Proposal: Add "Documentation" section listing all discovered docs
- Content:
  ```
  ## Documentation

  - [README.md](README.md) - Main project documentation
  - [CLAUDE.md](CLAUDE.md) - AI agent instructions
  - [docs/](docs/) - Additional documentation
    - [API.md](docs/API.md) - API reference
    - [Architecture.md](docs/Architecture.md) - System architecture
  - [src/components/README.md](src/components/README.md) - Component documentation
  ```
- Reason: No documentation index currently exists

---

## Summary
- Total files needing updates: X
- New features to document: Y
- Outdated sections to fix: Z
- New sections to add: N

Would you like me to proceed with these updates? (y/n)
```

### 6. Wait for Approval

**STOP and wait for the user to respond.**

If they say yes/y/proceed:
- Make the proposed changes using the Edit tool
- Update each file as outlined in the proposal
- Confirm completion

If they say no or want modifications:
- Adjust the proposal based on feedback
- Present revised proposal
- Wait for approval again

### 7. Make Updates (Only After Approval)

Once approved, for each documentation file:

1. **Make precise edits**
   - Use Edit tool to update specific sections
   - Preserve existing formatting and style
   - Keep changes minimal and focused

2. **Verify changes**
   - Read the updated sections to confirm accuracy
   - Ensure no formatting issues

3. **Summarize completion**
   - List all files updated
   - Confirm all proposed changes were applied

## Important Guidelines

### What to Look For

**New Features/APIs:**
- New classes, functions, endpoints, or modules
- New CLI commands or scripts
- New configuration options
- Verify they're documented with examples

**Dependency Changes:**
- Package managers: package.json, Gemfile, requirements.txt, go.mod, Cargo.toml, etc.
- System dependencies or tools required
- Version updates that might affect setup/usage
- Verify installation/setup instructions are current

**Structural Changes:**
- New directories or reorganization
- Moved or renamed files/modules
- Changes to build/deployment structure
- Migration from one tool/framework to another

**Workflow Changes:**
- New development processes or conventions
- Build, test, or deployment procedure changes
- New git workflow practices
- CI/CD pipeline modifications

### What NOT to Do

- ❌ Don't make changes without explicit approval
- ❌ Don't assume what the user wants - propose specific changes
- ❌ Don't update version numbers or dates without confirming
- ❌ Don't remove documentation unless feature is clearly deleted
- ❌ Don't change writing style or rewrite sections unnecessarily
- ❌ Don't add new sections without proposing them first

### Quality Standards

**Proposed changes should:**
- ✅ Be specific with file names and line numbers
- ✅ Explain WHY the update is needed
- ✅ Show WHAT will change (old → new)
- ✅ Be organized by documentation file
- ✅ Include a summary with counts

**When making approved updates:**
- ✅ Preserve existing formatting and markdown style
- ✅ Match the tone and voice of existing documentation
- ✅ Keep changes minimal - only update what's needed
- ✅ Maintain alphabetical ordering if present
- ✅ Update table of contents if affected

## Example Workflows

### Example 1: Web API Project

```bash
# User runs: /update-docs 30

# You:
1. Discover docs:
   - README.md
   - CLAUDE.md
   - docs/api.md
   - docs/architecture.md
2. Analyze commits: git log --oneline -30
3. Identify changes:
   - New /api/v2/users endpoint added
   - Migrated from REST to GraphQL for some endpoints
   - Added Redis caching layer
   - Updated Node.js version requirement
4. Find gaps:
   - README missing Redis in dependencies
   - docs/api.md doesn't document /api/v2/users
   - CLAUDE.md architecture section outdated (no mention of GraphQL)
   - Node version in README still shows v16, now requires v20
5. Present proposal with specific line numbers and changes
6. WAIT for approval
7. After approval: make the edits
8. Confirm completion
```

### Example 2: Library/Package Project

```bash
# User runs: /update-docs

# You:
1. Discover docs:
   - README.md
   - docs/getting-started.md
   - examples/README.md
2. Analyze last 20 commits (default)
3. Identify changes:
   - Added new `transform()` function to API
   - Deprecated `convert()` function
   - Breaking change: renamed config option `timeout` to `requestTimeout`
4. Find gaps:
   - README doesn't list `transform()` function
   - No deprecation notice for `convert()`
   - Breaking changes not documented in README
   - examples/README.md still uses old `timeout` config
5. Propose updates including:
   - Adding `transform()` to API section
   - Adding deprecation warning for `convert()`
   - Adding migration guide for v2.0 breaking changes
   - Updating all examples to use `requestTimeout`
6. WAIT for approval
7. Update all docs
8. Confirm completion
```

### Example 3: First Run (No Documentation Index)

```bash
# User runs: /update-docs 10

# You:
1. Discover docs:
   - README.md (no "Documentation" section found)
   - CLAUDE.md
   - docs/deployment.md
   - docs/testing.md
   - src/components/README.md
2. Analyze last 10 commits
3. Identify changes:
   - New testing utilities added
   - CI/CD pipeline updated
4. Find gaps:
   - No centralized documentation index
   - docs/testing.md doesn't mention new test utilities
   - README doesn't link to nested docs
5. Propose updates including:
   - NEW: Add "Documentation" section to README listing all docs
   - Update docs/testing.md with new utilities
   - Add links from README to specialized docs
6. WAIT for approval
7. Make updates
8. Confirm completion
```

## Usage Examples

```bash
# Review last 20 commits (default)
/update-docs

# Review last 50 commits
/update-docs 50

# Review last 10 commits (smaller scope)
/update-docs 10
```

---

Now, execute this workflow. Start by analyzing recent commits and identifying documentation gaps. Then present a detailed proposal and wait for approval before making any changes.
