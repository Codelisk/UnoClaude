# CLAUDE: Navigation Registration Pattern

## Overview
The UnoApp uses a structured navigation system based on ViewMaps and RouteMaps. This document explains how to register views and configure navigation routes using the pattern defined in `RegisterRoutes`.

## Navigation Components

### IViewRegistry
Manages the mapping between Views and ViewModels:
- Registers ViewMap entries
- Provides view lookup by ViewModel type
- Enables type-safe navigation

### IRouteRegistry  
Manages the navigation route hierarchy:
- Registers RouteMap entries
- Defines nested route structures
- Maps URLs to views

## RegisterRoutes Method

The `RegisterRoutes` method is called during host configuration:

```csharp
private static void RegisterRoutes(IViewRegistry views, IRouteRegistry routes)
{
    // Step 1: Register all ViewMaps
    // Step 2: Register RouteMap hierarchy
}
```

## ViewMap Registration

### Basic ViewMap Syntax

```csharp
views.Register(
    // ViewModel-only registration (view is inferred)
    new ViewMap(ViewModel: typeof(ShellViewModel)),
    
    // Generic registration (recommended)
    new ViewMap<LoginPage, LoginPageViewModel>(),
    new ViewMap<RegisterPage, RegisterPageViewModel>(),
    new ViewMap<MainPage, MainViewModel>(),
    
    // DataViewMap for data-bound views
    new DataViewMap<ProductDetailsPage, ProductDetailsViewModel, Product>()
);
```

### ViewMap Types

1. **ViewMap** - Standard page registration
2. **DataViewMap** - Pages that receive data during navigation

## RouteMap Registration

### Route Hierarchy Pattern

```csharp
routes.Register(
    new RouteMap(
        "",  // Root route
        View: views.FindByViewModel<ShellViewModel>(),
        Nested:
        [
            new(
                "Login",
                View: views.FindByViewModel<LoginPageViewModel>()
            ),
            new(
                "Register", 
                View: views.FindByViewModel<RegisterPageViewModel>()
            ),
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
```

### Route Structure Visualization

```
"" (Shell - Root Container)
├── "Login" (LoginPage)
├── "Register" (RegisterPage)
└── "Main" (MainPage)
    └── "Page2" (Page1)
        └── "Region1" (Region1 - RegionView)
```

## Navigation Patterns

### 1. Simple Page Navigation

```csharp
// ViewMap registration
new ViewMap<SettingsPage, SettingsViewModel>()

// RouteMap registration
new(
    "Settings",
    View: views.FindByViewModel<SettingsViewModel>()
)

// Navigation
await navigator.NavigateViewModelAsync<SettingsViewModel>();
```

### 2. Nested Navigation

```csharp
// Parent page
new(
    "Products",
    View: views.FindByViewModel<ProductListViewModel>(),
    Nested:
    [
        // Child page
        new(
            "Details",
            View: views.FindByViewModel<ProductDetailsViewModel>()
        )
    ]
)

// Results in route: /Products/Details
```

### 3. Region-based Navigation

Regions allow multiple views within a single page:

```csharp
new(
    PageNames.Dashboard,
    View: views.FindByViewModel<DashboardViewModel>(),
    Nested:
    [
        new(
            RegionViewsNames.LeftPanel,
            View: views.FindByViewModel<LeftPanelViewModel>()
        ),
        new(
            RegionViewsNames.RightPanel,
            View: views.FindByViewModel<RightPanelViewModel>()
        )
    ]
)
```

## Naming Conventions

### Page Names
Define constants for consistency:

```csharp
public static class PageNames
{
    public const string Main = "Main";
    public const string Products = "Products";
    public const string Settings = "Settings";
    public const string Profile = "Profile";
}
```

### Region Names
Define region identifiers:

```csharp
public static class RegionViewsNames
{
    public const string Region1 = "Region1";
    public const string LeftPanel = "LeftPanel";
    public const string RightPanel = "RightPanel";
    public const string ContentArea = "ContentArea";
}
```

