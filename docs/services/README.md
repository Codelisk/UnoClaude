# Services Architecture with Shiny Mediator Pattern

## Service Layer Structure

### Base Service Pattern
All services should encapsulate business logic:

```csharp
public interface IProductService
{
    Task<ImmutableList<Product>> GetProductsAsync(CancellationToken ct);
    Task<Product> GetProductByIdAsync(int id, CancellationToken ct);
    Task<Product> CreateProductAsync(Product product, CancellationToken ct);
}

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
Define requests and handlers for API operations:

```csharp
// Request
public record GetProductsRequest : IRequest<GetProductsResponse>
{
    public int? CategoryId { get; init; }
    public string? SearchTerm { get; init; }
}

// Response
public record GetProductsResponse
{
    public ImmutableList<Product> Products { get; init; } = ImmutableList<Product>.Empty;
}

// Handler
[SingletonHandler]
public class GetProductsHandler : IRequestHandler<GetProductsRequest, GetProductsResponse>
{
    private readonly IApiClient _apiClient;
    
    public GetProductsHandler(IApiClient apiClient)
    {
        _apiClient = apiClient;
    }
    
    public async Task<GetProductsResponse> Handle(GetProductsRequest request, CancellationToken ct)
    {
        var products = await _apiClient.GetProductsAsync(
            request.CategoryId, 
            request.SearchTerm, 
            ct
        );
        
        return new GetProductsResponse { Products = products.ToImmutableList() };
    }
}
```

### Message Bus Pattern
Use mediator for app-wide messaging:

```csharp
// Event
public record ProductUpdatedEvent : IEvent
{
    public int ProductId { get; init; }
    public string UpdatedBy { get; init; }
}

// Event Handler
[SingletonHandler]
public class ProductUpdatedEventHandler : IEventHandler<ProductUpdatedEvent>
{
    private readonly ILogger<ProductUpdatedEventHandler> _logger;
    
    public ProductUpdatedEventHandler(ILogger<ProductUpdatedEventHandler> logger)
    {
        _logger = logger;
    }
    
    public Task Handle(ProductUpdatedEvent @event, CancellationToken ct)
    {
        _logger.LogInformation($"Product {event.ProductId} updated by {event.UpdatedBy}");
        // Update UI, cache, etc.
        return Task.CompletedTask;
    }
}

// Publishing events
await _mediator.Publish(new ProductUpdatedEvent 
{ 
    ProductId = productId,
    UpdatedBy = currentUser 
});
```

### API Client Integration
Configure API clients with Refit or HttpClient:

```csharp
public interface IApiClient
{
    [Get("/api/products")]
    Task<List<Product>> GetProductsAsync(
        [Query] int? categoryId,
        [Query] string? search,
        CancellationToken ct
    );
    
    [Post("/api/products")]
    Task<Product> CreateProductAsync(
        [Body] Product product,
        CancellationToken ct
    );
}
```

## Dependency Injection with Shiny

### Service Registration
Register services and handlers with Shiny Mediator:

```csharp
// Service registration in MauiProgram
public class MauiProgram
{
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();
        
        builder.UseShiny();
        
        builder.Services.AddShinyMediator(cfg => 
        {
            cfg.AddHandlersFromAssemblyOf<GetProductsHandler>();
        });
        
        // Register Services
        builder.Services.AddSingleton<IProductService, ProductService>();
        
        // Register API Clients
        builder.Services.AddRefitClient<IApiClient>()
            .ConfigureHttpClient(c => c.BaseAddress = new Uri("https://api.example.com"));
        
        // Register background jobs if needed
        builder.Services.AddJob<SyncProductsJob>();
        
        return builder.Build();
    }
}
```

### Handler Registration with Attributes
Use Shiny attributes for handler registration:

```csharp
// Handlers with Shiny attributes
[SingletonHandler]
public class GetProductsHandler : IRequestHandler<GetProductsRequest, GetProductsResponse>
{
    private readonly IApiClient _apiClient;
    
    public GetProductsHandler(IApiClient apiClient)
    {
        _apiClient = apiClient;
    }
    
    public async Task<GetProductsResponse> Handle(GetProductsRequest request, CancellationToken ct)
    {
        // Implementation
    }
}

[SingletonHandler]
public class ProductUpdatedEventHandler : IEventHandler<ProductUpdatedEvent>
{
    private readonly ILogger<ProductUpdatedEventHandler> _logger;
    
    public ProductUpdatedEventHandler(ILogger<ProductUpdatedEventHandler> logger)
    {
        _logger = logger;
    }
    
    public Task Handle(ProductUpdatedEvent @event, CancellationToken ct)
    {
        // Implementation
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

## Best Practices

1. **Single Responsibility**: Each handler handles one request type
2. **Testability**: Mock mediator for unit testing
3. **Error Handling**: Use Result<T> pattern for error propagation
4. **Caching**: Implement caching in handlers when appropriate
5. **Logging**: Add structured logging in handlers
6. **Validation**: Validate requests before processing