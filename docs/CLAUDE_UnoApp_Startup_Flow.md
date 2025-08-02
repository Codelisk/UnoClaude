# UnoApp Startup Flow Documentation

## Overview
This document details the complete startup sequence of the UnoApp, from application launch to the first page display.

## Startup Sequence

### 1. Application Constructor
```csharp
public App()
{
    this.InitializeComponent();
}
```
- Initializes XAML-defined resources
- Sets up the application-level styles and themes
- Prepares the application for the launch sequence

### 2. OnLaunched Event Handler
The main startup logic executes when the application launches:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs args)
{
    // Startup sequence begins here
}
```

### 3. Builder Creation
```csharp
var builder = this.CreateBuilder(args)
```
- Creates an IApplicationBuilder instance
- Passes launch arguments for platform-specific initialization
- Establishes the foundation for service configuration

### 4. Navigation Support
```csharp
.UseToolkitNavigation()
```
- Enables navigation support for Uno Toolkit controls
- Registers TabBar and NavigationView navigation handlers
- Sets up the navigation infrastructure

### 5. Host Configuration
```csharp
.Configure(host => ConfigureHost(host))
```

The `ConfigureHost` method is defined in `App.xaml.host.cs`:

```csharp
// In App.xaml.host.cs
private static void ConfigureHost(IHostBuilder host)
{
    host.AddEnvironment()
        .AddLogging()
        .AddConfig()
        .AddLocalization()
        .ConfigureServices((context, services) =>
        {
            services.AddCommonServices(context.Configuration);
        })
        .UseNavigation(RegisterRoutes);
}
```

#### Configuration Steps:
1. **AddEnvironment()**: Loads .env files and sets environment variables
2. **AddLogging()**: Configures logging with environment-specific levels
3. **AddConfig()**: Sets up configuration providers (appsettings.json)
4. **AddLocalization()**: Enables multi-language support
5. **ConfigureServices()**: Registers application services
6. **UseNavigation()**: Sets up navigation routes and view mappings

### 6. Window Initialization
```csharp
MainWindow = builder.Window;

#if DEBUG
MainWindow.UseStudio();
#endif
MainWindow.SetWindowIcon();
```
- Assigns the builder's window as the main window
- Enables design-time tools in debug mode
- Sets the application icon

### 7. Initial Navigation
```csharp
Host = await builder.NavigateAsync<Shell>();
```
- Builds the IHost instance
- Navigates to the Shell (root container)
- Returns the fully initialized Host

## Service Registration Flow

### AddCommonServices
```csharp
services.TryAddScoped<BaseServices>();
services.AddGeneratedServices();
services.AddHttpClient();
services.AddSingleton<IAuthenticationService, AuthenticationService>();
services.AddShinyMediator(cfg => 
{
    cfg.UseUno();
    cfg.AddUnoPersistentCache();
});
services.AddDiscoveredMediatorHandlersFromUnoApp();
services.AddSingleton<IHttpRequestDecorator, BearerAuthenticationHttpDecorator>();
```

## Route Registration
The `RegisterRoutes` method is defined in `App.xaml.host.cs` and configures the application's navigation structure:

```csharp
private static void RegisterRoutes(IViewRegistry views, IRouteRegistry routes)
{
    // View registrations
    views.Register(
        new ViewMap(ViewModel: typeof(ShellViewModel)),
        new ViewMap<LoginPage, LoginPageViewModel>(),
        // ... additional view mappings
    );

    // Route hierarchy
    routes.Register(
        new RouteMap("", View: views.FindByViewModel<ShellViewModel>(),
            Nested: [
                new("Login", View: views.FindByViewModel<LoginPageViewModel>()),
                new("Register", View: views.FindByViewModel<RegisterPageViewModel>()),
                new(PageNames.Main, View: views.FindByViewModel<MainViewModel>(),
                    Nested: [
                        // Nested routes for main area
                    ]
                )
            ]
        )
    );
}
```

## Startup Completion
Once `NavigateAsync<Shell>()` completes:
1. The Shell page is displayed
2. All services are available via DI
3. Navigation system is ready
4. Application is fully initialized

## Error Handling
The startup process should handle:
- Missing configuration
- Service registration failures
- Navigation initialization errors
- Platform-specific issues

## Performance Considerations
- Keep service registration lightweight
- Defer heavy initialization to IHostedService
- Use lazy loading for non-critical services
- Minimize work in the constructor