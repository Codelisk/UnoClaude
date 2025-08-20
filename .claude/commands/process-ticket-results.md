Nutze den Ticket Processor Agent um die Analyse-Ergebnisse zu verarbeiten.

## Agent-Aufruf:
```
Task(
  description="Ticket-Ergebnisse verarbeiten",
  prompt="/ticket-processor",
  subagent_type="general-purpose"
)
```

Der Agent wird:
1. Die Datei ./Ticketprotokoll/grobanalyse.json laden
2. Basierend auf den boolean-Werten die entsprechenden Sub-Agents aufrufen:
   - entities-analyzer für entitiesChanged=true
   - api-analyzer für newApiEndpoints=true
   - ui-design-analyzer für uiChanges=true
   - ui-logic-analyzer für uiLogicChanges=true
3. Bei newLibraries=true die Datei ./Ticketprotokoll/thirdparty.txt erstellen
4. Alle durchgeführten Aktionen dokumentieren

## Agent-Dateien:
- Haupt-Agent: .claude/agents/ticket-processor.md
- Sub-Agents: .claude/agents/[entities|api|ui-design|ui-logic]-analyzer.md