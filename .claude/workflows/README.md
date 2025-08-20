# GitHub Ticket Analysis Workflow

Ein strukturiertes System zur Analyse von GitHub Tickets und automatischen Verarbeitung der Ergebnisse.

## Überblick

Dieses Workflow-System ersetzt die ursprünglichen lose gekoppelten Markdown-Dateien durch ein strukturiertes Command-System mit klaren Templates und Konfigurationen.

## Verwendung

### 1. Ticket analysieren
```bash
# Führe die Analyse eines GitHub Tickets durch
/analyze-ticket
```

Der Command fragt nach der Ticket-URL oder Issue-Nummer und erstellt eine strukturierte Analyse in `./Ticketprotokoll/grobanalyse.json`.

### 2. Ergebnisse verarbeiten
```bash
# Verarbeite die Analyse-Ergebnisse automatisch
/process-ticket-results
```

Basierend auf den Flags in der Grobanalyse werden automatisch die entsprechenden Detail-Analysen durchgeführt.

## Struktur

```
.claude/
├── commands/           # Slash-Commands für verschiedene Analysen
│   ├── analyze-ticket.md
│   ├── process-ticket-results.md
│   ├── entities-analysis.md
│   ├── api-analysis.md
│   ├── ui-design-analysis.md
│   └── ui-logic-analysis.md
├── templates/          # Vorlagen und Schemas
│   └── grobanalyse-schema.json
└── workflows/          # Workflow-Konfigurationen
    ├── ticket-analysis-config.json
    └── README.md
```

## Workflow-Ablauf

1. **Initiale Analyse**: `/analyze-ticket` analysiert das Ticket und identifiziert benötigte Änderungen
2. **Automatische Verarbeitung**: `/process-ticket-results` führt basierend auf den Flags die entsprechenden Detail-Analysen aus
3. **Detail-Analysen**: Jeder Bereich (Entities, API, UI, etc.) hat seinen eigenen spezialisierten Command
4. **Strukturierte Outputs**: Alle Ergebnisse werden in `./Ticketprotokoll/` gespeichert

## Vorteile gegenüber der alten Struktur

- **Klare Commands**: Eindeutige Slash-Commands statt verschachtelter MD-Dateien
- **Konfigurierbar**: Zentrale Konfiguration statt hardcodierter Pfade
- **Erweiterbar**: Neue Analyse-Typen können einfach hinzugefügt werden
- **Strukturiert**: JSON-Schemas für validierte Datenstrukturen
- **Wartbar**: Klare Trennung von Commands, Templates und Konfiguration