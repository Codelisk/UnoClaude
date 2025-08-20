Nutze den Ticket Analyzer Agent um das GitHub-Ticket zu analysieren.

## Agent-Aufruf:
```
Task(
  description="Ticket-Analyse durchführen",
  prompt="/ticket-analyzer [TICKET_URL oder ISSUE_NUMMER]",
  subagent_type="general-purpose"
)
```

Der Agent wird:
1. Das GitHub-Ticket lesen
2. Die Analyse gemäß der definierten Punkte durchführen
3. Die Datei ./Ticketprotokoll/grobanalyse.json erstellen
4. Eine Zusammenfassung der Analyse liefern

## Agent-Datei:
Der Agent nutzt die Definitionen aus: .claude/agents/ticket-analyzer.md