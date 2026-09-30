# PAUK Fahrzeugbewertung V5 – Bewertung, Marktvergleich und Privatverkaufsvermittlung

## Was enthalten ist
- Marke und Modell mit Suchvorschlägen aus einer angebundenen Datenquelle.
- Untermodell, Ausstattung, Motorcode, Kraftstoff, Leistung, Hubraum, Getriebe, Modelljahr, Erstzulassung, Kilometerstand und Karosserieform.
- Basis-VIN-Abfrage bei NHTSA vPIC, mit Ergebnisvorschau und bestätigter Übernahme.
- Import eines berechtigt bezogenen JSON-Fahrzeugkatalogs für Modell-/Untermodell-/Motorisierungsvorschläge.
- Optionale Anbindung eines lizenzierten Fahrzeugdaten-Adapters.
- Historieneinträge mit Quelle, Datum und Kilometerstand sowie PDF-/Fotobelege.
- Dauerhaftes, passwortgeschütztes Archiv für vollständige Fahrzeugfälle und Entwürfe, inklusive Originalfotos, Markierungen, Bewertung, Historie und Belegen.
- Jeder Speichervorgang erstellt einen unveränderlichen neuen Archivstand. Beim Öffnen und erneuten Speichern bleibt der Vorgänger erhalten.

## Einmalige Einrichtung in Cloudflare
Das bisherige Projekt hat noch keinen dauerhaften Speicher. Das Archiv benötigt einen privaten R2-Bucket. Er kann über das Cloudflare-Dashboard eingerichtet werden; kein OpenAI-Key muss geändert werden.

1. Cloudflare → R2 Object Storage: bei Bedarf R2 aktivieren und einen Bucket namens `pauk-fahrzeugarchiv` erstellen. Öffentlichen Zugriff deaktiviert lassen. Cloudflare kann die Einrichtung einer Abrechnung verlangen; Kosten richten sich nach dem dortigen Tarif.
2. Im entpackten Paket `wrangler.jsonc` öffnen und vor der letzten geschweiften Klammer nach dem vorhandenen letzten Eintrag ein Komma und folgende Eigenschaft ergänzen:
   "r2_buckets": [{"binding":"ARCHIVE","bucket_name":"pauk-fahrzeugarchiv"}]
   Die vollständige Vorlage liegt in `wrangler.archive.example.jsonc`. Erst verwenden, wenn der Bucket existiert. Der Worker-Name muss dem vorhandenen Projekt entsprechen.
3. Im bestehenden GitHub-Repository die entpackten Projektdateien ersetzen. `index.js` bleibt im Hauptverzeichnis. Commit changes. Cloudflare baut das Update.
4. Im bestehenden Worker unter Settings → Variables and Secrets ein Secret `ARCHIVE_PASSWORD` mit mindestens 16 Zeichen anlegen. Ein langes zufälliges Passwort verwenden und an berechtigte Mitarbeiter weitergeben. `OPENAI_API_KEY` bleibt bestehen.
5. Bereitstellen/neu bereitstellen. In der App unter Schritt 8 anmelden. Sobald `ARCHIVE_PASSWORD` gesetzt ist, benötigen auch Fahrzeugdaten- und KI-Aufrufe diese Anmeldung.

Die voreingestellte `wrangler.jsonc` enthält bewusst keine nicht existierende Bucket-Bindung. Ohne Archiv-Einrichtung wird in der App ein konkreter Einrichtungshinweis angezeigt. Fotos werden dann weiterhin nur in der Sitzung gehalten.

## Bedienung
1. Im Archiv anmelden.
2. Marke und Modell eingeben oder VIN mit 17 Zeichen abfragen. Ergebnis mit den Fahrzeugpapieren vergleichen und übernehmen. Leere Datenfelder werden nicht überschrieben.
3. Fehlende Untermodelle/Motorisierungen frei ergänzen oder einen berechtigten Katalog importieren. Vorschläge sind keine Vollständigkeitsgarantie.
4. Fotos aufnehmen, KI analysieren lassen und Schäden/Markierungen prüfen.
5. Historieneinträge ergänzen und Belege hochladen. Belegquelle im Feld „Quelle“ eintragen.
6. „Aktuellen Stand archivieren“ drücken. Erst die Erfolgsmeldung bestätigt, dass alle Dateien und der Fall gespeichert wurden. Entwürfe können vor einer Analyse gespeichert werden.
7. Archiv durchsuchen, gewünschten Stand öffnen, ggf. Änderungen vornehmen und erneut speichern. Nicht gespeicherte Änderungen werden vor dem Ersetzen einer laufenden Sitzung abgefragt.
8. Schadensprotokoll erzeugen und über Drucken / PDF sichern. Fahrzeugdaten und Historie stehen im Bericht; PDF-Belege bleiben separat abrufbar im Archiv.

