# UI-Analyse und Glossar

## Glossar-Imports

## CodeSample-Imports
@../../codesamples/ui/vms/viewmodel.md
@../../codesamples/ui/vms/command.md
@../../codesamples/ui/vms/property.md

## Important
@../../docs/mediator/index.md

- Füge für jede neue Page ein ViewModel hinzu
- Füge für jede neue View ein ViewModel hinzu
- Für die Regions die sich innerhalb der View oder Page befinden, diese werden definiert mit:
    public override IEnumerable<RegionModel> GetRegions(NavigationEventArgs e)
    {
        var navigationData = true;
        return [new("List", RegionViewsNames.View, data: navigationData)];
    }
- Für das Laden von Daten beim Startup überschreibe public virtual Task InitializeAsync(TNavigationEventArgs e)
- Schau ob in der Page oder View eine FeedView gibt, dann muss Uno.Extensions.Reactive IFeed verwendet werden
- Schau welche Commands es gibt
- Schau welche Properties NotifyPropertyChanged sein müssen
- Für jede andere Logik die gebraucht wird MUSST du das Shiny Mediator Pattern verwenden
