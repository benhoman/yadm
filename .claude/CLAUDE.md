# Personal Claude Code Guidelines

## Pull Request Best Practices

When creating pull requests, **always** follow these rules:

### 1. Use the Repository's PR Template

- Check for `.github/pull_request_template.md` in the repo
- Follow the template structure exactly - don't create custom formats
- Copy the template and fill in the appropriate sections

### 2. Leave Checkboxes Unchecked

- **Never** pre-check boxes in acceptance criteria or test cases
- Leave all `- [ ]` checkboxes empty for reviewers/QA team to check off
- This is part of the review process workflow

### 3. Pull Acceptance Criteria from JIRA

- **Don't create your own acceptance criteria**
- Copy the acceptance criteria directly from the JIRA ticket
- If the ticket doesn't have clear criteria, ask for clarification

### 4. Follow Template Structure Exactly

- Don't deviate from the provided template format
- Use the exact sections and formatting provided
- Maintain consistency across all pull requests

### Example Workflow

1. Read `.github/pull_request_template.md` first
2. Copy the template structure exactly  
3. Fill in JIRA ticket number and link: `# [PDW-XXXX](https://consumeraffairs.atlassian.net/browse/PDW-XXXX)`
4. Write overview based on actual changes made
5. Copy acceptance criteria from the JIRA ticket (don't create custom ones)
6. Leave all checkboxes unchecked for reviewers
7. Create clear, actionable test cases

This ensures consistency and proper workflow compliance across all repositories.

### 5. Post-PR Creation Workflow

After creating a pull request, **always** follow this workflow:

1. **Prompt user for JIRA updates:**
   ```
   Would you like me to move the ticket to Code Review and update story points?
   If yes, how many story points should I add to BE Story Points (actual)?
   ```

2. **Update JIRA ticket:**
   - Update `customfield_12750` (BE Story Points actual) with the specified value
   - Move ticket to "Code Review" status (transition ID: 431)

3. **Remind user to post in Slack:**
   ```
   Please post in #eng-prs Slack channel:

   Ticket: [TICKET-ID]
   Title: [Ticket Title]
   PR URL: [PR Link]
   ```


## JIRA Field Mappings

### Ticket Fields

- **Summary**: `summary` (string)
- **Ticket Description**: `customfield_12881` (Atlassian Document Format - ADF) - **USE THIS, NOT standard description field**
- **Acceptance Criteria**: `customfield_12819` (Atlassian Document Format - ADF)
- **Sprint Team**: `customfield_12615` (select field)
- **Description**: Leave empty/cleared - DO NOT USE this field
- **Components**: Standard JIRA components field
- **Labels**: Standard JIRA labels field

### Field Details

- `customfield_12881` = Ticket Description field (rich text in ADF format) - **PRIMARY description field**
- `customfield_12819` = Acceptance Criteria field (rich text in ADF format)
- `customfield_12615` = Sprint Team field (e.g., "Platform", "Backend", etc.)
- `customfield_12750` = BE Story Points (actual) field (numeric, accepts decimals like 0.5, 1.0, etc.)
- `summary` = Ticket title/summary (plain text)
- `description` = Standard description field - **MUST BE LEFT EMPTY**

### Ticket Status Transitions

- **Code Review** transition ID: `431` (use after creating PR)
- **Optimization**: When transitioning AND updating fields (e.g., moving to Code Review + setting story points), use a single `transitionJiraIssue` call with the `fields` parameter instead of separate calls:
  ```
  transitionJiraIssue(
      transition={"id": "431"},
      fields={"customfield_12750": 0.5}  # BE Story Points (actual)
  )
  ```

### Ticket Creation Workflow

**BEFORE creating any JIRA ticket:**

1. **Fetch available options** from JIRA metadata:
   - Get list of available Components for the project
   - Get list of available Sprint Team values (customfield_12615)
   - Get list of common Labels used in the project

2. **Propose defaults and CONFIRM with user:**
   - **Sprint Team**: Default to "Backend", but confirm with user
   - **Components**: If in a git repo, compare directory name to available components list and suggest best match, then confirm
   - **Labels**: Default to "backend", but confirm with user

3. **Example confirmation format:**
   ```
   I'm going to create a JIRA ticket with:
   - Sprint Team: Backend
   - Components: [inferred component or ask user]
   - Labels: backend

   Please confirm or let me know what these should be.
   ```

**CRITICAL RULES:**
- Put description content in `customfield_12881` (Ticket Description), NOT in `description`
- Clear/leave empty the standard `description` field
- Always populate `customfield_12819` (Acceptance Criteria)
- **NEVER create new labels, sprint teams, or components** - only use existing values from JIRA
- Always fetch and use existing values from JIRA metadata
- Never create a ticket without confirming Sprint Team, Components, and Labels first

### Usage Notes

- ADF (Atlassian Document Format) is required for rich text fields
- Use proper ADF structure with paragraphs, headings, lists, and code formatting
- Code blocks use `{"type": "text", "text": "code", "marks": [{"type": "code"}]}`
- Headings use `{"type": "heading", "attrs": {"level": 3}, "content": [...]}`

## Git Staging Guidelines

### Explicit File Staging

- **Never use `git add .`** to stage changes
- Always specify files explicitly when staging:
  - `git add specific_file.py` for individual files
  - `git add src/` for specific directories
  - `git add *.js` for file patterns when appropriate
- This ensures intentional staging and prevents accidental commits of unwanted files


## Language specific - Python

### Typing
- Always add typing where possible when writing new code.
- Use Python 3.10+ typing style when possible.

### Codestyle
- Make sure when writing the code its formatted correctly the way ruff would format it.
- Also follow all ruff linting guidelines. Use the pyproject.toml file to for project specific details.
- NEVER import a module within a function/class/module unless it's absolutely necessary to avoid a circular import error. ALWAYS prefer importing at the top of the file and sort it correctly according to the linter.
- Do the imports after implementing the code so the linter doesn't remove the import
