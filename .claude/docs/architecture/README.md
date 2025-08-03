# Architecture Overview

## Page and Region System

### BasePage Pattern
All pages in the UnoClaude project must inherit from `BasePage`:

```csharp
public partial class MyPage : BasePage
{
    // Page implementation
}
```

### UnoRegions Architecture
Pages are divided into UnoRegions for better modularity:

```xml
<Page x:Class="UnoClaude.Pages.MyPage">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        
        <!-- Header Region -->
        <ContentControl Grid.Row="0" 
                       uen:Region.Attached="true"
                       uen:Region.Name="Header"/>
        
        <!-- Content Region -->
        <ContentControl Grid.Row="1"
                       uen:Region.Attached="true"
                       uen:Region.Name="Content"/>
    </Grid>
</Page>
```

### Region Components
Each region consists of:
1. **Separate XAML file** - `HeaderRegion.xaml`
2. **Individual ViewModel** - `HeaderViewModel.cs`
3. **Model if needed** - `HeaderModel.cs`

### Navigation
Use Uno.Extensions.Navigation for region-based navigation:

```csharp
await _navigator.NavigateViewModelAsync<MainViewModel>(this);
```

### Folder Structure
Each page has its own subfolder containing all related components:

```
src/
├── Pages/
│   ├── Main/
│   │   ├── MainPage.xaml
│   │   ├── MainPage.xaml.cs
│   │   ├── Regions/
│   │   │   ├── Header/
│   │   │   │   ├── MainHeaderRegion.xaml
│   │   │   │   └── MainHeaderRegion.xaml.cs
│   │   │   └── Content/
│   │   │       ├── MainContentRegion.xaml
│   │   │       └── MainContentRegion.xaml.cs
│   │   └── ViewModels/
│   │       ├── MainViewModel.cs
│   │       ├── MainHeaderViewModel.cs
│   │       └── MainContentViewModel.cs
│   ├── Products/
│   │   ├── ProductsPage.xaml
│   │   ├── ProductsPage.xaml.cs
│   │   ├── Regions/
│   │   │   ├── List/
│   │   │   │   ├── ProductListRegion.xaml
│   │   │   │   └── ProductListRegion.xaml.cs
│   │   │   └── Details/
│   │   │       ├── ProductDetailsRegion.xaml
│   │   │       └── ProductDetailsRegion.xaml.cs
│   │   └── ViewModels/
│   │       ├── ProductsViewModel.cs
│   │       ├── ProductListViewModel.cs
│   │       └── ProductDetailsViewModel.cs
│   └── Settings/
│       ├── SettingsPage.xaml
│       ├── SettingsPage.xaml.cs
│       ├── Regions/
│       │   └── General/
│       │       ├── GeneralSettingsRegion.xaml
│       │       └── GeneralSettingsRegion.xaml.cs
│       └── ViewModels/
│           ├── SettingsViewModel.cs
│           └── GeneralSettingsViewModel.cs
└── Models/
    ├── Product.cs
    ├── User.cs
    └── Settings.cs
```

### Naming Convention
- Page folders: Named after the page (e.g., `Main`, `Products`, `Settings`)
- Region names: Prefixed with page name (e.g., `MainHeaderRegion`, `ProductListRegion`)
- ViewModels: Prefixed with page name (e.g., `MainViewModel`, `ProductListViewModel`)