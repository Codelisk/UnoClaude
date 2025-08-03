# Services Architecture with Shiny Mediator Pattern

## Service Layer Structure

### Base Service Pattern
All services should encapsulate business logic and use the Service attribute for registration:

```csharp
public interface IProductService
{
    Task<ImmutableList<Product>> GetProductsAsync(CancellationToken ct);
    Task<Product> GetProductByIdAsync(int id, CancellationToken ct);
    Task<Product> CreateProductAsync(Product product, CancellationToken ct);
}

[Service(ServiceLifetime.Singleton)]
public class ProductService : IProductService
{
    private readonly IMediator _mediator;
    
    public ProductService(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    public async Task<ImmutableList<Product>> GetProductsAsync(CancellationToken ct)
    {
        var response = await _mediator.Request(new GetProductsRequest(), ct);
        return response.Products;
    }
}
```

## Shiny Mediator Pattern

### Request/Response Pattern
Define requests and handlers for queries that return data:

```csharp
// Request
public record GetProductsRequest : IRequest<ImmutableList<Product>>
{
    public int? CategoryId { get; init; }
    public string? SearchTerm { get; init; }
}

// Handler
[SingletonHandler]
public class GetProductsHandler : IRequestHandler<GetProductsRequest, ImmutableList<Product>>
{
    private readonly IProductRepository _productRepository;
    
    public GetProductsHandler(IProductRepository productRepository)
    {
        _productRepository = productRepository;
    }
    
    public async Task<ImmutableList<Product>> Handle(GetProductsRequest request, IMediatorContext context, CancellationToken cancellationToken)
    {
        var products = await _productRepository.GetProductsAsync(
            request.CategoryId, 
            request.SearchTerm, 
            cancellationToken
        );
        
        return products.ToImmutableList();
    }
}
```

### Command Pattern
Define commands for operations that modify state:

```csharp
// Command
public record CreateProductCommand : ICommand
{
    public string Name { get; init; }
    public decimal Price { get; init; }
    public int CategoryId { get; init; }
}

// Handler
[SingletonHandler]
public class CreateProductCommandHandler : ICommandHandler<CreateProductCommand>
{
    private readonly IProductRepository _productRepository;
    private readonly IMediator _mediator;
    
    public CreateProductCommandHandler(IProductRepository productRepository, IMediator mediator)
    {
        _productRepository = productRepository;
        _mediator = mediator;
    }
    
    public async Task Handle(CreateProductCommand command, IMediatorContext context, CancellationToken cancellationToken)
    {
        var product = new Product
        {
            Name = command.Name,
            Price = command.Price,
            CategoryId = command.CategoryId
        };
        
        await _productRepository.CreateAsync(product, cancellationToken);
        
        // Publish event
        await _mediator.Publish(new ProductCreatedEvent { ProductId = product.Id });
    }
}
```

### Event Pattern
Use events for notifications and decoupled messaging:

```csharp
// Event
public record ProductCreatedEvent : IEvent
{
    public int ProductId { get; init; }
}

// Event Handler
[SingletonHandler]
public class ProductCreatedEventHandler : IEventHandler<ProductCreatedEvent>
{
    private readonly ILogger<ProductCreatedEventHandler> _logger;
    private readonly ICacheService _cacheService;
    
    public ProductCreatedEventHandler(ILogger<ProductCreatedEventHandler> logger, ICacheService cacheService)
    {
        _logger = logger;
        _cacheService = cacheService;
    }
    
    public async Task Handle(ProductCreatedEvent @event, IMediatorContext context, CancellationToken cancellationToken)
    {
        _logger.LogInformation($"Product {event.ProductId} was created");
        
        // Invalidate cache
        await _cacheService.InvalidateAsync("products", cancellationToken);
        
        // Update UI, send notifications, etc.
    }
}

// Publishing events
await _mediator.Publish(new ProductCreatedEvent { ProductId = productId });
```

### Usage Examples
```csharp
// In a ViewModel or Service
public class ProductViewModel
{
    private readonly IMediator _mediator;
    
    public ProductViewModel(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    // Query data
    public async Task<ImmutableList<Product>> LoadProductsAsync()
    {
        return await _mediator.Request(new GetProductsRequest { CategoryId = 1 });
    }
    
    // Execute command
    public async Task CreateProductAsync(string name, decimal price)
    {
        await _mediator.Send(new CreateProductCommand 
        { 
            Name = name, 
            Price = price, 
            CategoryId = 1 
        });
    }
    
    // Publish event
    public async Task NotifyProductViewedAsync(int productId)
    {
        await _mediator.Publish(new ProductViewedEvent { ProductId = productId });
    }
}
```

