# Claude Configuration for UnoClaude Project

This file provides guidance for Claude Code when working with the UnoClaude project. It references specific documentation files for different architectural components.

## Important Note on Documentation Sources

**Primary Documentation**: Always refer to the project-specific documentation in `.claude/docs/` first. These are tailored specifically for this project.

**Extended Documentation**: The `.claude/docs/extendeddocs/` folder contains comprehensive documentation from Uno Platform, Uno Extensions, Uno Toolkit UI, and Shiny libraries. These should be consulted ONLY when:
- You need specific implementation details not covered in the primary docs
- You're implementing advanced features that require deeper platform knowledge
- The primary documentation references a specific feature from these libraries

## Primary Documentation Structure

- [Architecture Overview](./.claude/docs/architecture/README.md) - Pages, Regions, and Navigation patterns
- [ViewModels and Models](./.claude/docs/mvvm/README.md) - Uno.Extensions.Reactive and MVUX patterns
- [XAML Styling Guidelines](./.claude/docs/styling/README.md) - Consistent styling approach
- [Services Architecture](./.claude/docs/services/README.md) - Service layer and Mediator pattern implementation

### Uno Hosting & Configuration
- [App Startup Structure](./.claude/docs/uno-hosting/CLAUDE_App_Startup_Structure.md) - Uno app initialization and hosting
- [Navigation Registration](./.claude/docs/uno-hosting/CLAUDE_Navigation_Registration.md) - Route and view registration patterns
- [Service Registration Pattern](./.claude/docs/uno-hosting/CLAUDE_Service_Registration_Pattern.md) - DI and service configuration

## Quick Reference

### Project Structure
```
UnoClaude/
├── Claude.md (this file)
├── .claude/
│   └── docs/
│       ├── architecture/
│       ├── mvvm/
│       ├── styling/
│       ├── services/
│       ├── uno-hosting/
│       └── extendeddocs/      # Reference docs - use only when needed
│           ├── shiny/          # Shiny Mediator documentation
│           ├── unoextensions/  # Uno.Extensions documentation
│           ├── unoplatform/    # Uno Platform core documentation
│           └── unotoolkitui/   # Uno Toolkit UI documentation
└── src/
    ├── Pages/
    │   ├── Main/
    │   │   ├── MainPage.xaml
    │   │   ├── Regions/
    │   │   └── ViewModels/
    │   ├── Products/
    │   │   ├── ProductsPage.xaml
    │   │   ├── Regions/
    │   │   └── ViewModels/
    │   └── Settings/
    │       ├── SettingsPage.xaml
    │       ├── Regions/
    │       └── ViewModels/
    ├── Models/
    └── Mediator/
    │   ├── Requests/
	│	│	├── IRequests/
    │   │	├── Events/
    │   │   └──Commands/
    │   └── Handlers/
    │       ├── IRequests/
    │       ├── Events/
    │       └── Commands/
    └── Services/
```

### Key Principles
1. **Pages and Regions**: All pages inherit from BasePage and use UnoRegions
2. **MVVM Pattern**: Extensive use of Uno.Extensions.Reactive
3. **Styling**: Every XAML control must have an explicit style
4. **Services**: Use Shiny Mediator Pattern for API and messaging

For detailed information, refer to the specific documentation files linked above.

## Extended Documentation Reference

When you need specific platform or library details not covered in the primary docs:

- **Shiny Mediator**: `.claude/docs/extendeddocs/shiny/` - For mediator patterns, commands, events
- **Uno Extensions**: `.claude/docs/extendeddocs/unoextensions/` - For MVUX, navigation, reactive patterns
- **Uno Platform**: `.claude/docs/extendeddocs/unoplatform/` - For platform-specific features, controls
- **Uno Toolkit UI**: `.claude/docs/extendeddocs/unotoolkitui/` - For UI controls and styling

**Remember**: Always check the primary project documentation first!