Die Suche arbeitet mit Seiten von jeweils bis zu 100 Archivständen. Bei „Weitere Archivstände durchsuchen“ werden weitere Seiten mit dem gleichen Suchbegriff abgefragt; kein Treffer auf einer Seite bedeutet nicht, dass das ganze Archiv keinen Treffer enthält.

## Datenquellen und Vollständigkeit
NHTSA vPIC ist eine Basisquelle aus Herstellerangaben für den US-Kontext. Eine erfolgreiche Anfrage garantiert keine vollständige Dekodierung einer europäischen VIN. Sie liefert keine Service-, Unfall-, Kilometerstands- oder Besitzerhistorie. Fehlende Daten werden nicht von der KI erfunden.

Alle verfügbaren europäischen Typen inklusive Varianten und Motorisierungen erfordern einen vollständigen, aktuellen, berechtigt bezogenen Katalog bzw. eine Lizenz (beispielsweise ein Fahrzeugidentifikationsanbieter). In dieser Lieferung ist kein kostenpflichtiger Gesamtkatalog enthalten. `Fahrzeugkatalog_Format.json` zeigt ausschließlich das Importformat, keine echten Fahrzeugdaten. Import ersetzt den bestehenden Katalog und wird im privaten R2-Speicher gehalten. Datenanbieter-Schlüssel werden ausschließlich serverseitig hinterlegt.

Direkte Herstellerhistorien sind nicht universell öffentlich abrufbar. Herstellerportale, Partnerberechtigungen und gegebenenfalls Einwilligungen sind erforderlich. Ohne einen tatsächlich berechtigten Adapter bleibt die Herstellerhistorie unbelegt. Als Alternative können offizielle Auszüge/Servicebelege importiert werden. Herkunft und Authentizität importierter Belege werden nicht automatisch geprüft. Keine Historiedaten bedeuten nicht „unfallfrei“.

Offizielle Informationen:
https://vpic.nhtsa.dot.gov/api/Home/Index
https://www.dat.de/fileadmin/de/download/rechtliches/produktbeschreibung-silverdat-vin-abfrage.pdf
https://www.bmwgroup.com/en/general/regulations/cardata.html
https://erwin.vwgroup-datahub.com/support-center
https://developers.cloudflare.com/r2/api/workers/workers-api-reference/

## Optionaler lizenzierter VIN-/Historie-Adapter
Der Adapter ist eine von euch/euren Datenanbietern bereitgestellte Schnittstelle, keine bereits eingerichtete DAT- oder Herstelleranbindung.

Worker-Variablen:
- `VEHICLE_DATA_URL`: HTTPS-Adresse des Adapters
- `VEHICLE_DATA_TOKEN`: Secret zur Anmeldung beim Adapter

Die App ruft serverseitig POST auf mit {"vin":"...","year":"2020"} und Authorization: Bearer <TOKEN>.
Antwortformat siehe `Adapter_Antwort_Format.json`. Der Adapter muss die dortigen Fahrzeugfelder und Historie aus seiner echten Datenquelle befüllen. Quelle, Abrufzeit und VIN-Antwort werden mit dem Fall archiviert. Die Lizenz-/Zugriffsrechte müssen vom Datenanbieter erteilt werden. Keine frei erfundene Herstellerhistorie und keine automatische Portal-Anmeldung.

## Archivtechnik und Grenzen
Binding `ARCHIVE` ist ein privater Cloudflare-R2-Bucket. Originaldateien werden einzeln gespeichert, maximal 15 MB je Datei. JPEG, PNG, WebP und PDF werden unterstützt. Ein Fall enthält bis zu 32 Fotos und 32 Belege. Zu große Dateien werden zurückgewiesen und nicht stillschweigend verkleinert. Scheitert ein Speichervorgang, wird kein vollständiger Archivstand bestätigt; schon hochgeladene Dateien können als nicht zugeordnete Objekte verbleiben. Es gibt in dieser Version keine automatische Bereinigung und keine Löschfunktion.

Alle Mitarbeiter mit dem gemeinsamen Passwort können alle Archivstände lesen und neue anlegen. Individuelle Benutzerkonten und Rollen sind nicht enthalten. Das Passwort bleibt nur in der laufenden Browser-Sitzung im Arbeitsspeicher; nach Neuladen erneut anmelden. Bucket nicht öffentlich machen. Dateien werden nur über authentifizierte Worker-Aufrufe abgerufen.