## Dependency Injection with Shiny

### Service Registration
Services are automatically registered using the Service attribute:

```csharp
// Service registration in App.xaml.cs
public partial class App : Application
{
    protected override void OnLaunched(LaunchActivatedEventArgs args)
    {
        var builder = this.CreateBuilder(args)
            .Configure(host => host
                .UseShiny()
                .ConfigureServices((context, services) =>
                {
                    services.AddShinyMediator(cfg => cfg.UseUno());
                    
                    // Register handlers individually
                    services.AddSingletonAsImplementedInterfaces<GetProductsHandler>();
                    services.AddSingletonAsImplementedInterfaces<CreateProductCommandHandler>();
                    services.AddSingletonAsImplementedInterfaces<ProductCreatedEventHandler>();
                    
                    // OR use source generation with [SingletonHandler] attribute
                    // services.AddDiscoveredMediatorHandlersFromUnoClaude();
                    
                    // Register services
                    services.AddSingleton<IProductService, ProductService>();
                    services.AddSingleton<IProductRepository, ProductRepository>();
                    
                    // Register background jobs if needed
                    services.AddJob<SyncProductsJob>();
                }));
                
        // Build and activate window
        var window = builder.Build().RootFrame;
    }
}
```

### Service Attribute Options
```csharp
// Singleton service (default)
[Service]
public class CacheService : ICacheService { }

// Scoped service
[Service(ServiceLifetime.Scoped)]
public class UserService : IUserService { }

// Transient service
[Service(ServiceLifetime.Transient)]
public class EmailService : IEmailService { }
```

### Handler Registration with Attributes
Use Shiny attributes for handler registration:

```csharp
// Handlers with Shiny attributes
[SingletonHandler]
public class GetProductsHandler : IRequestHandler<GetProductsRequest, ImmutableList<Product>>
{
    private readonly IProductRepository _productRepository;
    
    public GetProductsHandler(IProductRepository productRepository)
    {
        _productRepository = productRepository;
    }
    
    public async Task<ImmutableList<Product>> Handle(GetProductsRequest request, IMediatorContext context, CancellationToken cancellationToken)
    {
        // Implementation
        var products = await _productRepository.GetProductsAsync(request.CategoryId, request.SearchTerm, cancellationToken);
        return products.ToImmutableList();
    }
}

[SingletonHandler]
public class ProductViewedEventHandler : IEventHandler<ProductViewedEvent>
{
    private readonly ILogger<ProductViewedEventHandler> _logger;
    
    public ProductViewedEventHandler(ILogger<ProductViewedEventHandler> logger)
    {
        _logger = logger;
    }
    
    public Task Handle(ProductViewedEvent @event, IMediatorContext context, CancellationToken cancellationToken)
    {
        _logger.LogInformation($"Product {event.ProductId} was viewed");
        return Task.CompletedTask;
    }
}
```

### Background Jobs with Shiny
```csharp
public class SyncProductsJob : IJob
{
    private readonly IMediator _mediator;
    
    public SyncProductsJob(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    public async Task Run(JobInfo jobInfo, CancellationToken cancelToken)
    {
        await _mediator.Send(new SyncProductsRequest(), cancelToken);
    }
}
```

## Middleware Support

Shiny Mediator provides powerful middleware capabilities for cross-cutting concerns in Uno Platform applications.

### Default Middleware
When using Uno Platform extension, the following middleware is pre-installed:
- **User Notification Errors** - Displays user-friendly error messages
- **Offline Support** - Handles offline scenarios gracefully
- **Replay Stream** - Replays failed requests when back online
- **Persistent Caching** - File-based caching that survives app restarts

### Setup
```csharp
// In App.xaml.cs
public partial class App : Application
{
    protected override void OnLaunched(LaunchActivatedEventArgs args)
    {
        var builder = this.CreateBuilder(args)
            .Configure(host => host
                .UseShiny()
                .ConfigureServices((context, services) =>
                {
                    services.AddShinyMediator(cfg => 
                    {
                        // Adds default Uno middleware
                        cfg.UseUno();
                        
                        // Add additional middleware as needed
                        cfg.UsePerformanceLogging();
                        cfg.AddDataAnnotations();
                    });
                }));
    }
}
```

### Validation Middleware
Add validation to your commands and requests using Data Annotations:

