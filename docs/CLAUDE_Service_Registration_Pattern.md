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
    .AddLogging()
    .AddConfig()
    .AddLocalization()
    .AddSerialization()
    .AddHttp()
    .ConfigureServices((context, services) =>
    {
        services.AddCommonServices(context.Configuration);
    });
```

### 2. AddCommonServices Extension

The main service registration happens in `AddCommonServices`:

```csharp
public static IServiceCollection AddCommonServices(
    this IServiceCollection services, 
    IConfiguration configuration)
{
    // Base services
    services.TryAddScoped<BaseServices>();
    
    // Auto-discovered services
    services.AddGeneratedServices();
    
    // HTTP client factory
    services.AddHttpClient();
    
    // Authentication
    services.AddSingleton<IAuthenticationService, AuthenticationService>();
    
    // Shiny Mediator setup
    services.AddShinyMediator(cfg => 
    {
        cfg.UseUno();
        cfg.AddUnoPersistentCache();
    });
    
    // Auto-discover mediator handlers
    services.AddDiscoveredMediatorHandlersFromUnoApp();
    
    // HTTP request decorators
    services.AddSingleton<IHttpRequestDecorator, BearerAuthenticationHttpDecorator>();
    
    return services;
}
```

## Shiny Service Attributes

### [Service] Attribute

Marks a class for automatic registration:

```csharp
[Service]
public class MyService : IMyService
{
    // Implementation
}
```

### [ServiceSingleton] Attribute

Registers service as singleton:

```csharp
[ServiceSingleton]
public class CacheService : ICacheService
{
    // Singleton implementation
}
```

### [ServiceScoped] Attribute

Registers service with scoped lifetime:

```csharp
[ServiceScoped]
public class UserContextService : IUserContextService
{
    // Scoped to navigation context
}
```

## Shiny Mediator Pattern

### Request/Response Pattern

Define requests and handlers:

```csharp
// Request
public record GetUserQuery(int UserId) : IRequest<User>;

// Handler
[RegisterHandler]
public class GetUserQueryHandler : IRequestHandler<GetUserQuery, User>
{
    public async Task<User> Handle(GetUserQuery request, CancellationToken cancellationToken)
    {
        // Handle request
    }
}
```

### Event Pattern

Publish and handle events:

```csharp
// Event
public record UserLoggedInEvent(string UserId) : IEvent;

// Handler
[RegisterHandler]
public class UserLoggedInEventHandler : IEventHandler<UserLoggedInEvent>
{
    public async Task Handle(UserLoggedInEvent @event, CancellationToken cancellationToken)
    {
        // Handle event
    }
}
```

### Command Pattern

Execute commands without return values:

```csharp
// Command
public record UpdateUserCommand(int UserId, string Name) : ICommand;

// Handler
[RegisterHandler]
public class UpdateUserCommandHandler : ICommandHandler<UpdateUserCommand>
{
    public async Task Handle(UpdateUserCommand command, CancellationToken cancellationToken)
    {
        // Execute command
    }
}
```

## Middleware Configuration

Shiny Mediator supports middleware:

```csharp
services.AddShinyMediator(cfg => 
{
    cfg.UseUno();                      // Uno platform integration
    cfg.AddUnoPersistentCache();       // Persistent caching
    cfg.AddValidation();               // Request validation
    cfg.AddPerformanceLogging();       // Performance metrics
    cfg.AddExceptionHandling();        // Global exception handling
});
```

## HTTP Decorators

The app uses decorators for cross-cutting concerns:

```csharp
public class BearerAuthenticationHttpDecorator : IHttpRequestDecorator
{
    private readonly IAuthenticationService _authService;
    
    public async Task DecorateRequest(HttpRequestMessage request)
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

## Generated Services Pattern

Services marked with attributes are automatically discovered:

```csharp
// This method is generated at compile time
services.AddGeneratedServices();

// Discovers all handlers marked with [RegisterHandler]
services.AddDiscoveredMediatorHandlersFromUnoApp();
```

## Best Practices

### 1. Service Lifetime Selection
- **Singleton**: Authentication, Configuration, Caching
- **Scoped**: User context, Navigation state
- **Transient**: Lightweight operations, Stateless helpers

### 2. Handler Organization
```
/Handlers
  /Queries      - Read operations
  /Commands     - Write operations
  /Events       - Event handlers
```

### 3. Dependency Injection
```csharp
[ServiceScoped]
public class OrderService : IOrderService
{
    private readonly IMediator _mediator;
    private readonly ILogger<OrderService> _logger;
    
    public OrderService(IMediator mediator, ILogger<OrderService> logger)
    {
        _mediator = mediator;
        _logger = logger;
    }
}
```

### 4. Mediator Usage
```csharp
// In ViewModels or Services
public async Task LoadUserAsync(int userId)
{
    var user = await _mediator.Request(new GetUserQuery(userId));
    // Use user data
}
```

## Common Patterns

### 1. Repository Pattern with Mediator
```csharp
[RegisterHandler]
public class GetAllUsersQueryHandler : IRequestHandler<GetAllUsersQuery, List<User>>
{
    private readonly IUserRepository _repository;
    
    public GetAllUsersQueryHandler(IUserRepository repository)
    {
        _repository = repository;
    }
    
    public Task<List<User>> Handle(GetAllUsersQuery request, CancellationToken ct)
    {
        return _repository.GetAllAsync(ct);
    }
}
```

### 2. Caching with Mediator
```csharp
public record GetProductQuery(int ProductId) : IRequest<Product>, ICachedRequest
{
    public string CacheKey => $"product_{ProductId}";
    public TimeSpan? AbsoluteExpiration => TimeSpan.FromMinutes(5);
}
```

### 3. Validation
```csharp
[RegisterHandler]
public class CreateUserCommandValidator : AbstractValidator<CreateUserCommand>
{
    public CreateUserCommandValidator()
    {
        RuleFor(x => x.Email).NotEmpty().EmailAddress();
        RuleFor(x => x.Password).MinimumLength(8);
    }
}
```

## Registration Examples

### Manual Registration
```csharp
services.AddSingleton<IMyService, MyService>();
services.AddScoped<IRepository, Repository>();
services.AddTransient<IHelper, Helper>();
```

### Attribute-based Registration
```csharp
[ServiceSingleton(typeof(IMyService))]
public class MyService : IMyService { }

[ServiceScoped]
public class Repository : IRepository { }
```

### Factory Registration
```csharp
services.AddSingleton<IServiceFactory>(sp => 
    new ServiceFactory(sp.GetRequiredService<ILogger<ServiceFactory>>()));
```