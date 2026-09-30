---
name: build123d
description: Code-first parametrische CAD-Konstruktion mit build123d in Python. Erzeugt saubere parametrisierte Modelle, STEP/STL-Exporte, Bounding-Boxen und Massenberechnungen.
---

# build123d — Code-First CAD

Parametrische 3D-CAD-Konstruktion mit Python und **build123d**. Erzeugt reproduzierbare Bauteile, Baugruppen und technische Exporte (STEP, STL).

---

## Kern-Konventionen

1. **Parametrisch statt Magic Numbers:**
   - Alle wesentlichen Maße (Längen, Durchmesser, Wandstärken, Bohrungsabstände) stehen als benannte Konstanten am Kopf des Skripts.
   - Bohrbilder und Raster werden über `Locations`, `PolarLocations` oder `GridLocations` erzeugt, nicht durch wiederholtes manuelles Platzieren.

2. **Ausführbares Einzelteil-Skript:**
   - Jedes Teil-Skript (`part_*.py`) ist eigenständig lauffähig.
   - Beim direkten Aufruf (`if __name__ == "__main__":`) erzeugt es die Exporte (`export_step`, `export_stl`) und gibt Geometrie-Metriken aus:
     - Volumen (`part.volume` in mm³)
     - Bounding Box (`part.bounding_box().size`)

3. **Geometrie-Fast-Check:**
   - Vor Abschluss der Aufgabe immer sicherstellen, dass das Skript fehlerfrei kompiliert (`python -m py_compile <script.py>`) und bei Ausführung ein positives Volumen ohne Fehler liefert.

---

## Ausführungsumgebung

Zum Ausführen von build123d-Skripten:
- Falls eine Umgebungsvariable `BUILD123D_PYTHON` gesetzt ist oder ein lokales venv existiert, dieses nutzen.
- Ansonsten via `uv run`:
  ```bash
  uv run --with build123d python <script.py>
  ```

---

## Beispiel-Aufbau eines Bauteils

```python
"""Parametrisches Bauteil mit build123d."""
from build123d import *

# --- Parameter (in mm) ---
LENGTH = 100.0
WIDTH = 50.0
THICKNESS = 8.0
HOLE_DIAMETER = 5.2
HOLE_SPACING_X = 80.0
HOLE_SPACING_Y = 30.0

# --- Geometrie ---
with BuildPart() as part:
    Box(LENGTH, WIDTH, THICKNESS)
    # 4er-Bohrbild symmetrisch verteilen
    with GridLocations(HOLE_SPACING_X, HOLE_SPACING_Y, 2, 2):
        Hole(radius=HOLE_DIAMETER / 2)

# --- Validierung & Export ---
if __name__ == "__main__":
    p = part.part
    bb = p.bounding_box()
    print(f"Volumen: {p.volume:.1f} mm³")
    print(f"Bounding Box: {bb.size.X:.1f} x {bb.size.Y:.1f} x {bb.size.Z:.1f} mm")
    
    export_step(p, "part.step")
    export_stl(p, "part.stl")
    print("STEP und STL erfolgreich exportiert.")
```

---

## Inkludierte Beispiel-Erfahrungen (Optional / Fallweise)

> **Hinweis:** Die folgenden Praxishinweise sind optionale Erfahrungswerte aus realen Reverse-Engineering- und Konstruktionsaufträgen. Sie sind nicht für jedes einfache Standardteil zwingend, bieten aber bewährte Heuristiken bei komplexen Aufgabenstellungen, Zeichnungsübernahmen oder unvollständigen Vorgaben.

### 1. Reverse-Engineering aus PDF-Zeichnungen (Optional)
- **Vektordaten statt Bild-OCR:** Bemaßte PDF-Zeichnungen nicht nur als gerendertes Rasterbild betrachten. Mit `pymupdf` (fitz) können die exakten Vektorpfade extrahiert werden.
- **Kreisfit:** Bohrungs- und Radienmittelpunkte lassen sich über Endpoint-Clustering und RANSAC-Kreisfit auf ±0,2 mm genau bestimmen.
- **Kalibrierstrecke:** Den Skalierungsfaktor über eine bekannte Referenzstrecke (z. B. 50 mm Bemaßungslinie ↔ Vektordistanz) eichen. Textlayer für Feature-Callouts nutzen.

### 2. Plausibilitäts-Checks bei Massevorgaben (Optional)
- Gibt eine Zeichnung ein Richtgewicht an („APPROX WEIGHT“): Berechnetes Volumen mit der typischen Materialdichte gegenrechnen (z. B. Aluminium ~2,7 g/cm³, Stahl ~7,85 g/cm³), um Skalierungs- oder Modellierungsfehler frühzeitig aufzudecken.
- Bei mehrteiligen Baugruppen: Symmetrien und Einbaulagen prüfen (z. B. 180°-Drehung oder Spiegelsymmetrie bei Montagepartnern).

### 3. Annahmen-Register bei unvollständigen Maßen (Optional)
- Unbemaßte Geometriedetails (Fasen, Gewindetiefen, Taschen, Kugelsitze) am Dateianfang als benannte Parameter mit Schätzwerten deklarieren.
- Im Bericht oder Dateikopf kurz als Annahme kennzeichnen – das macht spätere Anpassungen mit einer einzigen Zahlenänderung möglich.

### 4. Widersprüche in Herstellerzeichnungen (Optional)
- In Zeichnungen mit dem Vermerk „FOR INFORMATION ONLY“ können Maße funktional inkonsistent sein (z. B. Kugelüberstand vs. Stapelhöhe).
- Solche Widersprüche dem Nutzer direkt melden, statt stillschweigend eine mutmaßliche „Korrektur“ einzubauen.
