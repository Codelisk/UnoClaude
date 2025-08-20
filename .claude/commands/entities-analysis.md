Analysiere und dokumentiere Entity-Änderungen für das Ticket

Basierend auf der Ticket-Analyse, führe folgende Schritte aus:

1. **Identifiziere betroffene Entities:**
   - Bestehende Entities, die geändert werden müssen
   - Neue Entities, die erstellt werden müssen
   - Entities, die entfernt werden können

2. **Dokumentiere für jede Entity:**
   - Entity-Name und Namespace
   - Neue Properties/Felder
   - Geänderte Properties/Felder
   - Relationships zu anderen Entities
   - Validierungsregeln

3. **Erstelle Migrations-Plan:**
   - Datenbank-Migrationen
   - Backward-Compatibility Überlegungen
   - Test-Daten Anpassungen

4. **Generiere Output:**
   - Erstelle ./Ticketprotokoll/entities-changes.md mit detaillierter Dokumentation
   - Erstelle ./Ticketprotokoll/entities-migration-plan.json
   - Liste Code-Dateien auf, die angepasst werden müssen

5. **Prüfe Abhängigkeiten:**
   - Repository-Klassen
   - Service-Layer
   - DTOs und Mapper