# CSV Backup Restore - Testspezifikation

## Testspezifikationsdokument
**Feature:** CSV Backup Restore für Stock Management App  
**Dokumentversion:** 1  
**Erstellt:** 31. August 2025  
**Zielanwendung:** Stock Management Smartphone App (Familienhaushalt-Nutzung)

---

## Feature-Datei: csv_backup_restore.feature

```gherkin
# language: de
Funktionalität: CSV Backup Restore
  Als Familienhaushalt-Nutzer der Lagerverwaltungs-App
  Möchte ich meine kompletten Inventardaten aus einer CSV-Backup-Datei wiederherstellen
  Damit ich Daten von einem anderen Gerät übertragen oder aus einem Backup wiederherstellen kann

  Grundlage:
    Angenommen die Lagerverwaltungs-App ist installiert und läuft
    Und der Benutzer ist auf dem Hauptbildschirm
    Und die App enthält derzeit einige vorhandene Inventardaten

  # =================================================================
  # ABSCHNITT 1: Navigation und Zugriffstests (Keine Datenänderungen)
  # =================================================================

  Regel: Benutzer können über Einstellungen auf die Wiederherstellungsfunktion zugreifen

    Szenario: Navigation zur CSV-Wiederherstellungsfunktion
      Angenommen ich bin auf dem Hauptbildschirm
      Wenn ich zu den Einstellungen navigiere
      Und ich zum Abschnitt "Backup und Restore" gehe
      Dann sollte ich eine Option "Aus CSV wiederherstellen" sehen

    Szenario: Dateiauswahl für CSV-Wiederherstellung öffnen
      Angenommen ich bin im Abschnitt "Backup und Restore" der Einstellungen
      Wenn ich auf "Aus CSV wiederherstellen" tippe
      Dann sollte eine Standard-Dateiauswahl angezeigt werden
      Und die Dateiauswahl sollte die Auswahl von CSV-Dateien ermöglichen

  # =================================================================
  # ABSCHNITT 2: Format-Validierungstests (Keine erfolgreichen Importe)
  # =================================================================

  Regel: CSV-Format-Validierung gewährleistet Datenintegrität

    Szenario: Ungültiges CSV-Header-Format
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und ich habe eine CSV-Datei "invalid_headers.csv" mit falschen Headern "Product;Qty;Level;Location;Unit"
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung anzeigen, dass das Dateiformat ungültig ist
      Und es sollten keine Daten importiert werden
      Und die App sollte weiterhin 0 Inventarelemente enthalten

    Szenario: Umgang mit leerer CSV-Datei
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 0 Inventarelemente
      Und ich habe eine leere CSV-Datei "empty_file.csv" nur mit Headern
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "0 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden" anzeigen
      Und die App sollte weiterhin 0 Inventarelemente enthalten

  # =================================================================
  # ABSCHNITT 3: Grundlegende erfolgreiche Importtests (Datenaufbau)
  # =================================================================

  Regel: Gültige CSV-Dateien werden erfolgreich verarbeitet

    Szenario: Erste erfolgreiche Wiederherstellung mit 3 grundlegenden Elementen
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 0 Inventarelemente
      Und ich habe eine gültige CSV-Datei "basic_3items.csv" mit diesen Elementen:
        | Item        | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Milk        | 2              | 1             | Küche           | bottles        |
        | Bread       | 1              | 1             | Küche           | pcs            |
        | Apples      | 5              | 3             | Küche           | pcs            |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte der Wiederherstellungsvorgang innerhalb von 15 Sekunden abgeschlossen sein
      Und ich sollte eine Abschlussmeldung sehen mit "3 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden"
      Und die App sollte genau 3 Inventarelemente enthalten

    Szenario: Vorhandene Daten mit 5 verschiedenen Elementen ersetzen
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 3 Inventarelemente aus dem vorherigen Test
      Und ich habe eine gültige CSV-Datei "household_5items.csv" mit diesen Elementen:
        | Item          | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Orange Juice  | 2              | 1             | Küche           | cartons        |
        | Pasta         | 4              | 2             | Pantry          | boxes          |
        | Olive Oil     | 1              | 1             | Pantry          | bottles        |
        | Detergent     | 3              | 1             | Bathroom        | bottles        |
        | Toilet Paper  | 12             | 6             | Bathroom        | wrapped blocks |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "5 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden" anzeigen
      Und die App sollte genau 5 Elemente enthalten (die neuen)
      Und keines der ursprünglichen 3 Elemente sollte mehr vorhanden sein

  # =================================================================
  # ABSCHNITT 4: Datenfeld-Validierungstests (Nutzt aktuellen 5-Element-Zustand)
  # =================================================================

  Regel: Datenfeldvalidierung folgt Geschäftsregeln

    Szenario: Import mit gemischten gültigen Verpackungseinheiten
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 5 Inventarelemente
      Und ich habe eine CSV-Datei "packaging_test.csv" mit diesen 6 Elementen, die verschiedene Einheiten testen:
        | Item             | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Canned Beans     | 8              | 4             | Pantry          | cans           |
        | Fresh Lettuce    | 2              | 1             | Küche           | heads          |
        | Jam              | 3              | 1             | Pantry          | jars           |
        | Butter           | 2              | 1             | Küche           | wrapped blocks |
        | Soup Mix         | 10             | 5             | Pantry          | sachets        |
        | Ice Cream        | 1              | 1             | Küche           | tubs           |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "6 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden" anzeigen
      Und die App sollte genau 6 Elemente mit korrekten Verpackungseinheiten-Übersetzungen enthalten

  # =================================================================
  # ABSCHNITT 5: Fehlerbehandlung mit gemischten gültigen/ungültigen Daten
  # =================================================================

  Regel: Ungültige Datensätze werden mit detaillierter Fehlerberichterstattung übersprungen

    Szenario: Gemischte gültige und ungültige Datensätze mit detaillierter Fehlerberichterstattung
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 6 Inventarelemente aus den Verpackungstests
      Und ich habe eine CSV-Datei "mixed_validity.csv" mit diesen 8 Datensätzen:
        | Row | Item Name       | Stock Quantity | Reorder Level | Storage Location | Packaging Unit | Validity |
        | 2   | Coffee          | 3              | 2             | Küche           | bags           | valid    |
        | 3   | Sugar           | 1              | 1             | Küche           | boxes          | valid    |
        | 4   | Expired Milk    | -2             | 1             | Küche           | bottles        | invalid  |
        | 5   | Tea             | 4              | 2             | Küche           | boxes          | valid    |
        | 6   | Special Sauce   | 1              | 1             | Garage          | invalid_unit   | invalid  |
        | 7   | Cookies         | 0              | 2             | Pantry          | boxes          | valid    |
        | 8   | Premium Coffee  |                | 1             | Pantry          | bags           | invalid  |
        | 9   | Rice            | 2              | 1             | Pantry          | bags           | valid    |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "5 Datensätze erfolgreich importiert, 3 Datensätze konnten nicht importiert werden" anzeigen
      Und die Fehlerdetails sollten "Zeile 4: Expired Milk (Küche)" enthalten
      Und die Fehlerdetails sollten "Zeile 6: Special Sauce (Garage)" enthalten
      Und die Fehlerdetails sollten "Zeile 8: Premium Coffee (Pantry)" enthalten
      Und die App sollte genau 5 gültige Elemente enthalten

    Szenario: Umgang mit doppelten Elementnamen
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 5 Inventarelemente
      Und ich habe eine CSV-Datei "duplicates.csv" mit doppelten "Coffee"-Elementen in Zeilen 2 und 5:
        | Row | Item Name | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | 2   | Coffee    | 3              | 2             | Küche           | bags           |
        | 3   | Sugar     | 1              | 1             | Küche           | boxes          |
        | 4   | Tea       | 4              | 2             | Küche           | boxes          |
        | 5   | Coffee    | 2              | 1             | Pantry          | jars           |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "3 Datensätze erfolgreich importiert, 1 Datensätze konnten nicht importiert werden" anzeigen
      Und die Fehlerdetails sollten "Zeile 5: Coffee (Pantry) - doppelter Elementname" enthalten
      Und die App sollte genau 3 Elemente enthalten
      Und das Coffee-Element sollte die Werte aus Zeile 2 haben

  # =================================================================
  # ABSCHNITT 6: CSV-Formatierung und Codierungstests
  # =================================================================

  Regel: CSV-Formatierung und Codierungsanforderungen

    Szenario: UTF-8-Codierung und Anführungszeichen mit Sonderzeichen
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 3 Inventarelemente
      Und ich habe eine UTF-8 CSV-Datei "special_chars.csv" mit diesen Elementen:
        | Item Name               | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Würstchen               | 4              | 2             | Kühlschrank     | pcs            |
        | Ben's "Special" Sauce   | 1              | 1             | Küche           | bottles        |
        | Müsli                   | 2              | 1             | Küche           | boxes          |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "3 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden" anzeigen
      Und alle deutschen Zeichen sollten korrekt als "Kühlschrank" angezeigt werden
      Und der Elementname in Anführungszeichen sollte als "Ben's \"Special\" Sauce" angezeigt werden

  # =================================================================
  # ABSCHNITT 7: Grenzfälle und Grenztests
  # =================================================================

  Regel: Grenzfälle werden angemessen behandelt

    Szenario: Alle Datensätze ungültig - keine Daten importiert
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 3 Inventarelemente aus dem vorherigen Test
      Und ich habe eine CSV-Datei "all_invalid.csv", bei der alle 4 Datensätze ungültige Daten haben:
        | Row | Item Name    | Stock Quantity | Storage Location | Issue                |
        | 2   | Bad Item 1   | -5             | Küche           | negative quantity    |
        | 3   | Bad Item 2   | 2              | Küche           | invalid_packaging    |
        | 4   |              | 1              | Küche           | empty item name      |
        | 5   | Bad Item 4   | 1              |                 | empty storage        |
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte die Abschlussmeldung "0 Datensätze erfolgreich importiert, 4 Datensätze konnten nicht importiert werden" anzeigen
      Und die App sollte weiterhin die gleichen 3 Elemente wie vorher enthalten (keine Änderungen)
      Und die Fehlerdetails sollten alle 4 fehlgeschlagenen Zeilen mit spezifischen Gründen auflisten

    Szenario: Feldlängen-Validierung
      Angenommen ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und die App enthält derzeit 3 Inventarelemente
      Und ich habe eine CSV-Datei "field_length.csv" mit einem Element mit einem 300-Zeichen-Namen
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte der Datensatz mit dem langen Namen abgelehnt werden
      Und der Fehler sollte "Feldlängen-Verletzung" und die Zeilennummer erwähnen

  # =================================================================
  # ABSCHNITT 8: Leistungstests (Verwendet großen Datensatz)
  # =================================================================

  Regel: Leistungsanforderungen werden erfüllt

    Szenario: Leistungstest mit Zielgröße (500 Elemente)
      # Hinweis: Dieser Test erfordert einen frischen App-Zustand aufgrund des großen Datensatzes
      Angenommen ich setze die App zurück auf 0 Inventarelemente
      Und ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und ich habe eine CSV-Datei "performance_500items.csv" mit 500 Elementen verteilt auf 60 Lagerstandorte
      Wenn ich die CSV-Datei auswähle und die Wiederherstellung bestätige
      Dann sollte der Wiederherstellungsvorgang innerhalb von 15 Sekunden abgeschlossen sein
      Und die Abschlussmeldung sollte "500 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden" anzeigen
      Und die App sollte genau 500 Elemente enthalten

  # =================================================================
  # ABSCHNITT 9: Datenverifikation (Verwendet den 500-Element-Datensatz vom Leistungstest)
  # =================================================================

  Regel: Datenverifikation nach erfolgreicher Wiederherstellung

    Szenario: Stichproben-Verifikation des großen Datensatzes
      Angenommen die App enthält 500 Inventarelemente aus dem Leistungstest
      Wenn ich zum Lagerstandort "Küche" navigiere (der 25 Elemente aus der CSV enthalten sollte)
      Dann sollten alle Elemente und ihre Werte exakt mit den CSV-Daten übereinstimmen
      Und deutsche Übersetzungen sollten korrekt für Verpackungseinheiten angezeigt werden
      Und alle numerischen Werte sollten mit korrekter Genauigkeit erhalten bleiben

    Szenario: Vollständige Datenanzahl-Verifikation
      Angenommen die App enthält Inventar aus dem 500-Element-Leistungstest
      Wenn ich die Gesamtelemente über alle Lagerstandorte zähle
      Dann sollte die Anzahl genau 500 Elementen entsprechen
      Und die Elemente sollten auf 60 verschiedene Lagerstandorte verteilt sein

  # =================================================================
  # ABSCHNITT 10: Benutzererfahrung und atomare Operationen
  # =================================================================

  Regel: Benutzererfahrung während des Wiederherstellungsvorgangs

    Szenario: Wiederherstellungsbestätigung und Fortschritt (mit mittlerem Datensatz)
      # Hinweis: Zurücksetzen auf bekannten Zustand für konsistente UX-Tests
      Angenommen ich setze die App zurück auf genau 10 Testelemente
      Und ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Und ich habe eine gültige CSV-Datei mit 50 Elementen ausgewählt
      Wenn ich die Dateiauswahl bestätige
      Dann sollte ich einen Bestätigungsdialog sehen, der warnt, dass alle vorhandenen Daten ersetzt werden
      Wenn ich mit der Wiederherstellung fortfahre
      Dann sollte ich eine Anzeige sehen, dass der Vorgang läuft
      Und die UI sollte reaktionsfähig bleiben
      Und bei Abschluss sollte ich "50 Datensätze erfolgreich importiert, 0 Datensätze konnten nicht importiert werden" sehen

  Regel: Fehlerwiederherstellung und Rollback-Verhalten

    Szenario: Dateizugriffsfehler-Behandlung
      Angenommen ich setze die App zurück auf genau 5 Testelemente
      Und ich bin in der Dateiauswahl für CSV-Wiederherstellung
      Wenn ich versuche, eine CSV-Datei auszuwählen, die während des Vorgangs unzugänglich wird
      Dann sollte ich eine angemessene Fehlermeldung erhalten
      Und die App sollte weiterhin die ursprünglich 5 Inventarelemente unverändert enthalten
```

