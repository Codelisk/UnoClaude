# UI-Analyse

## Glossar-Imports

@../glossar/ui/page.md
@../glossar/ui/region.md
@../glossar/ui/navigationsstruktur.md
@../glossar/ui/style.md

Dieses Dokument importiert die Glossar-Dateien für **Page**, **Region**, **Navigationsstruktur** und **Style**.  

Die Analyse zum UI-Part eines Tickets schaut so aus:
- Erstell eine Navigationsstruktur(Link) zum Ticket
- Wieviele Pages müssen erstellt werden?
- Wieviele Regions pro Page können erstellt werden?
- Welche Namen gibt man den Pages?
- Welche Namen gibt man den Regions?
- In welchen Ordner sollen die neuen Pages, Regions angelegt werden oder soll ein neuer Ordner angelegt werden?

Wenn es Regions gibt in der Page dann sollte die so eingebaut werden in der Page:
<ContentControl uen:Region.Attached="True" 
                uen:Region.Name="List"/>


Nun wird der Content der Pages und Views erstellt, beachte dabei folgendes:
- Arbeite nach folgendem Schema:

foreach(var page in pages)
{
foreach(var region in page.Regions)
{
//Schablone ausführen für region.View
}
}

- Die Schablone schaut folgendermaßen aus:
1. Schau welche UI-Komponenten von UnoPlatform oder uno.toolkit.ui verwendet werden sollen
2. Schau ob es schon Styles gibt für diese Komponenten die passen würden. Falls nicht erstellt Styles
3. Erstell den Content der Page
4. Es darf kein einziges Element ohne Style sein im Content, auch nicht die einfachen wie Grid, ContentControl usw. Falls ein Element noch keinen Style hat geht nochmal zu Schritt 3

