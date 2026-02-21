# 📐 Photo Measure Pro

**Perspective-corrected interior measurement tool**
*Single HTML file — no installation, no dependencies, works offline*

© 2026 **JackFurnish** · All Rights Reserved

---

## 🌐 Live Demo

**[jackfurnish.github.io/photo-measure-pro](https://jackfurnish.github.io/photo-measure-pro/)**

---

## Features

- **Homography (DLT)** — perspective correction from 4 corner points
- **Bilinear rectification** — straightened floor/wall plane view
- **Non-rectangular rooms** — 4 independent edge lengths (construction tolerances)
- **Y-up coordinate system** — origin at bottom-left corner (construction convention)
- **Measurement points** — click to place, voice comments (Web Speech API)
- **Segment mode** — click 2 points → precise distance with label
- **Zoom & pan** — scroll wheel + Space+drag
- **Crosshair overlay** — precision cursor guide
- **A4 report** — printable table + annotated photo PNG
- **DXF export** — AutoCAD R12, SketchUp-compatible (5 layers: BORDER, POINTS, SEGMENTS, DIMS, COMMENTS)
- **Project save/load** — `.pmproj` JSON format

---

## Usage

1. Open `index.html` in any modern browser (Chrome recommended)
2. Drop a photo of your room/wall
3. Click the 4 corners of the measured plane (TL → TR → BR → BL)
4. Enter the real dimensions (mm) of each edge
5. Click **Rektyfikacja** → **Punkty** or **Odcinki** to measure
6. Export as DXF, CSV, JSON or print annotated photo

---

## License

**All Rights Reserved © 2026 JackFurnish**

This software is proprietary. No part of this code may be used, copied,
modified, distributed or sublicensed by anyone other than the author.

See [LICENSE](./LICENSE) for full terms.
