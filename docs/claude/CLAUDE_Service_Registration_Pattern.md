# CLAUDE: Service Registration Pattern with Shiny.Extensions

## Overview
The UnoApp uses Shiny.Extensions for advanced dependency injection patterns. This document explains how services are registered and the patterns used throughout the application.

## Shiny.Extensions System

Shiny.Extensions provides enhanced DI capabilities:
- Automatic service discovery via attributes
- Mediator pattern implementation (CQRS)
- Persistent caching
- Request/Response handlers
- Middleware pipeline

## Service Registration Flow

### 1. Host Builder Extensions

Services are registered through extension methods on `IHostBuilder`:

```csharp
host.AddEnvironment()
    .ConfigureServices((context, services) =>
    {
        services.AddServices(context.Configuration);
    });
```

### 2. AddServices Extension

The main service registration happens in `AddServices`:

```csharp
public static IServiceCollection AddServices(
    this IServiceCollection services, 
    IConfiguration configuration)
{
    // Shiny Mediator setup
    services.AddShinyMediator(cfg => 
    {
        cfg.UseUno();
        cfg.AddUnoPersistentCache();
		//Other ShinyMediatorServices
    });
    
    // Auto-discover mediator handlers
    services.AddDiscoveredMediatorHandlersFromUnoApp();
    
    return services;
}
```

## Shiny Service Attributes

### [Service] Attribute

Marks a class for automatic registration:

```csharp
[Service(ServiceLifetime.Singleton)]
public class MyService : IMyService
{
    // Implementation
}
```


## Best Practices

- **Singleton**: Authentication, Configuration, Caching
- **Scoped**: User context, Navigation state
- **Transient**: Lightweight operations, Stateless helpers
