# Claude Configuration for UnoClaude Project

This file provides guidance for Claude Code when working with the UnoClaude project. It references specific documentation files for different architectural components.

## Documentation Structure

- [Architecture Overview](./docs/architecture/README.md) - Pages, Regions, and Navigation patterns
- [ViewModels and Models](./docs/mvvm/README.md) - Uno.Extensions.Reactive and MVUX patterns
- [XAML Styling Guidelines](./docs/styling/README.md) - Consistent styling approach
- [Services Architecture](./docs/services/README.md) - Service layer and Mediator pattern implementation

### Uno Hosting & Configuration
- [App Startup Structure](./docs/uno-hosting/CLAUDE_App_Startup_Structure.md) - Uno app initialization and hosting
- [Navigation Registration](./docs/uno-hosting/CLAUDE_Navigation_Registration.md) - Route and view registration patterns
- [Service Registration Pattern](./docs/uno-hosting/CLAUDE_Service_Registration_Pattern.md) - DI and service configuration

## Quick Reference

### Project Structure
```
UnoClaude/
├── Claude.md (this file)
├── docs/
│   ├── architecture/
│   ├── mvvm/
│   ├── styling/
│   └── services/
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
    └── Services/
```

### Key Principles
1. **Pages and Regions**: All pages inherit from BasePage and use UnoRegions
2. **MVVM Pattern**: Extensive use of Uno.Extensions.Reactive
3. **Styling**: Every XAML control must have an explicit style
4. **Services**: Use Shiny Mediator Pattern for API and messaging

For detailed information, refer to the specific documentation files linked above.