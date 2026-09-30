# PAUK Fahrzeugbewertung V3 – Fotos und Schadensprotokoll

## Bestehende Cloudflare-App aktualisieren
1. ZIP entpacken.
2. In deinem bestehenden GitHub-Repository `pauk-automotive-ki`: Add file → Upload files.
3. `index.js`, `wrangler.jsonc` und `package.json` aus diesem Paket auf die oberste Ebene hochladen; vorhandene Dateien ersetzen. Kein zusätzlicher Unterordner und keine ZIP hochladen.
4. Commit changes. Cloudflare baut das Update automatisch. `OPENAI_API_KEY` bleibt als Secret im bestehenden Worker. Optionales `OPENAI_VISION_MODEL` bleibt unterstützt; Standardmodell unverändert.
5. App neu laden. Die Schritte 5 und 6 erscheinen nach einer neuen Analyse.

## Für Anwender
Fahrzeugdaten eintragen, acht Ansichten und Schadensdetails aufnehmen, API testen und Bewertung starten. Jede Position besitzt eine Nummer. Unter Schritt 5 Schaden und Foto wählen. Rahmen ziehen; Ecke unten rechts zum Vergrößern ziehen. Für weitere Ansichten desselben Schadens einen Rahmen hinzufügen. Fehlende Treffer manuell ergänzen. Bauteil und Tabellenposition korrigieren, Prüfnotiz eintragen und Position bestätigen. Eine geänderte Tabellenposition oder Markierung hebt die Bestätigung auf.

Gemeinsame Reparaturarbeiten: Eine doppelte Kostenposition aus der Summe ausschließen und den Grund notieren. Es erfolgt keine automatische Zusammenfassung verschiedener Reparaturmethoden.

Prüfer angeben und Schadensprotokoll erstellen. Der Bericht enthält alle Fotos, nummerierte Markierungen bestätigter Positionen, Detailausschnitte, Kosten, ausgeschlossene Positionen und Prüfnotizen. „Drucken / PDF“ öffnet den Browserdruckdialog. Am iPhone Druckvorschau öffnen und über Teilen als PDF sichern. Leere Schadensliste kann ebenfalls als Sichtprüfbericht exportiert werden.

## Grenzen und Daten
KI-Rahmen sind Vorschläge. Die KI-Einschätzung ist keine kalibrierte Wahrscheinlichkeit. S4 und sicherheitsrelevante Bereiche brauchen technische Prüfung; verdeckte Schäden sind nicht erfasst. Preise stammen aus der bisherigen PAUK-Tabelle und sind Richtwerte.
Originaldateien werden nicht überschrieben. Vollauflösende Fotos bleiben für den Bericht in der Sitzung; an die API gehen verkleinerte JPEGs. Kein dauerhafter Fall-Speicher: vor Neuladen den Bericht speichern. API-Schlüssel bleibt im Worker-Secret. Modell-Erreichbarkeit bestätigt kein verfügbares Guthaben.

## Validierung
JavaScript-Syntax, Backend-Schema, Preiszuordnung, Pflichtansichten und Koordinatenvalidierung mit simulierten KI-Antworten geprüft. Ein vollständiger Browser-/iPhone-Test steht aus. Keine kostenpflichtige Live-Bildanalyse während der Erstellung durchgeführt.
