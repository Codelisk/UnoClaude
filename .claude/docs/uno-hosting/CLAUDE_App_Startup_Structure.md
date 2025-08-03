# CLAUDE: UnoApp Startup Structure

## Overview
The UnoApp follows a clear separation of concerns for application startup. The startup logic is split between two files:
- `App.xaml.cs` - Minimal startup code with the main entry point
- `App.xaml.host.cs` - All host builder configuration and service registration

## App.xaml.cs - Main Entry Point

The `App.xaml.cs` file contains only the essential startup code:

```csharp
public partial class App : Application
{
    public App()
    {
        this.InitializeComponent();
    }

    protected override async void OnLaunched(LaunchActivatedEventArgs args)
    {
        // Create the builder with launch args
        var builder = this.CreateBuilder(args)
            .Configure(host => ConfigureHost(host));

        // Navigate to Shell
        _ = await builder.NavigateAsync<Shell>();
    }
}
```

### Key Points:
- Constructor only calls `InitializeComponent()`
- `OnLaunched` creates the builder with args
- Delegates all configuration to `ConfigureHost` method (in App.xaml.host.cs)
- Handles window setup and initial navigation

## App.xaml.host.cs - Host Configuration

All host builder logic resides in the partial class file `App.xaml.host.cs`:

## Service Registration Pattern

@CLAUDE_Service_Registration

## Navigation Pattern

@CLAUDE_Navigation_Registration

## Best Practices

1. **Keep App.xaml.cs minimal** - Only startup essentials
2. **All configuration in App.xaml.host.cs** - Service registration, route setup
3. **Use extension methods** - For modular service registration
5. **Register services by lifecycle**:
   - Singleton for app-wide services
   - Scoped for navigation-scoped services
   - Transient for lightweight, stateless services