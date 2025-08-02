# UnoApp Dependencies Integration Documentation

## Overview
This document explains the third-party dependencies integrated into UnoApp and how they work together to provide a cohesive application architecture.

## Core Dependencies

### Shiny.Mediator
**Purpose**: Implements CQRS/Mediator pattern with HTTP communication support

#### Configuration
```csharp
services.AddShinyMediator(cfg => 
{
    cfg.UseUno();
    cfg.AddUnoPersistentCache();
});
```

#### Key Features
- **CQRS Pattern**: Separates commands and queries
- **HTTP Integration**: Built-in HTTP client support via Shiny.Mediator.Http
- **Caching**: Persistent cache for offline support
- **Uno Integration**: Seamless integration with Uno Platform

#### Usage Example
```csharp
// Define a request
public record GetContactsRequest : IRequest<IEnumerable<Contact>>;

// Handler implementation
public class GetContactsHandler : IRequestHandler<GetContactsRequest, IEnumerable<Contact>>
{
    public async Task<IEnumerable<Contact>> Handle(GetContactsRequest request, CancellationToken cancellationToken)
    {
        // Implementation
    }
}

// Usage in ViewModel
var contacts = await mediator.Send(new GetContactsRequest());
```

### Shiny.Extensions.DependencyInjection
**Purpose**: Provides additional DI patterns and service discovery

#### Features
- **Service Discovery**: `AddGeneratedServices()` automatically registers services
- **Attribute-Based Registration**: Use `[Service]` attribute
- **Handler Discovery**: `AddDiscoveredMediatorHandlersFromUnoApp()`

#### Service Attribute Pattern
```csharp
[Service]
public class ContactService : IContactService
{
    // Automatically registered in DI
}
```

### Uno.Resizetizer
**Purpose**: Icon and image asset management

#### Usage
```csharp
using Uno.Resizetizer;

// In App.cs
MainWindow.SetWindowIcon();
```

#### Features
- Automatic icon generation for all platforms
- Image optimization
- Splash screen generation
- App icon management

## Authentication Integration

### IAuthenticationService
```csharp
services.AddSingleton<IAuthenticationService, AuthenticationService>();
```

**Features**:
- Token management
- User session handling
- Secure credential storage

### BearerAuthenticationHttpDecorator
```csharp
services.AddSingleton<IHttpRequestDecorator, BearerAuthenticationHttpDecorator>();
```

**Purpose**: Automatically adds authentication headers to HTTP requests

**Implementation Pattern**:
```csharp
public class BearerAuthenticationHttpDecorator : IHttpRequestDecorator
{
    private readonly IAuthenticationService _authService;
    
    public async Task DecorateAsync(HttpRequestMessage request)
    {
        var token = await _authService.GetTokenAsync();
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers.Authorization = 
                new AuthenticationHeaderValue("Bearer", token);
        }
    }
}
```

## HTTP Communication Pattern

### Shiny.Mediator.Http Integration
Instead of direct HTTP client usage, the app uses Shiny.Mediator for HTTP communication:

```csharp
// Request definition
[HttpGet("/api/contacts")]
public record GetContactsApiRequest : IRequest<IEnumerable<Contact>>;

// No handler needed - Shiny.Mediator.Http handles it automatically
// Usage
var contacts = await mediator.Send(new GetContactsApiRequest());
```

### Benefits
- Automatic serialization/deserialization
- Built-in error handling
- Request/response caching
- Offline support with cache
- Automatic authentication via decorators

## Generated Code Integration

### Service Generation
The project uses source generators for:
- Service registration
- Mediator handler discovery
- Navigation view mapping

### Pattern Example
```csharp
// This attribute triggers code generation
[Service(Lifetime = ServiceLifetime.Scoped)]
public class MyService : IMyService { }

// Generated code (simplified)
services.AddScoped<IMyService, MyService>();
```

## Platform-Specific Integrations

### WebAssembly
```csharp
if (OperatingSystem.IsBrowser())
{
    // WebAssembly-specific configuration
    configBuilder.AddJsonFile("appsettings.wasm.json", optional: true);
}
```

### Debug Mode
```csharp
#if DEBUG
services.AddTransient<DelegatingHandler, DebugHttpHandler>();
MainWindow.UseStudio();
#endif
```

## Integration Best Practices

### 1. Use Mediator for All External Communication
```csharp
// Good - using mediator
var result = await mediator.Send(new GetDataRequest());

// Avoid - direct HTTP client
var result = await httpClient.GetAsync("/api/data");
```

### 2. Leverage Service Discovery
```csharp
// Good - auto-discovered
[Service]
public class MyService { }

// Avoid - manual registration
services.AddScoped<MyService>();
```

### 3. Use Decorators for Cross-Cutting Concerns
```csharp
// Authentication decorator handles tokens
// Logging decorator handles telemetry
// Caching decorator handles offline scenarios
```

### 4. Platform-Aware Code
```csharp
if (OperatingSystem.IsBrowser())
{
    // WebAssembly-specific code
}
else if (OperatingSystem.IsWindows())
{
    // Windows-specific code
}
```

## Dependency Update Strategy

### Version Management
- Use centralized version management
- Test thoroughly on all platforms
- Consider platform-specific limitations

### Breaking Changes
- Review Shiny.Mediator migration guides
- Check Uno Platform compatibility
- Validate source generator outputs

## Troubleshooting Common Issues

### Service Not Found
1. Check [Service] attribute is present
2. Verify assembly is included in discovery
3. Ensure correct lifetime is specified

### HTTP Requests Failing
1. Verify authentication service is registered
2. Check decorator is applied
3. Validate API endpoint configuration

### Navigation Not Working
1. Ensure view is registered in RegisterRoutes
2. Verify ViewModel naming convention
3. Check route path is correct