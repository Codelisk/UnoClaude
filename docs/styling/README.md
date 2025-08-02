# XAML Styling Guidelines

## Core Principle
**Every control in XAML must have an explicit style**, even for simple elements.

## Style Definition Structure

### App.xaml Resources
Define all styles in centralized resource dictionaries:

```xml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="Styles/Colors.xaml"/>
            <ResourceDictionary Source="Styles/TextStyles.xaml"/>
            <ResourceDictionary Source="Styles/ButtonStyles.xaml"/>
            <ResourceDictionary Source="Styles/ControlStyles.xaml"/>
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

### Style Examples

#### TextBlock Styles
```xml
<!-- TextStyles.xaml -->
<Style x:Key="HeaderTextStyle" TargetType="TextBlock">
    <Setter Property="FontSize" Value="24"/>
    <Setter Property="FontWeight" Value="Bold"/>
    <Setter Property="Foreground" Value="{StaticResource PrimaryBrush}"/>
    <Setter Property="Margin" Value="0,0,0,8"/>
</Style>

<Style x:Key="BodyTextStyle" TargetType="TextBlock">
    <Setter Property="FontSize" Value="14"/>
    <Setter Property="Foreground" Value="{StaticResource SecondaryBrush}"/>
    <Setter Property="LineHeight" Value="20"/>
</Style>
```

#### Button Styles
```xml
<!-- ButtonStyles.xaml -->
<Style x:Key="PrimaryButtonStyle" TargetType="Button">
    <Setter Property="Background" Value="{StaticResource PrimaryBrush}"/>
    <Setter Property="Foreground" Value="White"/>
    <Setter Property="Padding" Value="16,8"/>
    <Setter Property="CornerRadius" Value="4"/>
    <Setter Property="FontWeight" Value="SemiBold"/>
</Style>

<Style x:Key="SecondaryButtonStyle" TargetType="Button">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="BorderBrush" Value="{StaticResource PrimaryBrush}"/>
    <Setter Property="BorderThickness" Value="1"/>
    <Setter Property="Padding" Value="16,8"/>
</Style>
```

### Usage in XAML
```xml
<!-- Always use explicit styles -->
<Grid>
    <TextBlock Style="{StaticResource HeaderTextStyle}" 
               Text="Welcome"/>
    
    <TextBlock Style="{StaticResource BodyTextStyle}" 
               Text="Please select an option"/>
    
    <Button Style="{StaticResource PrimaryButtonStyle}"
            Content="Continue"/>
    
    <!-- Even simple elements need styles -->
    <Border Style="{StaticResource CardBorderStyle}">
        <TextBlock Style="{StaticResource BodyTextStyle}" 
                   Text="Content"/>
    </Border>
</Grid>
```

## Style Naming Convention

1. **Purpose-based naming**: `HeaderTextStyle`, `NavigationButtonStyle`
2. **State variations**: `PrimaryButtonStyle`, `SecondaryButtonStyle`
3. **Context-specific**: `ListItemTextStyle`, `DialogTitleStyle`

## Color Resources
Define all colors as resources:

```xml
<!-- Colors.xaml -->
<Color x:Key="PrimaryColor">#0078D4</Color>
<Color x:Key="SecondaryColor">#6C757D</Color>
<Color x:Key="SuccessColor">#28A745</Color>
<Color x:Key="ErrorColor">#DC3545</Color>

<SolidColorBrush x:Key="PrimaryBrush" Color="{StaticResource PrimaryColor}"/>
<SolidColorBrush x:Key="SecondaryBrush" Color="{StaticResource SecondaryColor}"/>
```

## Best Practices

1. **No inline styling** - Never set properties directly on controls
2. **Reusable styles** - Create base styles and inherit
3. **Consistent spacing** - Use standard margin/padding values
4. **Theme support** - Consider light/dark theme variations
5. **Platform-specific** - Use conditional XAML for platform differences