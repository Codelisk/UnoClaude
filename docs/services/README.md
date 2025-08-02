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
        
        // Services are auto-registered via [Service] attribute
        builder.Services.AddServicesFromAssemblyOf<ProductService>();
        
        // Register background jobs if needed
        builder.Services.AddJob<SyncProductsJob>();
        
        return builder.Build();
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

## Best Practices

1. **Single Responsibility**: Each handler handles one request type
2. **Testability**: Mock mediator for unit testing
3. **Error Handling**: Use Result<T> pattern for error propagation
4. **Caching**: Implement caching in handlers when appropriate
5. **Logging**: Add structured logging in handlers
6. **Validation**: Validate requests before processing