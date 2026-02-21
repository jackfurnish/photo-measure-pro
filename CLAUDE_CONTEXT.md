# Photo Measure Pro — Kontekst projektu dla Claude

## Podstawowe info
- **Autor / właściciel:** JackFurnish (© 2026, All Rights Reserved)
- **GitHub:** https://github.com/jackfurnish/photo-measure-pro
- **Live demo:** https://jackfurnish.github.io/photo-measure-pro/
- **Plik główny:** `index.html` (~4520 linii, single-file app)
- **Uruchamianie:** otwórz `index.html` bezpośrednio w przeglądarce (`file://`)
- **Zależności:** ZERO — brak CDN, brak npm, brak frameworków

## Co robi aplikacja
Perspektywicznie-korygowane narzędzie do pomiarów wnętrz.
Użytkownik wrzuca zdjęcie pomieszczenia, zaznacza 4 narożniki mierzonej płaszczyzny,
podaje rzeczywiste wymiary krawędzi — aplikacja oblicza homografię (DLT) i pozwala
mierzyć odległości w mm z perspektywicznie wyprostowanego widoku.

## Stack techniczny
- **Vanilla JS** — brak modułów ES (`type="module"` nie działa z `file://`)
- **Funkcje globalne** na `window.*` (wymagane przez `onclick=""` w HTML)
- **Canvas 2D API** — dwa nakładające się canvasy: `#img-canvas` (zdjęcie) + `#main-canvas` (overlay)
- **CSS Variables** — design system "Quiet Luxury 2026" (Plus Jakarta Sans, accent #B8860B)
- **Web Speech API** — komentarze głosowe (`lang:'pl-PL'`)
- **FileReader API** — dwa odczyty: ArrayBuffer (EXIF) + DataURL (canvas)

## Architektura — kluczowe funkcje

### Homografia
- `computeHomography(src, dst)` — DLT 8×8, eliminacja Gaussa
- `rectifyImage()` — prostuje zdjęcie na `state.rectCanvas`
- `onCanvasClick()` — klik → mm przez `Hinv`

### Tryby (mode-bar zakładki)
1. **Narożniki** — zaznaczanie 4 rogów płaszczyzny (z lupą 4×)
2. **Rektyfikacja** — podgląd wyprostowanego widoku
3. **Punkty** — pomiary punktowe (drag, snap, voice comment)
4. **Odcinki** — dwa kliki = odcinek z etykietą
5. **Powierzchnia** — wielokąt ze shoelace formula, area m²

### State object (główny stan)
```javascript
const state = {
  mode: 'corners',           // 'corners'|'rectified'|'measure'|'segments'|'polygon'
  imageSrc, imageEl, imageNaturalW, imageNaturalH,
  exif: null,                // {make,model,focal,aperture,shutter,iso,date,gps,...}
  corners: [null×4],         // TL,TR,BR,BL w px na zdjęciu
  edges: {top,bot,left,right}, // mm — 4 niezależne krawędzie (tolerancje budowlane)
  planeMM: {w, h},           // średnie z edges
  H, Hinv, pxPerMM,          // macierze homografii
  rectWidth, rectCanvas,
  points: [],                // {id,x_mm,y_mm,u_px,v_px,comment}
  nextId,
  segments: [],              // {id,a:{x_mm,y_mm},b:{x_mm,y_mm},dist_mm,comment}
  nextSegId, segmentDraft,
  polygons: [],              // {id,pts:[{x_mm,y_mm}],area_mm2,perimeter_mm,comment}
  nextPolyId, polyDraft,
  _dragPoint,                // {id} — przeciągany punkt
  _snapTarget,               // {x_mm,y_mm} — cel snapa (zielony pierścień)
  _cursorPos,                // {cx,cy} — crosshair
  undoStack, redoStack,
  projectName, dirty, cornerSetIndex
};
```

### Undo/Redo
- `pushUndoState()` — klonuje stan przez `cloneState()`
- `undo()` / `redo()` — przywracają przez `applySnapshot()`

### Eksport / Zapis
- **CSV** — punkty
- **JSON** — punkty
- **DXF** — AutoCAD R12 ASCII, 6 warstw: BORDER, POINTS, SEGMENTS, DIMS, POLYGONS, COMMENTS
- **PNG** — annotated canvas z EXIF w nagłówku
- **Raport A4** — HTML print window z tabelami + EXIF
- **`.pmproj`** — JSON format (save/load projektu)

### EXIF Reader
- `parseExif(arrayBuffer)` — czyste JS, brak bibliotek
- `parseIFD()` — IFD0 + SubIFD (ExifIFD 0x8769, GPS 0x8825), LE/BE
- Pokazany w: sidebar, raport A4, PNG header, DXF $COMMENTS

### Wzorce pomieszczeń (Template Wizard)
- Przycisk "📐 Wzorzec…" w headerze
- 4-krokowy kreator: typ → nazwa → wymiary → otwory (drzwi/okna)
- 6 wzorców: Łazienka, Kuchnia, Salon, Sypialnia, Korytarz, Inne
- Otwory → automatyczne odcinki pomiarowe z etykietą 🚪/🪟
- Wypełnia edge-top/bot/left/right automatycznie

### Inne funkcje
- **Snap** — `findSnapTarget()`, 15mm próg, zielony pierścień `#snap-ring`
- **Drag points** — mousedown/mousemove/mouseup, recalc Hinv
- **Magnifier** — 4× lupa podczas zaznaczania narożników (MAG_SIZE=180)
- **Crosshair** — checkbox, cienkie linie przez kursor
- **Zoom/Pan** — scroll + Space+drag, CSS transform na `#canvas-wrapper`
- **Siatka** — co 100mm, Y-up (origin lewy dolny narożnik)

## Modals (lista)
- `#voice-modal` — komentarze głosowe (reużywany dla punktów/odcinków/wielokątów)
- `#report-modal` — raport A4
- `#tmpl-modal` — kreator wzorców pomieszczeń

## Commit history (ostatnie)
- `dcbdf2f` — room template wizard
- `5b0d840` — EXIF reader + polygon/area + drag + snap + DXF EXIF header
- `c27da82` — wcześniejsze funkcje (DXF, crosshair, magnifier, print PNG)

## Jak kontynuować pracę
1. Otwórz plik: `/Users/leszekborowski/Library/Mobile Documents/com~apple~CloudDocs/CLAUDE_PROGRAMOWANIE/PHOTO MEASURE PRO/index.html`
2. Cały kod jest w jednym pliku — HTML + CSS + JS
3. Po zmianach: `git add index.html && git commit && git push origin master`
4. GitHub Pages aktualizuje się automatycznie (może potrwać 1–2 min)

## Styl kodu
- Komentarze po polsku (sekcje) + angielskie nazwy zmiennych/funkcji
- Sekcje oddzielone: `/* ═══════════════ NAZWA ═══════════════ */`
- Toast: `toast('wiadomość', 'success'|'warn'|'error')`
- Dirty flag: `state.dirty = true` po każdej zmianie danych
- `pushUndoState()` przed każdą destruktywną operacją

## Właściciel
Tylko **JackFurnish** może używać, kopiować, modyfikować i dystrybuować ten kod.
Licencja: All Rights Reserved © 2026 JackFurnish
