# CLAUDE.md – Glücksrad Web-App (Wheel of Fortune)

Diese Datei dient als Projektanleitung für Claude Code, um eine Glücksrad-Web-App im Stil von "Wheel of Fortune" zu entwickeln. Sie enthält Projektkontext, Architekturvorgaben, Feature-Beschreibungen und Implementierungsrichtlinien.

## 1. Projektüberblick

**Name:** Glücksrad – Random Name Picker
**Typ:** Single-Page Web-Anwendung (statisch)
**Zweck:** Eine interaktive Web-App, in die der Nutzer Namen eingeben kann. Per Klick startet ein animiertes Glücksrad, das sich dreht und am Ende einen Zufallsgewinner ermittelt und feierlich präsentiert.

**Zielgruppe:** Lehrkräfte, Moderatoren, Event-Organisatoren, Teams – überall dort, wo eine faire und unterhaltsame Zufallsauswahl benötigt wird (z. B. Schulklassen, Verlosungen, Team-Meetings).

## 2. Tech-Stack

- **HTML5** – Struktur (eine einzige `index.html`)
- **CSS3** – Styling, Animationen (Keyframes, Transforms)
- **Vanilla JavaScript (ES6+)** – Logik, Canvas-Rendering, Event-Handling
- **HTML5 Canvas API** – Zeichnen des Glücksrads
- **Web Audio API** – Sound-Effekte
- **Keine Build-Tools, keine Frameworks, keine externen npm-Pakete.**
- Direkt im Browser per Doppelklick auf `index.html` lauffähig.