Der Bericht wird aus gespeicherten Daten neu erzeugt; eine einmal exportierte PDF-Datei wird nicht zusätzlich automatisch im Archiv gespeichert. KI-Anfragen brauchen verfügbares API-Guthaben. Speicher ist nach erfolgreicher Cloudflare-Einrichtung geräteübergreifend abrufbar; Ausfälle und Änderungen am Cloudflare-Konto können die Verfügbarkeit beeinflussen.

## Prüfung
Chromium-Integrationstests mit simuliertem VIN-/KI-Datenanbieter und simuliertem R2-Speicher bestanden: VIN-Übernahme, Untermodell, Historie, acht Originalfotos speichern, Neuladen und vollständiges Wiederöffnen, ältere Archivstände erhalten, Passwortschutz, Katalogimport und Historie im Bericht. JavaScript-Syntax geprüft. Keine kostenpflichtigen Anbieter-/Hersteller-Live-Abfragen; kein echter Cloudflare-R2-Deploymenttest während der Erstellung.


## Neu in V5
Die Oberfläche hat eine Fahrzeugübersicht, eine Navigation durch Fahrzeug, Fotos, Zustand, Marktpreis und Inserat sowie eine ständig sichtbare Bewertungsübersicht auf großen Bildschirmen. Am Smartphone bleibt die Übersicht kompakt. Die Navigation springt zu den jeweiligen Abschnitten; der Fall bleibt vollständig zugänglich.

- Originalfotos mit Vorschau und Hinweisen zu Auflösung, Helligkeit und Bilddetails. Diese Heuristiken prüfen nicht zuverlässig, ob eine Ansicht korrekt oder ein Schaden erkennbar ist.
- 8 Pflichtansichten und 12 optionale Ansichten: Räder, Schadendetails, Innenraum, Tacho und VIN.
- VIN und Kilometerstand aus Fotos als KI-Vorschlag lesen; Übernahme nach menschlicher Prüfung.
- Markierungen ziehen, skalieren und über Prozentwerte fein einstellen. Beschreibung bearbeiten; technische Prüfung separat dokumentieren.
- Hinweise zu möglichen doppelten Reparaturarbeiten, Begründung ausgeschlossener Positionen, Tabellenübersicht und kompakter Fotobericht.
- Früheren Archivstand derselben VIN zum visuellen Vergleich öffnen; Schäden manuell als bestehend, neu oder verändert zuordnen. Keine automatische Aussage über Verursachung oder Zeitpunkt.
- Lokale IndexedDB-Entwürfe, JSON-Fallkopie und Wiederimport. Passwörter werden nicht in diesen Entwürfen gespeichert. Lokale Daten können durch Browserbereinigung verloren gehen.
- Archivdateien mit Formatprüfung und SHA-256-Prüfsummen; unveränderliche Archivstände; Bereinigung abgebrochener Uploads nur bei noch nicht gespeicherten Ständen.
- Sperre paralleler Bearbeitungsaktionen während Analyse, Laden und Speichern; Zeitlimits bei API-Anfragen.

## Preisvergleich
Vergleiche aus willhaben, gebrauchtwagen.at, AutoScout24 und Das WeltAuto lassen sich manuell erfassen, als JSON importieren oder über einen eigenen lizenzierten Adapter abrufen. Es gibt keine aktivierte Direktanbindung an diese Anbieter und kein Plattform-Scraping.

Einbezogen werden nur manuell als vergleichbar geprüfte Angebote mit Datum innerhalb von 30 Tagen, passendem Verkäufertyp und den eingestellten Toleranzen für Erstzulassungsjahr und km. Marke, Modellgeneration, Motor, Getriebe und Zustand müssen fachlich geprüft werden. Gleiche Links und gleiche manuell vergebene Fahrzeug-IDs werden nur einmal gezählt. Plattformübergreifende Doppelanzeigen benötigen eine gemeinsame Fahrzeug-ID.

Berechnung: ungewichteter Median und interpoliertes 25./75. Perzentil der Angebotspreise. Ab drei Angeboten kann der Median als Preisentwurf übernommen werden; unter acht Angeboten wird auf die kleine Stichprobe hingewiesen. Keine Schätzung tatsächlich erzielter Verkaufspreise, Verkaufstage, Nachfrage oder KI-Genauigkeit. Eurotax-Einkaufs-, Händlerverkaufs- oder Privatverkaufswerte werden separat mit Datum und Nachweis erfasst. Sie fließen nicht in den Angebotsmedian ein. Zustand wird nur über eine begründete manuelle Korrektur berücksichtigt; Reparaturkosten sind kein automatischer Minderwertabzug.

Die Preisanalyse bezieht sich auf den aktuellen gespeicherten Datenbestand, nicht auf ein vollständiges Marktinventar. Vergleichsstände werden mit jedem Archivstand aufbewahrt. Datenbeschaffung und gewerbliche Verwendung setzen passende Nutzungsrechte voraus.

