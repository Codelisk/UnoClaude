# Ticket: Create Hosting Documentation for Claude AI

## Beschreibung
Erstelle eine umfassende Dokumentation für Claude AI, die die Hosting-Architektur der UnoApp beschreibt. Diese Dokumentation soll Claude helfen, das Projekt-Hosting und die Startup-Mechanismen zu verstehen.

## Ziel
Erstellung von strukturierten MD-Dateien, die Claude AI als Kontext dienen, um:
- Die Hosting-Architektur der UnoApp zu verstehen
- Den Startup-Prozess nachzuvollziehen
- Die Dependency Injection und Service-Registrierung zu verstehen
- Die Navigation und Route-Konfiguration zu verstehen

## Zu erstellende Dokumentationsdateien

### 1. `CLAUDE_UnoApp_Hosting_Overview.md`
**Inhalt:**
- Überblick über die UnoApp Hosting-Architektur
- Erklärung des IHost-Patterns
- Verwendung von IApplicationBuilder und IHostBuilder
- Integration mit Uno.Extensions.Hosting

**Wichtige Punkte:**
- App.cs als Einstiegspunkt
- CreateBuilder(args) Pattern
- Window-Erstellung über builder.Window
- Host-Property und dessen Lebenszyklus

### 2. `CLAUDE_UnoApp_Startup_Flow.md`
**Inhalt:**
- Detaillierter Startup-Flow der Anwendung
- OnLaunched-Methode und ihre Schritte
- Builder-Konfiguration im Detail
- Navigation-Initialisierung

**Zu dokumentierende Schritte:**
1. App-Konstruktor Initialisierung
2. OnLaunched Event-Handler
3. CreateBuilder mit LaunchActivatedEventArgs
4. UseToolkitNavigation() für Toolkit-Controls
5. Configure-Block mit Host-Konfiguration
6. NavigateAsync<Shell>() für Initial-Navigation

### 3. `CLAUDE_UnoApp_Service_Registration.md`
**Inhalt:**
- Service-Registrierung über HostBuilderExtensions
- Erklärung der einzelnen Extension-Methoden
- Dependency Injection Setup

**Zu dokumentierende Services:**
- **AddEnvironment()**: DotEnvLoader und Environment-Setup
- **AddLogging()**: Log-Level-Konfiguration basierend auf Environment
- **AddConfig()**: Configuration-Setup mit appsettings.json
- **AddLocalization()**: Mehrsprachigkeits-Support
- **AddSerialization()**: JSON-Serialisierung mit System.Text.Json
- **AddHttp()**: HTTP-Client-Konfiguration mit Kiota
- **AddCommonServices()**: 
  - BaseServices Registration
  - Shiny.Mediator Integration
  - Authentication Service
  - HTTP Request Decorators

### 4. `CLAUDE_UnoApp_Navigation_Routes.md`
**Inhalt:**
- Route-Registrierung über RegisterRoutes
- ViewRegistry und RouteRegistry Pattern
- Nested Routes Struktur

**Zu dokumentierende Komponenten:**
- ViewMap und DataViewMap Registrierungen
- RouteMap Hierarchie:
  - Shell als Root
  - Login/Register Pages
  - Main Page mit Nested Routes
  - WixContacts und Contacts Pages
  - RegionViews für Listen

### 5. `CLAUDE_UnoApp_Dependencies_Integration.md`
**Inhalt:**
- Integration von Third-Party Libraries
- Shiny.Mediator für CQRS/Mediator Pattern
- Kiota für API-Client-Generation
- Authentication mit Bearer Tokens

**Wichtige Integrationen:**
- Uno.Resizetizer für Icon-Management
- Shiny.Extensions.DependencyInjection
- Generated Services Pattern
- HTTP Request Decorators für Authentication

## Implementierungs-Hinweise

### Code-Beispiele aus den bereitgestellten Dateien:

1. **App.cs Startup:**
   - InitializeComponent() im Konstruktor
   - OnLaunched mit CreateBuilder Pattern
   - UseToolkitNavigation für UI-Controls
   - Configure-Block für Host-Setup
   - NavigateAsync<Shell>() für Initial Navigation

2. **HostBuilderExtensions:**
   - Modulare Extension-Methods für jeden Service-Bereich
   - Environment-basierte Konfiguration
   - Platform-spezifische Setups (z.B. WebAssembly)
   - Debug-spezifische Handler

3. **ServicesExtensions:**
   - AddGeneratedServices() für [Service]-Attribute
   - Shiny Mediator mit Uno-spezifischen Extensions
   - Bearer Authentication Decorator Pattern

## Erwartetes Ergebnis
5 strukturierte MD-Dateien, die Claude AI einen vollständigen Überblick über die UnoApp Hosting-Architektur geben, sodass Claude in Zukunft:
- Neue Features korrekt in die bestehende Architektur integrieren kann
- Die Service-Registrierung verstehen und erweitern kann
- Navigation und Routing korrekt implementieren kann
- Die Startup-Sequenz nachvollziehen kann

## Priorität
Hoch - Diese Dokumentation ist fundamental für das Verständnis der gesamten Anwendungsarchitektur

## Akzeptanzkriterien
- [ ] Alle 5 MD-Dateien sind erstellt
- [ ] Jede Datei enthält konkrete Code-Beispiele aus dem Projekt
- [ ] Die Dokumentation ist strukturiert und für Claude AI optimiert
- [ ] Alle wichtigen Konzepte sind erklärt
- [ ] Die Beziehungen zwischen den Komponenten sind klar dargestellt