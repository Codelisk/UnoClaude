# GitHub Ticket Analysis Workflow

## Overview
This workflow provides a structured approach for analyzing GitHub tickets and generating code for the UnoClaude project.

## Workflow Execution

### Step 1: Initial Analysis
When you receive a GitHub ticket for analysis, follow these steps:

1. **Read the ticket content** thoroughly
2. **Execute the initial analysis** using the template in `templates/initial-analysis.json`
3. **Create the output file** `Ticketprotokoll/grobanalyse.json` following the schema

### Step 2: Conditional Execution
Based on the analysis results in `grobanalyse.json`, execute the appropriate handlers:

- If `entitiesChanged == true` → Execute `handlers/entities-handler.json`
- If `newApiEndpoints == true` → Execute `handlers/api-handler.json`
- If `uiChanges == true` → Execute `handlers/ui-handler.json`
- If `uiLogicChanges == true` → Execute `handlers/uilogic-handler.json`
- If `newLibraries == true` → Create `Ticketprotokoll/thirdparty.txt`

## Claude Code Instructions

### Starting the Workflow

```markdown
I have a GitHub ticket to analyze: [ticket content]
Please execute the GitHub Ticket Analysis Workflow from .claude/workflows/github-ticket-analysis/
```

### Analysis Phase
1. Create the directory `Ticketprotokoll/` if it doesn't exist
2. Analyze the ticket according to `templates/initial-analysis.json`
3. Generate `Ticketprotokoll/grobanalyse.json` with the analysis results
4. Validate against `schemas/grobanalyse.schema.json`

### Implementation Phase
For each true condition in the analysis:
1. Load the corresponding handler from `handlers/`
2. Follow the step-by-step instructions in the handler
3. Reference the code samples and patterns as specified
4. Log progress to the specified log files

## Handler Execution Order

1. **Entities** (if needed) - Create/modify data models first
2. **API** (if needed) - Create endpoints that use the entities
3. **UI** (if needed) - Create pages and visual structure
4. **UI Logic** (if needed) - Implement ViewModels and business logic

## Important Patterns

### Entity Creation
- Follow schema in `../../codesamples/entity.md`
- Place in appropriate folder under `src/Models/`

### API Endpoints
- Use Shiny Mediator pattern
- Follow schema in `../../codesamples/mediator/aspnethandler.md`
- Place handlers in `src/Mediator/Handlers/`

### UI Pages
- All pages inherit from `BasePage`
- Use UnoRegions for content areas
- Every XAML element must have an explicit style

### ViewModels
- Use Uno.Extensions.Reactive patterns
- Implement IFeed for collections
- Use Shiny Mediator for all business logic

## Validation Checklist

After completing the workflow:
- [ ] All generated code compiles without errors
- [ ] Naming conventions are followed
- [ ] Proper folder structure is maintained
- [ ] All UI elements have explicit styles
- [ ] Mediator pattern is used for business logic
- [ ] Documentation comments are included
- [ ] Error handling is implemented

## Output Structure

```
Ticketprotokoll/
├── grobanalyse.json        # Initial analysis results
├── entities-changes.log    # Entity modification log
├── api-changes.log         # API endpoint log
├── ui-changes.log          # UI changes log
├── uilogic-changes.log     # UI logic implementation log
└── thirdparty.txt         # Required libraries (if any)
```

## Tips for Claude Code

1. **Use TodoWrite** to track workflow progress
2. **Batch operations** when possible for efficiency
3. **Validate schemas** before proceeding to implementation
4. **Reference existing code** for consistency
5. **Test compilation** after each major change

## Error Handling

If any step fails:
1. Log the error in the appropriate log file
2. Attempt to resolve the issue
3. If unresolvable, document in the log and continue with other tasks
4. Report all issues in the final summary