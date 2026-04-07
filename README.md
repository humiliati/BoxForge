# BoxForge v1.0

**CSS 3D Box Geometry Editor** — a zero-dependency, single-file visual tool for designing animated 3D box compositions using pure CSS transforms.

BoxForge was built for [Dungeon Gleaner](https://github.com/) (DC Game Jam 2026) to design the peek-window UI elements that appear when the player interacts with doors, chests, torches, and other dungeon objects. It generates production CSS that renders entirely in the browser with no canvas, no WebGL, and no JavaScript at runtime.

## What it does

BoxForge lets you build a 3D box out of six CSS-transformed faces (back, left, right, top, bottom, lid), then compose additional elements on top of it — glowing light sources, animated orbs, rotating pyramids — all driven by a three-phase animation system (Idle / Hover / Activated).

The editor runs at 60fps in the browser. Everything you see in the viewport is live CSS, not a simulation.

### Core features

- **Box shell** — width, height, half-depth sliders with perspective and rotation camera controls
- **Six-face pane system** — each face has its own color, texture (gradient, stone, iron-plate, grid-lines, hidden), hinge side, open angle, and hover angle
- **Lid mechanics** — any face can become a hinged lid that swings open on hover with configurable angle interpolation
- **Glow sources** — up to 4 radial gradient light sources, each independently positioned and rotated in 3D space
- **Orb composer** — pure-CSS sphere built from ring/slice geometry, with per-phase palette, size, speed, and state (unlit, ember, lit)
- **Pyramid composer** — three-face CSS pyramid with per-phase color triads and glow halos
- **Three-phase animation** — every property can vary across Phase 1 (Idle), Phase 2 (Hover), and Phase 3 (Activated), with per-phase toggle buttons in the top bar
- **Compose bar** — toggle orb and pyramid visibility per phase; orb-only and pyramid-primary modes dim the box to feature the composed element
- **Export CSS** — one-click export of the complete CSS for the current phase, ready to paste into a stylesheet
- **Resource monitor** — live FPS counter, scene DOM node count, and pane tally

### Project persistence

- **Save JSON** — serialize the full editor state (shell, colors, panes, glows, orb, pyramid, phase animations, mode flags) to a `.boxforge.json` file
- **Load JSON** — restore any saved project from file
- **Save as Template** — snapshot the current editor state as a named template stored in localStorage
- **Delete user templates** — hover the x button on any user-created template to remove it

### Shipped templates

BoxForge ships with 6 built-in templates that demonstrate the range of compositions:

| Template | Description |
|----------|-------------|
| **Splash Cube** | Default 200x200 box with pink accent lid — the starter template |
| **Single Door** | Stone door frame with a single hinged door panel |
| **Double Doors** | Stone frame with left and right swinging doors |
| **Orb** | Standalone animated sphere (orb-only mode) with smoke/ember/fire phases |
| **Pyramid** | Standalone rotating pyramid (pyramid-primary mode) with tri-color glow |
| **Torch Peek** | Full composition — wall-mounted torch bracket with orb flame + pyramid ember + glow sources |

## Quick start

Open `boxforge.html` in any modern browser. No server, no install, no build step.

```
your-browser boxforge.html
```

That's it. The entire application is a single HTML file (~3,800 lines) with inline CSS and JavaScript.

## File manifest

```
tools/
  boxforge.html          Main editor (canonical copy)
  peek-workbench.html    Development copy (identical to boxforge.html)
  orb-component.html     Standalone orb playground — isolated ring/slice sphere editor
  README.md              This file
  LICENSE                MIT license
```

`peek-workbench.html` is the development-time filename used during the jam. `boxforge.html` is the release name. They are kept in sync.

`orb-component.html` is a standalone tool for experimenting with the CSS orb (sphere) geometry in isolation, outside the full BoxForge editor. It has its own ring count, slice count, palette, and animation controls.

## Architecture

BoxForge is a single IIFE (Immediately Invoked Function Expression) with no external dependencies. The code is organized into these sections:

1. **CSS** (~260 lines) — all styles are in a single `<style>` block
2. **HTML** (~460 lines) — sidebar controls, viewport, compose bar, export area
3. **JavaScript** (~3,050 lines) — the IIFE containing:
   - Factory functions (`makePane`, `makeGlow`, `makeOrbPhase`, `makePyrPhase`)
   - State management (shell, colors, panes, glows, orbConfig, pyramidConfig, phaseAnims)
   - Template registry (built-in snapshots + localStorage user templates)
   - DOM rendering (pane list, glow list, orb/pyramid sections, compose bar)
   - 3D viewport rendering (CSS transform composition, label overlays)
   - Serialization (`serializeProject` / `loadProject`) for JSON save/load
   - Export (CSS generation from current state)
   - Init sequence (snapshot built-ins, load first template, start render loop)

### The `.boxforge.json` format

```json
{
  "_format": "boxforge",
  "_version": 1,
  "shell":         { "w": 200, "h": 200, "d": 100, "perspective": 800, "rotX": -25, "rotY": 20 },
  "colors":        { "cDark": "...", "cLight": "...", "cFloor": "...", "cCeil": "...", "cGlow": "..." },
  "panes":         [ { "id": 1, "name": "Back", "face": "back", ... } ],
  "glows":         [ { "id": 1, "name": "Floor glow", ... } ],
  "orbConfig":     { "p1": { ... }, "p2": { ... }, "p3": { ... } },
  "pyramidConfig": { "p1": { ... }, "p2": { ... }, "p3": { ... } },
  "phaseAnims":    { "p1": { ... }, "p2": { ... }, "p3": { ... } },
  "phaseMode":     "box",
  "orbOnly":       false,
  "pyrPrimary":    false,
  "currentState":  "p1"
}
```

Files are forward-compatible: `loadProject` fills missing fields from factory defaults, so older saves work with newer editor versions.

### Public API

BoxForge exposes a minimal API on `window.BoxForge` for scripting and testing:

```js
window.BoxForge.serialize()   // → full project state object
window.BoxForge.load(data)    // ← restore from project state object
```

## Browser support

Tested in Chromium-based browsers (Chrome, Brave, Edge). Requires CSS 3D transforms, CSS custom properties, and `preserve-3d`. No polyfills needed for any modern browser.

## Target platform

BoxForge was built to produce CSS for **Dungeon Gleaner**, which targets the LG Content Store as a webOS TV app driven by the Magic Remote. The generated CSS is designed to render cleanly on the webOS Chromium runtime (Chrome 87+).

## License

MIT — see [LICENSE](LICENSE).
