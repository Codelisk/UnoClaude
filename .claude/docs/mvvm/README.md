# ViewModels and Models with Uno.Extensions.Reactive

## Base Classes

### BaseViewModel
All ViewModels must inherit from BaseViewModel:

```csharp
public partial class MyViewModel : BaseViewModel
{
    // ViewModel implementation
}
```

### BaseItemModel
For list items and data models:

```csharp
public partial class MyItemModel : BaseItemModel
{
    // Model implementation
}
```

## Uno.Extensions.Reactive Patterns

### IFeed<T> for Async Data
Use `IFeed<T>` for asynchronous data streams:

```csharp
public partial class ProductViewModel : BaseViewModel
{
    private readonly IProductService _productService;

    public IFeed<ImmutableList<Product>> Products => Feed.Async(async ct =>
        await _productService.GetProductsAsync(ct)
    );
}
```

### IState<T> for User Input
Use `IState<T>` for two-way binding:

```csharp
public partial class SearchViewModel : BaseViewModel
{
    public IState<string> SearchText => State<string>.Value(this, () => string.Empty);
    
    public IFeed<ImmutableList<SearchResult>> Results => SearchText
        .Where(text => !string.IsNullOrEmpty(text))
        .SelectAsync(async (text, ct) => await _searchService.SearchAsync(text, ct));
}
```

### MVUX Pattern
Consider using MVUX in regions for simplified reactive programming:

```csharp
public partial record ProductModel(string Name, decimal Price)
{
    public IFeed<ProductDetails> Details => this.GetDetails();
    
    private async ValueTask<ProductDetails> GetDetails(CancellationToken ct)
    {
        // Fetch additional details
    }
}
```

### FeedView Control
Use FeedView for handling different data states:

```xml
<mvux:FeedView Source="{Binding Products}">
    <mvux:FeedView.ValueTemplate>
        <DataTemplate>
            <ListView ItemsSource="{Binding Data}"/>
        </DataTemplate>
    </mvux:FeedView.ValueTemplate>
    
    <mvux:FeedView.LoadingTemplate>
        <DataTemplate>
            <ProgressRing IsActive="True"/>
        </DataTemplate>
    </mvux:FeedView.LoadingTemplate>
    
    <mvux:FeedView.ErrorTemplate>
        <DataTemplate>
            <TextBlock Text="Error loading products"/>
        </DataTemplate>
    </mvux:FeedView.ErrorTemplate>
</mvux:FeedView>
```

## Best Practices

1. **Immutability**: Use immutable records for models
2. **Cancellation**: Always support CancellationToken
3. **Error Handling**: Use Feed's built-in error states
4. **Testing**: ViewModels should be easily testable with mock services