### Optionaler Marktdaten-Adapter
Als Worker-Variable `MARKET_DATA_URL` eine eigene HTTPS-Adapteradresse setzen und `MARKET_DATA_TOKEN` als Secret. Der Adapter ist keine enthaltene Anbieter-Lizenz. Authentifizierter Aufruf über `/market/offers` sendet POST mit `vehicle`, `market: "AT"`, `currency: "EUR"`. VIN, Kennzeichen und Eigentümerkontakt werden nicht mitgesendet. Die Antwort enthält `source`, `offers` und optional `warnings`; Schema siehe `Marktvergleich_Format.json`. Maximal 500 Angebote, bis 2 MB Antwort. Jeder importierte Vorschlag bleibt zunächst ungeprüft. Ohne Adapter meldet die App ausdrücklich, dass die Datenquelle fehlt.

## Inserate und Vermittlungsauftrag
Der private Kunde bleibt Verkäufer und Vertragspartner des Käufers. PAUK wird als Vermittler benannt. Eigentümer, interner Kontakt, Mindestpreis, Aufwandsentschädigung und Auftragsreferenz werden intern gespeichert. Inseratpreis, öffentlicher Ort und Vermittlerkontakt werden separat erfasst.

Drei Textstile erstellen Vorschläge aus eingegebenen Fahrzeugdaten, belegter Ausstattung, dokumentierter Wartung und bestätigten Schäden. Es werden keine Eigenschaften wie Unfallfreiheit, Garantie, lückenlose Historie oder perfekte Fahrzeugqualität erfunden. Weitere bekannte Mängel sind manuell zu ergänzen. Die Redaktion ersetzt keine vollständige Fahrzeugprüfung.

Vor TXT-/JSON-Export verlangt die App geprüfte Angaben, einen Auftragsnachweis und die aktuelle Eigentümerfreigabe samt Referenz. Änderungen an den zugrunde liegenden Daten machen die Freigabe ungültig. Diese Dokumentation ist kein elektronischer Unterschriftsdienst. Die vollständige Liste öffentlicher Exportfelder steht in der Implementierung von `exportListing`; VIN, Kennzeichen, interner Eigentümerkontakt, Gebühren, Mindestpreis und Belege gehören nicht dazu. Freitext wird zusätzlich auf mehrere interne Identifikatoren geprüft; eine menschliche Datenschutzprüfung bleibt erforderlich.

Das exportierte JSON ist ein neutrales PAUK-Format, kein bestätigtes Importformat einer Plattform. Es wird nichts automatisch veröffentlicht. Fotos werden nicht mitexportiert; Kennzeichen, Gesichter und Dokumente sind vor einer Veröffentlichung gesondert zu prüfen. Das WeltAuto ist ein Händlerkanal; ein Zugang bzw. eine Partnerschaft darf nicht vorausgesetzt werden.

Ein pauschales „ohne Gewährleistung“ wird nicht eingefügt. In Österreich hängen Gewährleistungsregeln von den tatsächlichen Vertragsparteien und Umständen ab. Vermittlungsvertrag, Leistungsumfang, Gebührenfälligkeit und Kaufvertrag müssen vor kommerziellem Betrieb für dieses Geschäftsmodell juristisch geprüft werden. Die eigene Vermittlungsleistung von PAUK bleibt gesondert zu beurteilen.

## Validierung und Grenzen
Browserintegration mit simuliertem OpenAI-/VIN-Dienst und R2-Speicher geprüft: VIN-Übernahme, Historie, Originalfotos, Archivversionen, Anmeldung, Katalogimport, OCR-Vorschau, Vergleich, lokale Wiederherstellung, Marktmedian, Eurotax-Trennung, Exportfreigabe, Datenminimierung und Wiederöffnung des Verkaufsfalls. Adapter separat auf Authentifizierung, Datenminimierung und unkonfigurierte Antwort geprüft. Desktop und Smartphone visuell geprüft.

Live-Zugänge zu Eurotax, Herstellern und Plattformen, tatsächliche KI-Trefferqualität, Cloudflare-Abrechnung und Produktionsdeployment sind nicht durch diese lokalen Tests geprüft. Das Archiv verwendet ein gemeinsames Mitarbeiterpasswort; es ist kein öffentliches Kundenportal mit getrennten Konten. Für den Kundenzugang sind separate Identitäten, Fallberechtigungen und geeignete Aufbewahrungs-/Löschprozesse erforderlich. „Jederzeit abrufbar“ setzt korrekte Einrichtung, Verfügbarkeit und Backups voraus.