## Advanced Scenarios

### 1. Conditional Routes

```csharp
var routes = new List<RouteMap>();

// Always add main route
routes.Add(new RouteMap(
    "",
    View: views.FindByViewModel<ShellViewModel>(),
    Nested: GetConditionalRoutes()
));

RouteMap[] GetConditionalRoutes()
{
    var nestedRoutes = new List<RouteMap>();
    
    // Add login/register for unauthenticated
    nestedRoutes.Add(new("Login", View: views.FindByViewModel<LoginPageViewModel>()));
    nestedRoutes.Add(new("Register", View: views.FindByViewModel<RegisterPageViewModel>()));
    
    // Add authenticated routes
    nestedRoutes.Add(new(
        PageNames.Main,
        View: views.FindByViewModel<MainViewModel>(),
        Nested: GetAuthenticatedRoutes()
    ));
    
    return nestedRoutes.ToArray();
}
```

### 2. Dynamic Route Registration

```csharp
// Register feature modules dynamically
foreach (var module in GetFeatureModules())
{
    views.Register(module.GetViewMaps());
    routes.Register(module.GetRouteMaps());
}
```

### 3. Data-Driven Navigation

```csharp
// Register DataViewMap
new DataViewMap<OrderDetailsPage, OrderDetailsViewModel, Order>()

// Navigate with data
await navigator.NavigateViewModelAsync<OrderDetailsViewModel>(
    data: selectedOrder
);
```

## Best Practices

### 1. Organization
- Group related ViewMaps together
- Structure routes to match UI hierarchy
- Use consistent naming patterns

### 2. Route Design
- Keep URLs short and meaningful
- Use nested routes for related pages
- Avoid deep nesting (max 3-4 levels)

### 3. ViewMap Guidelines
- Always use generic syntax when possible
- Register DataViewMaps for data-receiving pages
- Ensure ViewModel names match convention

### 4. Testing Routes
```csharp
// Verify route registration
var view = views.FindByViewModel<MainViewModel>();
Assert.NotNull(view);

// Test navigation
await navigator.NavigateViewModelAsync<MainViewModel>();
Assert.IsType<MainPage>(navigator.CurrentView);
```

## Common Patterns

### 1. Tab Navigation
```csharp
new(
    PageNames.Main,
    View: views.FindByViewModel<MainViewModel>(),
    Nested:
    [
        new("Home", View: views.FindByViewModel<HomeViewModel>()),
        new("Search", View: views.FindByViewModel<SearchViewModel>()),
        new("Profile", View: views.FindByViewModel<ProfileViewModel>())
    ]
)
```

### 2. Master-Detail
```csharp
new(
    "Customers",
    View: views.FindByViewModel<CustomerListViewModel>(),
    Nested:
    [
        new(
            "{customerId}",
            View: views.FindByViewModel<CustomerDetailViewModel>()
        )
    ]
)
```

### 3. Wizard Flow
```csharp
new(
    "Setup",
    View: views.FindByViewModel<SetupWizardViewModel>(),
    Nested:
    [
        new("Step1", View: views.FindByViewModel<Step1ViewModel>()),
        new("Step2", View: views.FindByViewModel<Step2ViewModel>()),
        new("Step3", View: views.FindByViewModel<Step3ViewModel>()),
        new("Complete", View: views.FindByViewModel<CompleteViewModel>())
    ]
)
```

## Troubleshooting

### Common Issues

1. **View not found**: Ensure ViewMap is registered before RouteMap
2. **Navigation fails**: Check route path matches registration
3. **Wrong view displayed**: Verify ViewModel type in FindByViewModel

### Debugging
```csharp
// Log registered routes
foreach (var route in routes.GetAllRoutes())
{
    _logger.LogDebug($"Route: {route.Path} -> {route.View}");
}
```