## Testausführungs-Reihenfolge Hinweise

### Vorteile der sequenziellen Ausführung:
1. **Abschnitte 1-2**: Beginnen mit Navigation und Validierungstests, die keine Daten importieren
2. **Abschnitt 3**: Aufbau von 0→3→5 Elementen, Etablierung einer bekannten Grundlage
3. **Abschnitt 4**: Nutzt den 5-Element-Zustand für Verpackungsvalidierung, wächst auf 6 Elemente
4. **Abschnitt 5**: Testet Fehlerbehandlung mit gemischten Daten, nutzt aktuellen Zustand als Grundlage
5. **Abschnitt 6**: Testet spezielle Formatierung bei handhabbarer Datensatzgröße
6. **Abschnitt 7**: Grenzfälle, die die App in verschiedenen Zuständen belassen können
7. **Abschnitt 8**: Leistungstest, der Zurücksetzen aufgrund der großen Datensatzgröße erfordert
8. **Abschnitt 9**: Verifikationstests, die die 500-Element-Leistungstest-Daten nutzen
9. **Abschnitt 10**: UX-Tests, die spezifische kontrollierte Zustände erfordern

### Erforderliche Testdatendateien:
1. **invalid_headers.csv** - Falsches Header-Format
2. **empty_file.csv** - Nur Header
3. **basic_3items.csv** - 3 einfache gültige Elemente
4. **household_5items.csv** - 5 Haushaltselemente
5. **packaging_test.csv** - 6 Elemente, die alle Verpackungseinheiten testen
6. **mixed_validity.csv** - 8 Elemente (5 gültig, 3 ungültig mit spezifischen Zeilennummern)
7. **duplicates.csv** - 4 Elemente mit doppelten Namen in bestimmten Zeilen
8. **special_chars.csv** - 3 Elemente mit UTF-8 und Anführungszeichen
9. **all_invalid.csv** - 4 völlig ungültige Elemente
10. **field_length.csv** - Element mit 300-Zeichen-Namen
11. **performance_500items.csv** - 500 Elemente verteilt auf 60 Standorte

### Zurücksetz-Punkte:
- **Leistungstest** (Abschnitt 8): Erfordert Zurücksetzen aufgrund der Datensatzgröße
- **UX-Tests** (Abschnitt 10): Erfordern kontrollierte Zustände für konsistente Tests

Diese Organisation minimiert den Setup-Aufwand und gewährleistet gleichzeitig umfassende Abdeckung und realistischen Testfluss.