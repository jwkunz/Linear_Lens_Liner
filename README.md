# Linear Lens Liner

A single-file, browser-based tool for designing planar (2D side-view) optical
systems with paraxial ray tracing, and for running a higher-fidelity Fourier
optics (beam propagation) calculation on top of a finished design.

No build system, no server, no dependencies. Open `Linear_Lens_Liner.html` in a
modern browser (Chrome, Firefox, or Edge) and it runs.

## Features

- **Interactive layout** of Sources, Lenses, and Sinks on a 2D optical-axis
  canvas, with pan/zoom, a configurable snap grid, and draggable/resizable
  panels.
- **Paraxial ray-trace** (ABCD matrix optics) with configurable ray count per
  source, aperture clipping, and a live results panel (spot diameter,
  centroid, magnification, angular magnification, per-ray table, clipping
  report).
- **Physical lens geometry** for 7 lens types (Thin, Plano-Convex/Concave,
  Bi-Convex/Concave, Positive/Negative Meniscus), derived from focal length
  and index of refraction via the thin-lens/lensmaker's equation.
- **Geometry Report** and **Generate in FreeCAD** exports per lens: a
  tabulated physical-dimensions report, and a Python macro that builds the
  actual solid body in FreeCAD.
- **Fourier optics simulation**: a 1D angular-spectrum split-step beam
  propagation (BPM) calculation through the real, meshed refractive-index
  profile of your lenses (not just idealized thin-lens phase screens),
  complete with progress bar, cancellation, and `results.json` export.
- **Global media index**, used consistently by both the ray-trace's lens
  matrices and the Fourier calculation.
- Save/load designs as JSON.

## Getting Started

1. Download `Linear_Lens_Liner.html`.
2. Open it in your browser. That's it — everything runs client-side.

## Help

### The basics

- **Add a component**: click *Add Source*, *Add Lens*, or *Add Sink* in the
  toolbar, then click on the canvas to place it.
- **Select / move**: switch to *Select* mode, click a component to select it,
  or drag it to reposition (snapped to the current grid size).
- **Edit properties**: double-click a component to open its property panel.
  Numeric fields are "roller" controls — drag left/right, scroll the mouse
  wheel over them, use the +/- buttons, or click once to type a value
  directly. A shared step size (bottom of the panel) controls the roller
  increment; a "Recompute on change" checkbox controls whether edits
  re-run the ray-trace live or only on Save.
- **Keyboard shortcuts**: `Delete` removes the selected component, arrow keys
  nudge it by one snap step, `Ctrl+D` duplicates it, `Ctrl+S` downloads the
  design as JSON, `Ctrl+O` loads a JSON design.
- **Canvas navigation**: scroll to zoom, middle-mouse-drag to pan, *Fit View*
  to frame the whole design.

### Ray tracing ("Compute Rays")

Click **Compute Rays** to trace every source's rays (evenly spaced across its
beam width, up to 99 per source) through all lenses to every sink downstream.
Results — spot diameter, centroid, magnification, angular magnification, a
per-ray table, and a clipping report — appear in the side panel. Rays that
miss a component's clear aperture are clipped and shown up to the clip point
only. The *Ray Colors* toolbar option switches between grayscale shading and
a ROYGBV spectrum across the ray bundle.

### Lens geometry tools

Every lens's property panel includes:

- **Index of refraction** field, with a small calculator that computes
  `n = √εr` from a relative permittivity you enter.
- **Geometry Report**: downloads a `.txt` file with the lens's derived
  surface radii, sag, and edge thickness for its specific lens type.
- **Generate in FreeCAD**: downloads a `.FCMacro` Python macro. Open it in
  FreeCAD (or paste it into the Python console) to build the lens as an
  actual solid body (a cylindrical blank intersected/cut with spheres at the
  correct front/back surface positions).

Note: idealized "Thin" lenses have no physical surfaces to report or model,
and meniscus lens shapes require an assumed radius ratio (documented in the
output) since focal length and index alone don't uniquely fix two radii.

### Fourier optics ("Compute Fourier")

Click **Compute Fourier** to open the wave-optics configuration panel:

- **Source / Sink** — pick which pair of components to propagate between.
- **Source function** — Gaussian, boxcar, or triangular amplitude profile
  across the source's beam width. The source's pointing angle and divergence
  are converted into an exact (not small-angle-approximated) tilt and
  wavefront curvature.
- **Wavelength**, **FFT size** (transverse grid points, power of 2),
  **Box clipping height** (transverse window size — the *Auto* button sets it
  to 2× the tallest component in the design), and **Propagation step size**
  (in micrometers — this needs to be meaningfully smaller than a lens's
  surface sag to resolve its curvature accurately).
- **Media index** is set globally (see below), not per-calculation.

Hit **Calculate** to run. Lenses with real physical geometry are meshed
directly into the propagation grid (each pixel at each step is assigned
either the lens's index or the surrounding media index); idealized "Thin"
lenses fall back to the same phase-screen approximation the ray-trace uses.
A progress bar shows step count and an estimated time remaining; **Cancel**
stops a run in progress. When finished, **Download results.json** exports
the complex field (real/imaginary, amplitude, unwrapped phase) at both the
source and sink planes, and four plots render inline (source/sink ×
amplitude/unwrapped phase).

### Global media index

The **Media Index** control in the toolbar (next to the snap-size selector)
is used everywhere: the ray-trace's lens matrices adjust each lens's
effective focal length for the surrounding medium (e.g. a lens in water is
weaker than the same lens in air), and the Fourier calculation uses it as
the background index for propagation.

### Saving and loading

**Save** downloads the current design (components, snap size, zoom) as a
JSON file. **Load** reads one back in. The format is stable and
human-readable if you want to script designs directly.

## License

Distributed under the MIT license by numerius.engineering@gmail.com

```
MIT License

Copyright (c) 2026 numerius.engineering@gmail.com

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