```csharp
// Contract with validation
[Validate]
public record CreateProductCommand : ICommand
{
    [Required]
    public string Name { get; init; }
    
    [Range(0.01, 999999.99)]
    public decimal Price { get; init; }
    
    [Required]
    public int CategoryId { get; init; }
}

// Handle validation errors
try
{
    await _mediator.Send(new CreateProductCommand { Name = "", Price = -1 });
}
catch (ValidateException ex)
{
    // ex.Result.Errors contains validation errors by property name
    foreach (var error in ex.Result.Errors)
    {
        Console.WriteLine($"{error.Key}: {string.Join(", ", error.Value)}");
    }
}
```

### Caching Middleware
Implement caching for expensive queries:

```csharp
// Using attribute
[SingletonHandler]
[Cache(AbsoluteExpirationSeconds = 300, SlidingExpirationSeconds = 60)]
public class GetProductsHandler : IRequestHandler<GetProductsRequest, ImmutableList<Product>>
{
    public async Task<ImmutableList<Product>> Handle(
        GetProductsRequest request, 
        IMediatorContext context, 
        CancellationToken cancellationToken)
    {
        // This result will be cached
        return await _repository.GetProductsAsync(cancellationToken);
    }
}

// Force cache refresh from caller
var products = await _mediator.Request(
    new GetProductsRequest(), 
    cancellationToken,
    ctx => ctx.ForceCacheRefresh()
);

// Check if data came from cache
var response = await _mediator.Request(new GetProductsRequest());
var cacheInfo = response.Context.Cache();
if (cacheInfo?.IsHit == true)
{
    Console.WriteLine($"Data from cache, stored at: {cacheInfo.Timestamp}");
}
```

### Performance Logging
Monitor slow operations:

```csharp
// In appsettings.json
{
    "Mediator": {
        "PerformanceLogging": {
            "MyApp.Handlers.*": {
                "ErrorThresholdMilliseconds": 1000
            }
        }
    }
}

// Will log error if handler takes > 1 second
[SingletonHandler]
public class SlowOperationHandler : IRequestHandler<SlowRequest, Result>
{
    public async Task<Result> Handle(
        SlowRequest request,
        IMediatorContext context,
        CancellationToken cancellationToken)
    {
        // If this takes > 1 second, an error will be logged
        await Task.Delay(2000, cancellationToken);
        return new Result();
    }
}
```

### Main Thread Middleware
Ensure UI updates happen on the main thread:

```csharp
[SingletonHandler]
public class ProductUpdatedEventHandler : IEventHandler<ProductUpdatedEvent>
{
    private readonly IProductListViewModel _viewModel;
    
    [MainThread]
    public async Task Handle(
        ProductUpdatedEvent @event,
        IMediatorContext context,
        CancellationToken cancellationToken)
    {
        // Safe to update UI here - guaranteed to be on main thread
        _viewModel.RefreshProduct(@event.ProductId);
    }
}
```

### Configuration
Configure middleware via appsettings.json:

```json
{
    "Mediator": {
        "Cache": {
            "MyApp.Handlers.GetProductsHandler": {
                "Priority": "High",
                "AbsoluteExpirationSeconds": 300,
                "SlidingExpirationSeconds": 60
            }
        },
        "PerformanceLogging": {
            "*": {
                "ErrorThresholdMilliseconds": 5000
            }
        },
        "Resilience": {
            "MyApp.Handlers.ApiCallHandler": {
                "TimeoutMilliseconds": 5000,
                "Retry": {
                    "MaxAttempts": 3,
                    "DelayMilliseconds": 1000,
                    "BackoffType": "Exponential"
                }
            }
        }
    }
}
```

### Custom Middleware
Create your own middleware by implementing the pipeline:

```csharp
public class LoggingMiddleware<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    private readonly ILogger<LoggingMiddleware<TRequest, TResponse>> _logger;
    
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        IMediatorContext context,
        CancellationToken cancellationToken)
    {
        _logger.LogInformation($"Handling {typeof(TRequest).Name}");
        
        try
        {
            var response = await next();
            _logger.LogInformation($"Handled {typeof(TRequest).Name} successfully");
            return response;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, $"Error handling {typeof(TRequest).Name}");
            throw;
        }
    }
}
```

## Best Practices

1. **Single Responsibility**: Each handler handles one request type
2. **Testability**: Mock mediator for unit testing
3. **Error Handling**: Use Result<T> pattern for error propagation
4. **Caching**: Implement caching in handlers when appropriate
5. **Logging**: Add structured logging in handlers
6. **Validation**: Validate requests before processing