# OBSIDIAN — liquid-metal automotive site

React · Three.js · React Three Fiber · Drei · GSAP (ScrollTrigger) · Framer Motion · Lenis · Vite

## Run locally
```bash
npm install
npm run dev        # http://localhost:5173
npm run build      # production build -> dist/
npm run preview    # serve the build
```
Node 18+.

## Deploy
Static site, no server needed.
- **Vercel / Netlify / Cloudflare Pages**: import the repo; build `npm run build`, output `dist`.
- **Anywhere else**: upload the contents of `dist/`.

## How the 3D works
`src/shaders/liquid.frag.glsl` is a raymarched signed-distance-field scene rendered on one full-screen
triangle-quad inside R3F (`src/components/HeroScene.jsx`). There are **no models or textures** to load or break.

A single scalar `uMorph` (0 → 1) blends between six procedural shapes:
droplet/puddle → rising sculpture → spikes → vintage racer front (grille) → sports car → luxury SUV.
Shapes are blended by interpolating their distance fields, so the change is one continuous deformation.
Material = black chrome: Fresnel reflection of a procedural studio (softboxes, white cyclorama), soft shadows,
contact AO and a faint floor reflection.

`uMorph` = `max(timed autoplay, scroll progress)` (`src/lib/store.js`). It plays on load over
`HERO_AUTOPLAY_SECONDS`; scrolling the pinned hero scrubs ahead. "Replay ↺" restarts it.

### Performance & accessibility
- Three/R3F are code-split and lazy-loaded; the page shell paints first.
- Drei `PerformanceMonitor` lowers pixel ratio and raymarch steps automatically on slow GPUs; mobile starts lower.
- Rendering pauses when the hero is off-screen.
- No WebGL (or context lost) → falls back to cross-faded pre-rendered frames (`Fallback.jsx`).
- `prefers-reduced-motion`: no smooth scroll, no autoplay (final SUV frame shown), no cursor effects.

## Editing
- Copy, nav, stage labels, e-mail: `src/config.js` (**set your real e-mail there**).
- Look & feel: CSS variables at the top of `src/styles/global.css`.
- Shapes: edit the `s*()` functions in the shader. Re-render stills after changing it:
  `npm i -D playwright sharp && npx playwright install chromium && npm run stills`
  (outputs `public/images/*.webp`: collection images + fallback frames).

## Structure
```
src/
  App.jsx  main.jsx  config.js
  shaders/liquid.frag.glsl
  lib/        store.js  gsap.js  scroll.js  webgl.js
  hooks/      useMedia.js  useSmoothScroll.js
  components/ Loader Nav Cursor Hero HeroScene Fallback Collection Experience About Contact Footer Button Words Fade
  styles/global.css
scripts/render-stills.mjs
public/images/
```