Optional über CDN (nur wenn nötig):
- `canvas-confetti` für Konfetti-Animation (https://cdn.jsdelivr.net/npm/canvas-confetti)

## 3. Projektstruktur

```
/
├── index.html          # Hauptdatei mit kompletter App
├── style.css           # (optional) ausgelagertes Styling
├── script.js           # (optional) ausgelagerte Logik
├── assets/
│   ├── spin.mp3        # Drehgeräusch
│   └── win.mp3         # Gewinner-Fanfare
└── CLAUDE.md           # Diese Datei
```

**Hinweis:** Bevorzugt eine Single-File-Lösung (`index.html` mit Inline-CSS und -JS), damit die App ohne Server lauffähig ist.

## 4. Features

### 4.1 Kern-Features (MVP)

1. **Namens-Eingabe**
   - Textarea oder Eingabefeld, in das Namen (einer pro Zeile oder kommagetrennt) eingegeben werden.
   - Button "Hinzufügen" / "Liste aktualisieren".
   - Anzeige der aktuellen Teilnehmerliste mit Möglichkeit, einzelne Namen zu entfernen (X-Button).
   - Mindestens 2 Namen erforderlich, bevor das Rad gedreht werden kann.

2. **Glücksrad-Visualisierung (Canvas)**
   - Kreisförmiges Rad, in gleich große Segmente unterteilt – je nach Anzahl der Namen.
   - **Klassisch bunte Farben** (Wheel-of-Fortune-Stil): Rot, Orange, Gelb, Grün, Blau, Lila, Pink, Türkis – Farben rotieren durch.
   - Namen werden zentriert in den Segmenten angezeigt (gegebenenfalls Schriftgröße dynamisch anpassen).
   - Pfeil/Marker am oberen Rand des Rads, der den Gewinner anzeigt.
   - Mittelkreis mit Logo oder "SPIN!"-Text.

3. **Drehmechanik**
   - Großer "Drehen!"-Button startet die Animation.
   - Rad dreht sich mit zufälligem Endwinkel; Rotation mit Easing-Funktion (langsamer Auslauf – `ease-out` oder eigene Funktion wie `cubic-bezier`).
   - Drehdauer: ca. 4–6 Sekunden.
   - Während der Drehung ist der Button deaktiviert.

4. **Gewinner-Anzeige**
   - Nach Stillstand wird der Gewinner-Name groß auf dem Bildschirm präsentiert (Modal oder Overlay).
   - Button zum Schließen / "Nochmal drehen".
   - Optional: Gewinner aus dem Pool entfernen (Toggle).

### 4.2 Erweiterte Features

5. **Konfetti-Animation**
   - Beim Bekanntgeben des Gewinners regnet Konfetti vom oberen Bildschirmrand.
   - Implementierung: `canvas-confetti` (CDN) oder eigene Canvas-Animation.

6. **Gewinner-Historie**
   - Seitenleiste oder Liste, die alle bisherigen Gewinner mit Zeitstempel speichert.
   - Persistenz über `localStorage` (damit die Liste nach Neuladen erhalten bleibt).
   - Button "Historie löschen".

7. **Sound-Effekte**
   - Drehgeräusch während der Rotation (Loop oder Tick-Sound, der mit der Drehgeschwindigkeit korreliert).
   - Fanfare/Jubel beim Gewinner.
   - Mute-Button (oben rechts) zum Stummschalten.
   - Sound-Dateien im `assets/`-Ordner, eingebunden über `Audio`-API.

8. **CSV-Import**
   - Datei-Upload-Button (`<input type="file" accept=".csv,.txt">`).
   - Parser liest die Datei zeilenweise oder kommagetrennt aus und füllt die Teilnehmerliste.
   - Validierung: Leere Zeilen ignorieren, Duplikate optional zusammenführen.

## 5. Implementierungsrichtlinien

### 5.1 Code-Stil

- **Sprache:** Variablen, Funktionen und Kommentare auf Deutsch oder Englisch (konsistent halten).
- **UI-Texte:** Deutsch.
- ES6+-Features verwenden (`const`/`let`, Arrow Functions, Template Literals, Destructuring).
- Keine globalen Variablen außer einem zentralen `app`-Objekt.
- Code in logische Funktionen unterteilen: `drawWheel()`, `spinWheel()`, `pickWinner()`, `addName()`, `removeName()`, `showWinner()`, etc.

### 5.2 Glücksrad-Mathematik

- Segmentwinkel: `2π / Anzahl_Namen`
- Zufälliger Endwinkel: `currentRotation + (5 bis 10 volle Umdrehungen) + zufälliger Offset`
- Gewinner-Bestimmung anhand des finalen Winkels relativ zum Pfeil (12-Uhr-Position):
  ```
  normalizedAngle = (totalRotation % 2π)
  winnerIndex = floor((2π - normalizedAngle) / segmentAngle) % numberOfNames
  ```
- Animation per `requestAnimationFrame` mit Easing:
  ```
  easeOut(t) = 1 - Math.pow(1 - t, 3)
  ```

### 5.3 Design (Wheel-of-Fortune-Stil)

- **Farbpalette für Segmente:** `#FF6B6B`, `#FFA500`, `#FFD93D`, `#6BCB77`, `#4D96FF`, `#9B59B6`, `#FF6FAE`, `#1ABC9C`
- **Hintergrund:** dunkles Blau oder Lila (`#1a1a2e` oder Verlauf), damit das Rad hervorsticht.
- **Schrift:** Bold, Sans-Serif (z. B. `'Poppins'`, `'Montserrat'` über Google Fonts oder System-Stack).
- **Goldene Akzente** für Pfeil, Rahmen und Buttons (`#FFD700`).
- Responsive: Funktioniert auf Desktop und Tablet (Mindestbreite 320 px).

### 5.4 UX-Hinweise

- Klare visuelle Hierarchie: Rad mittig/groß, Eingabefelder seitlich oder darunter.
- Buttons mit Hover- und Active-States.
- Animationen flüssig (mind. 60 fps) – Canvas-Rendering optimieren.
- Tastaturbedienung: Enter im Eingabefeld fügt Namen hinzu; Leertaste startet das Rad.
- Eingabevalidierung: Keine leeren Namen, max. 50 Zeichen pro Name, max. 100 Namen für sinnvolle Darstellung.

## 6. Entwicklungsschritte (empfohlene Reihenfolge)

1. Grundgerüst `index.html` mit Canvas und Eingabefeldern erstellen.
2. Statisches Rad mit fest hinterlegten Test-Namen zeichnen.
3. Funktion zum dynamischen Hinzufügen/Entfernen von Namen + Neuzeichnung.
4. Drehanimation mit `requestAnimationFrame` und Easing implementieren.
5. Gewinner-Berechnung und -Anzeige (Modal).
6. Persistenz (`localStorage`) für Namensliste und Historie.
7. CSV-Import.
8. Sound-Effekte einbinden + Mute-Button.
9. Konfetti-Animation einbauen.
10. Styling polieren, Responsiveness testen, Edge Cases behandeln (1 Name, sehr viele Namen, Sonderzeichen).

## 7. Test-Szenarien

- Rad mit 2, 5, 10, 50, 100 Namen drehen – Lesbarkeit prüfen.
- Sehr lange Namen (z. B. "Maximilian-Wolfgang von Habsburg-Lothringen") – Truncation oder Wrapping testen.
- Namen mit Umlauten und Sonderzeichen (ä, ö, ü, ß, é, 漢字, Emojis).
- Mehrfaches Drehen hintereinander – Animation darf nicht hängen bleiben.
- Browser-Refresh: Liste und Historie bleiben erhalten.
- Mute-Button vor und während des Drehens.
- CSV-Import mit gültigen, ungültigen und leeren Dateien.

## 8. Mögliche Erweiterungen (Nice-to-have)

- Mehrere gespeicherte Listen (z. B. "Klasse 5a", "Team Marketing") mit Switch-Funktion.
- Export der Historie als CSV oder PDF.
- Mehrsprachigkeit (Deutsch / Englisch).
- Anpassbare Segmentfarben.
- Dark-/Light-Mode-Toggle.
- Animations-Geschwindigkeit einstellbar.
- "Loser-Modus" – der ausgewählte Name fliegt aus dem Rad.
- QR-Code zum Teilen einer vorgefertigten Liste.

## 9. Deployment / Git-Hinweis

**Git ist auf diesem System NICHT installiert.** Zum Pushen auf GitHub immer die **GitHub REST API mit dem PAT** aus der `.env`-Datei verwenden.

PowerShell-Muster zum Hochladen einer Datei:

```powershell
$token = (Get-Content ".env" | Where-Object { $_ -match "^GITHUB_TOKEN=" }) -replace "^GITHUB_TOKEN=", ""
$headers = @{ Authorization = "token $token"; "User-Agent" = "PowerShell"; "Content-Type" = "application/json" }

function Push-FileToGitHub {
    param($filePath, $repoPath)
    $content = [Convert]::ToBase64String([System.IO.File]::ReadAllBytes($filePath))
    # SHA abrufen falls Datei schon existiert (für Updates nötig)
    try { $existing = Invoke-RestMethod -Uri "https://api.github.com/repos/Locker18/gluecksrad/contents/$repoPath" -Headers $headers; $sha = $existing.sha } catch { $sha = $null }
    $body = @{ message = "Update $repoPath"; content = $content; sha = $sha } | ConvertTo-Json
    Invoke-RestMethod -Uri "https://api.github.com/repos/Locker18/gluecksrad/contents/$repoPath" -Method Put -Headers $headers -Body $body | Out-Null
    Write-Output "Hochgeladen: $repoPath"
}
```

- `.env` und `CLAUDE.md` niemals hochladen.
- Repository: `Locker18/gluecksrad`
- GitHub Pages: https://locker18.github.io/gluecksrad/

## 10. Hinweise für Claude

- **Beginne mit einer funktionierenden MVP-Version in einer einzigen `index.html`-Datei.** Erst danach Auslagerung und Erweiterungen.
- **Halte den Code lesbar und kommentiert** – das Projekt soll auch als Lernobjekt dienen.
- **Teste das Rad in mehreren Konfigurationen** (verschiedene Namensanzahlen) bevor du fertig meldest.
- **Erkläre keine Trivialitäten**, aber dokumentiere mathematische Berechnungen (Winkel, Rotation, Gewinner-Index) im Code.
- Verwende **moderne Browser-APIs** (Canvas, Audio, FileReader, localStorage) – kein Polyfill für IE notwendig.
- Bei Sound-Dateien: Falls keine eigenen Dateien vorhanden sind, kann auf öffentliche Royalty-free-Quellen verwiesen oder ein einfacher synthetischer Ton via Web Audio API erzeugt werden.
