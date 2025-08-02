# UnoApp Service Registration Documentation

## Overview
This document explains the service registration process in UnoApp, detailing how services are configured and made available through dependency injection.

## HostBuilderExtensions
The `HostBuilderExtensions` class provides modular extension methods for configuring different aspects of the host:

### AddEnvironment()
```csharp
internal static IHostBuilder AddEnvironment(this IHostBuilder hostBuilder)
{
    // Load environment variables from .env file if present
    DotEnvLoader.Load();
    
#if DEBUG
    // Switch to Development environment when running in DEBUG
    hostBuilder = hostBuilder.UseEnvironment(Environments.Development);
#endif
    return hostBuilder;
}
```
**Purpose**: 
- Loads environment variables from .env files
- Sets Development environment in DEBUG builds
- Enables environment-specific configurations

### AddLogging()
```csharp
internal static IHostBuilder AddLogging(this IHostBuilder hostBuilder)
{
    return hostBuilder.UseLogging(
        configure: (context, logBuilder) =>
        {
            logBuilder
                .SetMinimumLevel(
                    context.HostingEnvironment.IsDevelopment()
                        ? LogLevel.Information
                        : LogLevel.Warning
                )
                .CoreLogLevel(LogLevel.Warning);
        },
        enableUnoLogging: true
    );
}
```
**Purpose**:
- Configures log levels based on environment
- Development: Information level
- Production: Warning level
- Enables Uno Platform specific logging

**Available Log Filters** (commented out by default):
- `XamlLogLevel()`: Generic XAML events
- `XamlLayoutLogLevel()`: Layout specific messages
- `StorageLogLevel()`: Storage messages
- `XamlBindingLogLevel()`: Binding related messages
- `BinderMemoryReferenceLogLevel()`: Memory reference tracking
- `HotReloadCoreLogLevel()`: DevServer and HotReload
- `WebAssemblyLogLevel()`: JS interop debugging

### AddConfig()
```csharp
internal static IHostBuilder AddConfig(this IHostBuilder hostBuilder)
{
    return hostBuilder
        .UseConfiguration(configure: configBuilder =>
            configBuilder
                .EmbeddedSource<App>()
                .Section<AppConfig>()
        )
        .ConfigureAppConfiguration((context, configBuilder) =>
        {
            if (OperatingSystem.IsBrowser())
            {
                // WebAssembly specific configuration
                configBuilder.AddJsonFile("appsettings.wasm.json", optional: true, reloadOnChange: false);
            }
            else
            {
                // Add environment variables
                configBuilder.AddEnvironmentVariables();
            }
        });
}
```
**Purpose**:
- Loads embedded configuration resources
- Binds configuration to AppConfig type
- Platform-specific configuration loading
- Environment variable support

### AddLocalization()
```csharp
internal static IHostBuilder AddLocalization(this IHostBuilder hostBuilder)
{
    return hostBuilder.UseLocalization();
}
```
**Purpose**:
- Enables localization support
- Configures resource management
- Sets up culture detection

## ServicesExtensions
The `ServicesExtensions` class handles the registration of application services:

### AddCommonServices()
```csharp
internal static void AddCommonServices(
    this IServiceCollection services,
    IConfiguration configuration
)
{
    // Base services registration
    services.TryAddScoped<BaseServices>();
    
    // Auto-register services with [Service] attribute
    services.AddGeneratedServices();
    
    // HTTP client factory
    services.AddHttpClient();
    
    // Authentication
    services.AddSingleton<IAuthenticationService, AuthenticationService>();
    
    // Shiny Mediator with Uno integration
    services.AddShinyMediator(cfg => 
    {
        cfg.UseUno();
        cfg.AddUnoPersistentCache();
    });
    
    // Auto-discover mediator handlers
    services.AddDiscoveredMediatorHandlersFromUnoApp();
    
    // HTTP authentication decorator
    services.AddSingleton<IHttpRequestDecorator, BearerAuthenticationHttpDecorator>();
}
```

### Service Registration Details

#### BaseServices
- **Lifetime**: Scoped
- **Purpose**: Common services used across view models
- **Usage**: Injected into view models for shared functionality

#### Generated Services
- **Pattern**: Uses source generators to find [Service] attributes
- **Registration**: Automatic based on attribute decoration
- **Example**:
```csharp
[Service]
public class MyService : IMyService
{
    // Automatically registered
}
```

#### Authentication Service
- **Lifetime**: Singleton
- **Interface**: IAuthenticationService
- **Implementation**: AuthenticationService
- **Purpose**: Manages user authentication state and tokens

#### Shiny Mediator
- **Configuration**:
  - `UseUno()`: Integrates with Uno Platform
  - `AddUnoPersistentCache()`: Enables caching for mediator requests
- **Pattern**: CQRS/Mediator for decoupled communication
- **HTTP Support**: Built-in HTTP client integration

#### HTTP Request Decorator
- **Type**: BearerAuthenticationHttpDecorator
- **Purpose**: Automatically adds Bearer tokens to HTTP requests
- **Integration**: Works with Shiny.Mediator.Http

## Service Lifetimes

### Singleton
- Created once per application
- Examples: AuthenticationService, HttpRequestDecorator
- Use for: Stateless services, caches, configuration

### Scoped
- Created once per scope (navigation context)
- Examples: BaseServices
- Use for: View model dependencies, request-specific data

### Transient
- Created each time requested
- Use for: Lightweight, stateless operations

## Best Practices

1. **Use Appropriate Lifetimes**
   - Singleton for app-wide state
   - Scoped for navigation-specific state
   - Transient for stateless operations

2. **Leverage Code Generation**
   - Use [Service] attribute for automatic registration
   - Use source generators for mediator handlers

3. **Platform-Specific Services**
   - Check platform before registration
   - Use conditional compilation when needed

4. **Configuration Integration**
   - Bind configuration sections to strongly-typed classes
   - Use IOptions<T> pattern for configuration

5. **Testing Considerations**
   - Design services with interfaces
   - Make services mockable
   - Avoid static dependencies