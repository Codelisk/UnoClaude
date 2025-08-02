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
```
src/
├── Pages/
│   ├── MainPage.xaml
│   └── MainPage.xaml.cs
├── Regions/
│   ├── Header/
│   │   ├── HeaderRegion.xaml
│   │   └── HeaderRegion.xaml.cs
│   └── Content/
│       ├── ContentRegion.xaml
│       └── ContentRegion.xaml.cs
└── ViewModels/
    ├── MainViewModel.cs
    ├── HeaderViewModel.cs
    └── ContentViewModel.cs
```