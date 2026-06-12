# chain-reaction — Chain Reaction Digital Model

Standalone single-file sim deployed to `ghostoutfit.github.io/chain-reaction/`.
Derived from the canonical `v13/index.html` in the `protons` repo — **chain reaction tab only**, fission tab fully removed.
Vanilla HTML + CSS + JS only. No build step — edit `index.html` directly.

To run locally: `python3 -m http.server` in this directory, then open `localhost:8000/`.

## Differences from canonical v13

- Fission tab and all its code removed (see below)
- `currentTab` is a `const` `'chain'` — never changes
- `applyTab('chain')` called once at init
- Zoom fixed at -0.6 (`viewZoom = zoomFromSlider(-0.6)`); zoom slider hidden in DOM but kept for JS compatibility
- `renderAtFrac(frac)` uses `frac < NEUTRON_FRAC` branching (not `neutronState`) for nucleus+neutron approach phase
- Energy bar chart (`drawChainBarChart`) always visible at right edge; canvas-drawn, not a separate panel
- Image paths use `images/` (no `../images/` parent prefix)

## What was removed (fission tab code)

- Replay button, playback speed slider, scrub bar
- `drawForceViz`, `drawEnergyGraph`
- `doReplay`, nucleus drag/rotation
- `computeEnergy`, prerender system
- `captureFrame()` / `markPrerenderDone()` are no-op stubs (kept to avoid errors from shared call sites)

## What this repo has that fission does not

- `chainNeutrons[]` — neutrons in flight across the chain grid
- `enrichSites[]` — enriched nucleus sites; each has `{ cx, cy, state, fissT, angle, wobbleMode }`
- `chainParticleOffsets` — lazy cache of 3D-rotated parent particle offsets for per-site rendering
- `drawChainBarChart()` — canvas-drawn energy bar chart pinned to right edge (`CHAIN_BAR_PANEL_W = 240`); bars minimum 4px tall; left border glow strengthened for visibility
- `triggerEnrichSite()`, `emitChainNeutrons()`, `pickChainSite()`
- `CHAIN_NORMAL_DURATION = 5000` ms per site fission

## Phase machine

`renderAtFrac(frac)` reconstructs chain geometry analytically:

```
frac < NEUTRON_FRAC (0.20)  →  nucleus + incoming neutron approach phase
frac ≥ NEUTRON_FRAC         →  fission + daughter ejection + chain propagation
```

No frame buffer — scrubbing is phase-indexed via `currentScrubFrac`.

## Energy bar chart

`CHAIN_BAR_PANEL_W = 240` — right-side panel width in pixels.
`CHAIN_ENERGY_MAX  = 100` — fixed chart scale.
Bars: minimum 4px tall. Left border glow present for visibility.

## Physics constants

```javascript
const PARTICLE_RADIUS = 10;
const coulombStrength = 150;
const NEUTRON_FRAC    = 0.2;
const CHAIN_NORMAL_DURATION = 5000;  // ms
```

## Images

```
images/
  favicon.png / favicon.svg
  Rabbit.png / logo-placeholder.png
```

(No Turtle.png, Stopwatch.png — speed slider and scrub bar are removed.)

## Deployment

GitHub Pages from `main` branch root. Push to `origin` to deploy.
Remote: `https://github.com/ghostoutfit/chain-reaction.git`

## Cross-sim search

```bash
# Compare with canonical v13
grep -n "functionName" index.html /path/to/protons/v13/index.html
```

Changes to canonical `v13/index.html` in the protons repo should generally be ported here (chain-reaction-relevant parts only). They are not auto-synced.
