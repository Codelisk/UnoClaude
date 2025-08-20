# Ticket Analysis Agents

Spezialisierte Agents für die strukturierte Analyse von GitHub Tickets.

## Agents Übersicht

### 1. ticket-analyzer.md
**Zweck**: Initiale Grobanalyse von GitHub Tickets
- Analysiert ob Entities, APIs, UI oder Libraries betroffen sind
- Erstellt ./Ticketprotokoll/grobanalyse.json
- Basiert auf ursprünglicher 1.md

### 2. ticket-processor.md
**Zweck**: Verarbeitet Analyse-Ergebnisse und ruft Sub-Agents auf
- Liest grobanalyse.json
- Führt bedingte Aktionen basierend auf boolean-Werten aus
- Basiert auf ursprünglicher 2.md

### 3. entities-analyzer.md
**Zweck**: Detaillierte Entity-Analyse
- Identifiziert neue/geänderte/gelöschte Entities
- Erstellt oder bearbeitet Entity-Dateien
- Basiert auf ursprünglicher entities.md

### 4. api-analyzer.md
**Zweck**: API-Endpunkt Analyse und Erstellung
- Analysiert benötigte Endpunkte (GET, POST, PUT, DELETE)
- Erstellt Endpunkt-Implementierungen
- Basiert auf ursprünglicher api.md

### 5. ui-design-analyzer.md
**Zweck**: UI-Design und Komponenten-Analyse
- Erstellt Navigationsstruktur
- Plant Pages und Regions
- Definiert Styles für alle UI-Elemente
- Basiert auf ursprünglicher uidesign.md

### 6. ui-logic-analyzer.md
**Zweck**: UI-Logik und ViewModel-Implementierung
- Erstellt ViewModels für Pages/Views
- Implementiert Commands und Properties
- Nutzt Shiny Mediator Pattern
- Basiert auf ursprünglicher uilogic.md

## Verwendung

Die Agents werden über die Slash-Commands aufgerufen:
- `/analyze-ticket` - Startet ticket-analyzer
- `/process-ticket-results` - Startet ticket-processor

Der ticket-processor ruft automatisch die entsprechenden Sub-Agents auf.