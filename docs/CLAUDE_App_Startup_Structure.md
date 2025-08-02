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
    protected Window? MainWindow { get; private set; }
    protected IHost? Host { get; private set; }

    public App()
    {
        this.InitializeComponent();
    }

    protected override async void OnLaunched(LaunchActivatedEventArgs args)
    {
        // Create the builder with launch args
        var builder = this.CreateBuilder(args)
            .UseToolkitNavigation()
            .Configure(host => ConfigureHost(host));

        // Set window and configure for debug
        MainWindow = builder.Window;
#if DEBUG
        MainWindow.UseStudio();
#endif
        MainWindow.SetWindowIcon();

        // Navigate to Shell
        Host = await builder.NavigateAsync<Shell>();
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

```csharp
public partial class App : Application
{
    private static void ConfigureHost(IHostBuilder host)
    {
        host.AddEnvironment()
            .AddLogging()
            .AddConfig()
            .AddLocalization()
            .AddSerialization()
            .AddHttp()
            .ConfigureServices((context, services) =>
            {
                services.AddCommonServices(context.Configuration);
            })
            .UseNavigation(RegisterRoutes);
    }

    private static void RegisterRoutes(IViewRegistry views, IRouteRegistry routes)
    {
        // View registrations
        views.Register(
            new ViewMap(ViewModel: typeof(ShellViewModel)),
            new ViewMap<LoginPage, LoginPageViewModel>(),
            new ViewMap<RegisterPage, RegisterPageViewModel>(),
            new ViewMap<MainPage, MainViewModel>(),
            new ViewMap<Page1, Page1ViewModel>(),
            new ViewMap<Region1, Region1ViewModel>()
        );

        // Route hierarchy
        routes.Register(
            new RouteMap(
                "",
                View: views.FindByViewModel<ShellViewModel>(),
                Nested:
                [
                    new(
                        PageNames.Main,
                        View: views.FindByViewModel<MainViewModel>(),
                        Nested:
                        [
                            new(
                                PageNames.Page2,
                                View: views.FindByViewModel<Page1ViewModel>(),
                                Nested:
                                [
                                    new(
                                        RegionViewsNames.Region1,
                                        View: views.FindByViewModel<Region1ViewModel>()
                                    )
                                ]
                            )
                        ]
                    )
                ]
            )
        );
    }
}
```

## Service Registration Pattern

Services are registered through extension methods:

```csharp
public static class ServicesExtensions
{
    public static IServiceCollection AddCommonServices(
        this IServiceCollection services, 
        IConfiguration configuration)
    {
        // Base services
        services.TryAddScoped<BaseServices>();
        
        // Generated services
        services.AddGeneratedServices();
        
        // HTTP client
        services.AddHttpClient();
        
        // Authentication
        services.AddSingleton<IAuthenticationService, AuthenticationService>();
        
        // Shiny Mediator
        services.AddShinyMediator(cfg => 
        {
            cfg.UseUno();
            cfg.AddUnoPersistentCache();
        });
        
        // Discover mediator handlers
        services.AddDiscoveredMediatorHandlersFromUnoApp();
        
        // HTTP decorators
        services.AddSingleton<IHttpRequestDecorator, BearerAuthenticationHttpDecorator>();
        
        return services;
    }
}
```

## Navigation Structure

The navigation follows a hierarchical pattern:

```
Shell (Root Container)
├── Login Page
├── Register Page
└── Main Page
    └── Page2
        └── Region1 (RegionView)
```

### ViewMap Registration
- ViewMaps link View types to ViewModel types
- Use generic syntax: `new ViewMap<TView, TViewModel>()`
- Or object syntax: `new ViewMap(ViewModel: typeof(TViewModel))`

### RouteMap Registration
- Routes are nested using the `Nested` parameter
- Each route has a path segment (empty string for root)
- Views are resolved using `views.FindByViewModel<TViewModel>()`

## Best Practices

1. **Keep App.xaml.cs minimal** - Only startup essentials
2. **All configuration in App.xaml.host.cs** - Service registration, route setup
3. **Use extension methods** - For modular service registration
4. **Follow naming conventions**:
   - Pages: `PageNames.PageName`
   - Regions: `RegionViewsNames.RegionName`
5. **Register services by lifecycle**:
   - Singleton for app-wide services
   - Scoped for navigation-scoped services
   - Transient for lightweight, stateless services

## Example Implementation

When adding a new feature:

1. Create the View and ViewModel
2. Register ViewMap in `RegisterRoutes`
3. Add RouteMap in appropriate hierarchy
4. Register any required services in `AddCommonServices`

```csharp
// Add ViewMap
new ViewMap<NewFeaturePage, NewFeatureViewModel>()

// Add Route
new(
    PageNames.NewFeature,
    View: views.FindByViewModel<NewFeatureViewModel>()
)
```