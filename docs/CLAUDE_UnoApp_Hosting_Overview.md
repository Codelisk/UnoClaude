# UnoApp Hosting Architecture Overview

## Introduction
The UnoApp utilizes the Uno.Extensions.Hosting framework to provide a robust application hosting infrastructure. This document explains the core hosting concepts and architecture patterns used throughout the application.

## IHost Pattern
The hosting architecture is built around the `IHost` interface from Microsoft.Extensions.Hosting, which provides:
- Dependency injection container
- Configuration management
- Logging infrastructure
- Application lifetime management
- Environment-specific configurations

## Application Entry Point
The hosting initialization begins in `App.cs`:

```csharp
public partial class App : Application
{
    protected Window? MainWindow { get; private set; }
    protected IHost? Host { get; private set; }

    protected override async void OnLaunched(LaunchActivatedEventArgs args)
    {
        // Application startup logic
    }
}
```

## Builder Pattern
The application uses the builder pattern for configuration:

### 1. IApplicationBuilder Creation
```csharp
var builder = this.CreateBuilder(args)
```
This extension method creates an `IApplicationBuilder` from the `Application` instance.

### 2. Host Configuration
The builder is configured using a fluent API:
```csharp
.Configure(host =>
    host.AddEnvironment()
        .AddLogging()
        .AddConfig()
        .AddLocalization()
        .ConfigureServices((context, services) => { })
)
```

### 3. Window Management
The main window is managed by the builder:
```csharp
MainWindow = builder.Window;
```

### 4. Host Building and Navigation
```csharp
Host = await builder.NavigateAsync<Shell>();
```

## Service Lifetime
- **Singleton**: Services that live for the entire application lifetime
- **Scoped**: Services that live for a specific scope (typically a navigation context)
- **Transient**: Services created each time they're requested

## Environment Support
The hosting system supports multiple environments:
- Development
- Staging
- Production
- Custom environments

Environment configuration affects:
- Logging levels
- Configuration sources
- Debug features
- Service registrations

## Integration Points
The hosting system integrates with:
- Uno.Extensions.Navigation for page navigation
- Uno.Extensions.Configuration for settings management
- Shiny.Mediator for CQRS pattern implementation
- Authentication services for secure API access

## Best Practices
1. Register services in the appropriate extension methods
2. Use environment-specific configurations sparingly
3. Leverage the built-in DI container for all dependencies
4. Initialize long-running services using IHostedService
5. Keep the startup process lightweight for better performance