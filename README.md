# BLOCKSTORM

Endloses 8×8-Block-Puzzle als installierbare PWA. Eine `index.html`, kein Build-Schritt, kein Framework.

## Dateien

| Datei | Zweck |
|---|---|
| `index.html` | Das komplette Spiel — HTML, CSS, JS, Canvas-Engine, Sound |
| `manifest.json` | PWA-Manifest |
| `sw.js` | Service Worker, macht das Spiel offline lauffähig |
| `icon-192.png` / `icon-512.png` | App-Icons |
| `icon-maskable-512.png` | Android-Icon für runde/adaptive Formen |
| `apple-touch-icon.png` | iOS-Homescreen-Icon |

Alle Dateien flach ins gleiche Verzeichnis. Dateinamen nicht ändern, sonst greift die Installationsaufforderung auf Android nicht.

## Auf GitHub Pages bringen

1. Repository anlegen, z. B. `blockstorm`.
2. Die sechs Dateien in den Wurzelordner hochladen.
3. **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`** → Save.
4. Nach ein bis zwei Minuten läuft es unter `https://<dein-name>.github.io/blockstorm/`.
5. Am Handy im Browser öffnen → Menü → *Zum Startbildschirm hinzufügen*.

Unterordner funktionieren auch, alle Pfade sind relativ.

## Nach einem Update

In `sw.js` die Zeile `const CACHE = "blockstorm-v2";` hochzählen. Ohne das liefert der Service Worker die alte Version weiter aus.

## Steuerung

- Teil aus der Ablage nach oben ziehen und aufs Feld setzen. Es erscheint über dem Finger.
- Teile lassen sich nicht drehen.
- **Platzier-Hilfe**: sitzt der Finger knapp daneben, rutscht das Teil auf die nächste gültige Position. In den Optionen abschaltbar.
- Blasse Teile in der Ablage passen nirgends mehr.
- Kleine Striche am Feldrand markieren Reihen und Spalten, denen genau ein Block fehlt.
- 🔨 antippen, dann einen Block antippen: der Block verschwindet.
- 🔄 tauscht alle verbleibenden Teile aus.

## Punkte

| Ereignis | Wert |
|---|---|
| Block gesetzt | 2 pro Zelle |
| 1 Linie | 100 |
| 2 Linien | 300 |
| 3 Linien | 600 |
| 4 Linien | 1000 |
| 5 Linien | 1500 |
| aufgelöste Zelle | +5 |
| Perfect Clear | 5000 × Combo |

**Combo-Multiplikator** = `1 + 0,4 × (Combo − 1)`, gedeckelt bei Combo 30 (×12,6).
Ein Zug ohne Auflösen setzt die Combo zurück — ab Combo 5 wird sie nur halbiert.

**Chain-Wellen**: Sprengt ein Spezialblock weitere Spezialblöcke frei, zündet die nächste Welle mit ×1,5, ×2, ×2,5 …

## Spezialblöcke

| Symbol | Wirkung |
|---|---|
| ◆ Gem | +300 Bonus |
| ✦ Star | +200 und räumt die komplette Kreuzlinie |
| ● Bombe | +150 und sprengt 3×3 — löst die meisten Ketten aus |
| ×2 Boost | verdoppelt die Punkte der ganzen Welle, mehrere stapeln multiplikativ |

Rund 10 % Chance pro Teil zu Beginn, bis ~34 % in hohen Levels.

## Teile-Generator

Vor jedem neuen Dreiersatz wird gewürfelt und bewertet: Wie viele Positionen gibt es für jedes Teil, kann eines eine Linie schließen, und existiert eine Reihenfolge, in der alle drei Platz finden? Passt der Satz nicht gut genug, wird neu gewürfelt — bis zu zehnmal.

Gemessen bei 72 % Brettbelegung:

| | rein zufällig | mit Generator |
|---|---|---|
| Satz komplett spielbar | 87 % | 100 % |
| alle drei einzeln platzierbar | 29 % | 89 % |
| sofortiges Game Over | 8 % | 0 % |

Je nach Level besteht eine Chance von 4 bis 30 %, dass ein Satz bewusst sperrig ausfällt — aber nie, wenn das Brett schon zu über 50 % voll ist.

## Level & Akzent

Alle 12.000 Punkte steigt das Level, der Akzentton wechselt (8 Töne im Wechsel) und Spezialblöcke werden häufiger. Der Hintergrund bleibt bewusst ruhig.

## Daten

Highscore, Statistiken, Einstellungen und der laufende Spielstand liegen im `localStorage` unter dem Präfix `blockstorm.v1.`. Ist `localStorage` blockiert, läuft das Spiel trotzdem — nur ohne Speichern. Zurücksetzen über *Optionen → Statistiken löschen*.

## Anpassen

Alles Wichtige steht oben in `index.html`:

- `COLORS` — Blockfarben als Lichtfase / Frontfläche / Schattenfase
- `ACCENTS` — Akzenttöne pro Level
- `SHAPE_SRC` — Teileformen als Textmuster plus Gewichtung (höher = häufiger)
- `SPECIALS` — Verteilung der Spezialblöcke
- `paintBlock()` — die Klotz-Optik: `r` steuert die Eckenrundung, `b` die Fasenbreite
- `refillTray()` / `setScore()` — wie streng der Generator aussiebt
