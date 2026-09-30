# pi-build123d

Code-first parametrische CAD-Konstruktion für den [pi coding agent](https://pi.dev) mit [build123d](https://github.com/gumyr/build123d).

Dieser Skill versetzt pi in die Lage, mechanische Bauteile und Baugruppen reproduzierbar in Python zu modellieren, als STEP/STL zu exportieren und geometrisch zu validieren.

---

## Voraussetzungen

Ein Python-Interpreter mit installiertem `build123d`:

```bash
# Entweder direkt via uv (empfohlen):
uv pip install build123d

# Oder klassisch per pip:
pip install build123d
```

*(Für das optionale PDF-Vektor-Reverse-Engineering wird zusätzlich `pymupdf` empfohlen).*

---

## Installieren

```bash
pi install https://github.com/kambodscharoger294-cyber/pi-build123d
```

Nach der Installation steht pi der Skill `build123d` zur Verfügung.

---

## Was der Skill mitbringt

1. **Parametrische Konstruktionsregeln:** Saubere Trennung von Bemaßungskonstanten am Dateikopf und Geometrieerzeugung über standardisierte `Locations`-Raster (keine Magic Numbers).
2. **Standard-Exporte & Metriken:** Jedes Skript erzeugt bei Aufruf automatisch STEP- (CAD/CAM) und STL-Dateien (3D-Druck) und prüft Bounding Box und Volumen.
3. **Inkludierte Beispiel-Erfahrungen (Optional / Fallweise):**
   - Vektor-Reverse-Engineering aus technischen PDF-Zeichnungen (Kreis-Fit via `pymupdf`).
   - Plausibilitäts- und Massenabgleich gegen Zeichnungsangaben (`APPROX WEIGHT`).
   - Saubere Dokumentation von Annahmen bei fehlenden Bemaßungen.
   - Umgang mit widersprüchlichen „FOR INFORMATION ONLY“-Zeichnungen.

---

## Lizenz

MIT — siehe [LICENSE](LICENSE).
