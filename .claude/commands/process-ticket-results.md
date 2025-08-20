Verarbeite die Ergebnisse der Ticket-Analyse basierend auf ./Ticketprotokoll/grobanalyse.json

Führe folgende Schritte aus:

1. Lade die Datei ./Ticketprotokoll/grobanalyse.json
2. Prüfe die Analyse-Ergebnisse und führe die entsprechenden Templates aus:

   - Wenn `entitiesChanged` = true:
     → Führe /entities-analysis aus
   
   - Wenn `newApiEndpoints` = true:
     → Führe /api-analysis aus
   
   - Wenn `uiChanges` = true:
     → Führe /ui-design-analysis aus
   
   - Wenn `uiLogicChanges` = true:
     → Führe /ui-logic-analysis aus
   
   - Wenn `newLibraries` = true:
     → Erstelle oder überschreibe ./Ticketprotokoll/thirdparty.txt mit:
       - Liste der benötigten Libraries
       - Funktionen/Features, für die sie benötigt werden
       - Empfohlene Packages

3. Erstelle eine Zusammenfassung aller durchgeführten Aktionen
4. Speichere den Status in ./Ticketprotokoll/processing-log.json