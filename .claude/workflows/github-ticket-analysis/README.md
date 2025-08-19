# GitHub Ticket Analysis Workflow System

## Overview

This directory contains a structured workflow system for analyzing GitHub tickets and automatically generating code for the UnoClaude project. The workflow replaces the previous unstructured markdown files with a JSON-based configuration system that Claude Code can execute deterministically.

## Directory Structure

```
github-ticket-analysis/
├── config.json              # Main workflow configuration
├── workflow.md              # Execution guide for Claude Code
├── templates/               # Analysis templates
│   └── initial-analysis.json
├── handlers/                # Component-specific handlers
│   ├── entities-handler.json
│   ├── api-handler.json
│   ├── ui-handler.json
│   └── uilogic-handler.json
├── schemas/                 # JSON schemas for validation
│   └── grobanalyse.schema.json
└── README.md               # This file
```

## Features

### 1. Structured Analysis
- **Systematic ticket evaluation** using predefined questions
- **JSON-based output** with schema validation
- **Conditional execution** based on analysis results

### 2. Component Handlers
Each handler provides step-by-step instructions for implementing specific aspects:

- **Entities Handler**: Data model creation and modification
- **API Handler**: Endpoint implementation using Shiny Mediator pattern
- **UI Handler**: Page and region creation with styling
- **UI Logic Handler**: ViewModel implementation with reactive patterns

### 3. Integration with UnoClaude Patterns
- Follows established project conventions
- References existing code samples
- Maintains consistent folder structure
- Enforces styling requirements

## Usage

### For Claude Code

1. **Start the workflow** when given a GitHub ticket:
   ```
   "Please execute the GitHub Ticket Analysis Workflow from .claude/workflows/github-ticket-analysis/"
   ```

2. **Follow the workflow steps**:
   - Perform initial analysis
   - Create `Ticketprotokoll/grobanalyse.json`
   - Execute handlers based on analysis results
   - Generate code following project patterns

### For Developers

1. **Provide the ticket**: Share the GitHub ticket content with Claude Code
2. **Request analysis**: Ask Claude Code to execute the workflow
3. **Review output**: Check generated code and logs in `Ticketprotokoll/`

## Workflow Process

### Phase 1: Analysis
1. Read GitHub ticket requirements
2. Answer analysis questions from template
3. Generate structured analysis in JSON format
4. Validate against schema

### Phase 2: Implementation
Based on analysis results, execute relevant handlers:

1. **If entities need changes** → Execute entities-handler
2. **If API endpoints needed** → Execute api-handler
3. **If UI changes needed** → Execute ui-handler
4. **If UI logic needed** → Execute uilogic-handler
5. **If libraries needed** → Create thirdparty.txt

### Phase 3: Validation
- Ensure code compiles
- Verify naming conventions
- Check style requirements
- Validate Mediator pattern usage

## Output Files

The workflow generates several output files in `Ticketprotokoll/`:

- `grobanalyse.json` - Initial analysis results
- `entities-changes.log` - Entity modification log
- `api-changes.log` - API endpoint implementation log
- `ui-changes.log` - UI changes log
- `uilogic-changes.log` - UI logic implementation log
- `thirdparty.txt` - Required third-party libraries (if any)

## Benefits Over Previous System

### Previous System (Markdown Files)
- Unstructured markdown files
- No validation
- Inconsistent execution
- Difficult to maintain

### New System (JSON Workflow)
- ✅ Structured JSON configuration
- ✅ Schema validation
- ✅ Deterministic execution
- ✅ Easy to maintain and extend
- ✅ Integration with TodoWrite for progress tracking
- ✅ Clear handler separation
- ✅ Comprehensive logging

## Extending the Workflow

To add new analysis aspects or handlers:

1. **Update config.json** with new workflow steps
2. **Create handler** in `handlers/` directory
3. **Update schema** if output format changes
4. **Document changes** in workflow.md

## Best Practices

1. **Always validate** the grobanalyse.json against schema
2. **Follow handler order**: Entities → API → UI → UI Logic
3. **Use TodoWrite** for tracking workflow progress
4. **Reference existing code** for consistency
5. **Log all changes** for traceability

## Troubleshooting

### Common Issues

1. **Schema validation fails**: Check that all required fields are present in grobanalyse.json
2. **Handler execution fails**: Verify prerequisites are met (e.g., entities exist before creating API)
3. **Code doesn't compile**: Check naming conventions and imports

### Support

For issues or improvements to the workflow system:
1. Check the workflow.md for detailed instructions
2. Review handler configurations for specific components
3. Validate output against schemas

## Version History

- **v1.0.0** - Initial structured workflow system
  - Replaced markdown-based workflows
  - Added JSON configuration
  - Implemented schema validation
  - Created component handlers