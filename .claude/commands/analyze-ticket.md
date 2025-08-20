Analysiere das GitHub-Ticket und erstelle eine strukturierte Analyse in ./Ticketprotokoll/grobanalyse.json

Führe folgende Schritte aus:

1. Lese das GitHub-Ticket (URL oder Issue-Nummer wird vom User bereitgestellt)
2. Analysiere folgende Punkte:
   - Müssen für das Ticket Entities verändert oder hinzugefügt werden?
   - Braucht man für das Ticket neue Api Endpunkte?
   - Braucht man UI Anpassungen für das Ticket?
   - Braucht man UI-Logik Anpassungen für das Ticket?
   - Gibt es im Ticket Teile für die man neue Nuget Packete/Third Libraries benötigt?

3. Erstelle die Datei ./Ticketprotokoll/grobanalyse.json mit folgendem Schema:
```json
{
  "ticketId": "string",
  "ticketTitle": "string",
  "analysisDate": "ISO-8601 date",
  "entitiesChanged": boolean,
  "entitiesDetails": "string (optional)",
  "newApiEndpoints": boolean,
  "apiEndpointsDetails": "string (optional)",
  "uiChanges": boolean,
  "uiChangesDetails": "string (optional)",
  "uiLogicChanges": boolean,
  "uiLogicDetails": "string (optional)",
  "newLibraries": boolean,
  "librariesDetails": "array of strings (optional)"
}
```

4. Stelle sicher, dass das Verzeichnis ./Ticketprotokoll existiert
5. Gib eine Zusammenfassung der Analyse aus