# UnoApp Navigation and Routes Documentation

## Overview
This document explains the navigation system in UnoApp, including route registration, view mapping, and the hierarchical navigation structure.

## Navigation Architecture
The navigation system is built on Uno.Extensions.Navigation and uses:
- **IViewRegistry**: Maps views to view models
- **IRouteRegistry**: Defines navigation routes
- **ViewMap**: Associates views with view models
- **RouteMap**: Defines the navigation hierarchy

## RegisterRoutes Method
The `RegisterRoutes` method in `App.cs` defines the complete navigation structure:

```csharp
private static void RegisterRoutes(IViewRegistry views, IRouteRegistry routes)
{
    // View registrations
    views.Register(/* view mappings */);
    
    // Route registrations
    routes.Register(/* route hierarchy */);
}
```

## View Registration

### Basic ViewMap
```csharp
new ViewMap<LoginPage, LoginPageViewModel>()
```
- Maps LoginPage view to LoginPageViewModel
- Enables navigation by view model type
- Supports dependency injection for view models

### Shell ViewMap
```csharp
new ViewMap(ViewModel: typeof(ShellViewModel))
```
- Special registration for the Shell
- No view type specified (Shell is special)
- Acts as the root container

### DataViewMap for Lists
```csharp
new DataViewMap<
    WixContactsListView,
    WixContactsListViewModel,
    IFeed<IEnumerable<WixContactsListModel>>
>()
```
- Maps list views with data context
- Supports reactive data binding
- Integrates with MVUX feeds

## Route Hierarchy

### Root Route (Shell)
```csharp
new RouteMap(
    "",  // Empty path = root
    View: views.FindByViewModel<ShellViewModel>(),
    Nested: [ /* nested routes */ ]
)
```

### Authentication Routes
```csharp
new("Login", View: views.FindByViewModel<LoginPageViewModel>()),
new("Register", View: views.FindByViewModel<RegisterPageViewModel>())
```
- Direct children of Shell
- Accessible via /Login and /Register
- No nested routes

### Main Application Area
```csharp
new(
    PageNames.Main,
    View: views.FindByViewModel<MainViewModel>(),
    Nested: [
        // Feature pages
    ]
)
```

### Feature Pages with Regions
```csharp
new(
    PageNames.WixContacts,
    View: views.FindByViewModel<WixContactsPageViewModel>(),
    Nested: [
        new(
            RegionViewsNames.WixContactList,
            View: views.FindByViewModel<WixContactsListViewModel>()
        ),
    ]
),
new(
    PageNames.Contacts,
    View: views.FindByViewModel<ContactsPageViewModel>(),
    Nested: [
        new(
            RegionViewsNames.ContactList,
            View: views.FindByViewModel<ContactsListViewModel>()
        ),
    ]
)
```

## Complete Navigation Structure
```
Shell (/)
├── Login (/Login)
├── Register (/Register)
└── Main (/Main)
    ├── WixContacts (/Main/WixContacts)
    │   └── WixContactList (Region)
    └── Contacts (/Main/Contacts)
        └── ContactList (Region)
```

## Constants Used

### PageNames
```csharp
public static class PageNames
{
    public const string Main = "Main";
    public const string WixContacts = "WixContacts";
    public const string Contacts = "Contacts";
}
```

### RegionViewsNames
```csharp
public static class RegionViewsNames
{
    public const string WixContactList = "WixContactList";
    public const string ContactList = "ContactList";
}
```

## Navigation Patterns

### Navigate to Page
```csharp
// Navigate to login
await navigator.NavigateViewModelAsync<LoginPageViewModel>();

// Navigate with data
await navigator.NavigateViewModelAsync<ContactsPageViewModel>(data: contactId);
```

### Navigate to Route
```csharp
// Navigate using route path
await navigator.NavigateAsync("Login");

// Navigate to nested route
await navigator.NavigateAsync("Main/Contacts");
```

### Region Navigation
Regions are named areas within a page that can host different content:
```csharp
// In the page XAML
<Grid uen:Region.Name="ContactList" />

// Navigation happens automatically based on route
```

## View and ViewModel Conventions

### Naming Conventions
- Views: `*Page.cs` or `*View.cs`
- ViewModels: `*PageViewModel.cs` or `*ViewModel.cs`
- Regions: Use descriptive names in `RegionViewsNames`

### ViewModel Requirements
```csharp
public partial class LoginPageViewModel
{
    // Can inject services via constructor
    public LoginPageViewModel(IAuthenticationService authService)
    {
        // Initialize
    }
}
```

### DataViewModel for Lists
```csharp
public partial class ContactsListViewModel : IViewModel<IFeed<IEnumerable<ContactsListModel>>>
{
    // Implements data context pattern
}
```

## Navigation Guards and Events

### IRouteNotifier
ViewModels can implement `IRouteNotifier` to be notified of navigation events:
```csharp
public partial class MainViewModel : IRouteNotifier
{
    public Task OnNavigatingTo(NavigationRequest request)
    {
        // Called before navigation
    }
    
    public Task OnNavigatedTo(NavigationRequest request)
    {
        // Called after navigation
    }
}
```

## Best Practices

1. **Use Constants**: Define route and region names as constants
2. **Hierarchical Structure**: Organize routes to match UI hierarchy
3. **Region-Based Layouts**: Use regions for dynamic content areas
4. **Type-Safe Navigation**: Prefer NavigateViewModelAsync over string-based navigation
5. **Data Context**: Use DataViewMap for list views with MVUX
6. **Consistent Naming**: Follow naming conventions for automatic discovery