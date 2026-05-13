# CLAUDE.md — AR Apartment Viewer

Project notes for future Claude sessions working on this codebase.

---

## Project Overview

A pure static-file WebAR system for real estate apartment showcasing.
- Customers scan a QR code or tap a WhatsApp link
- Browser opens (no app download)
- WebXR Hit Test API detects any flat surface
- 3D apartment model (built from A-Frame primitives) is placed on that surface
- Deployed on GitHub Pages (free HTTPS hosting)

**No markers. No printouts. No backend. No build tools.**

---

## File Purposes

| File | Role |
|---|---|
| `index.html` | Main WebAR viewer. Contains A-Frame scene + custom `ar-hit-test` component + all UI overlays. |
| `qr.html` | QR code generator page. Uses QRious.js. User pastes their GitHub Pages URL, downloads QR as PNG. |
| `marker.html` | Customer instructions page. Explains how to use the AR viewer. No longer about a physical marker. |
| `README.md` | Step-by-step deployment and usage guide for the developer. |
| `CLAUDE.md` | This file. Context for future Claude sessions. |

---

## Tech Stack & Pinned CDN Versions

```
A-Frame       1.5.0   https://cdn.jsdelivr.net/npm/aframe@1.5.0/dist/aframe.min.js
QRious        4.0.2   https://cdn.jsdelivr.net/npm/qrious@4.0.2/dist/qrious.min.js
```

**AR.js is NOT used.** Surface AR is implemented via the native **WebXR Hit Test API**, which is built into A-Frame 1.5.0's WebXR support (Three.js r158 underneath).

---

## AR Architecture

### How surface detection works

1. `<a-scene webxr="requiredFeatures: hit-test, local-floor">` opens a WebXR immersive-ar session.
2. The custom `ar-hit-test` A-Frame component (registered in `index.html` before `<a-scene>`) requests a hit-test source against the `viewer` reference space.
3. Each frame, `frame.getHitTestResults(hitTestSource)` returns intersection points with real-world geometry detected by ARCore (Android) or ARKit (iOS).
4. The gold reticle entity is repositioned to the closest hit point.
5. On the WebXR `select` event (screen tap), the apartment entity is moved to the last hit position and made visible.

### Key A-Frame scene attributes

```html
<a-scene
  webxr="requiredFeatures: hit-test, local-floor; optionalFeatures: dom-overlay, unbounded; domOverlay: #loading-overlay"
  vr-mode-ui="enabled: false"
  renderer="antialias: true; alpha: true; logarithmicDepthBuffer: true;"
  loading-screen="enabled: false"
  ar-hit-test
>
```

### Component registration

The `ar-hit-test` component **must be registered before `<a-scene>` is parsed** by the browser. It lives in a `<script>` block immediately before `<a-scene>` in `index.html`.

### scene.frame

Inside the component's `tick()`, `this.el.sceneEl.frame` holds the current `XRFrame`. A-Frame 1.x assigns this in its internal render loop before calling tick.

---

## 3D Apartment Model

Built entirely with A-Frame primitives (`<a-box>`, `<a-text>`, `<a-ring>`, `<a-circle>`).
No `.glb`, `.obj`, or external asset files.

### Coordinate system

- Y-axis is UP
- Model origin is at floor centre (0, 0, 0)
- All room geometry sits at or just above Y=0
- Walls extend from Y=0 to Y≈0.5

### Scale in the real world

The `#apartment-root` entity has `scale="0.18 0.18 0.18"`.
In WebXR, 1 A-Frame unit = 1 metre.
The floor is 3 × 2.5 units, so in real life:
- 3 × 0.18 = **0.54 m wide**
- 2.5 × 0.18 = **0.45 m deep**

This appears as an architectural miniature model on a table — intentional.

### Room layout (top-down view)

```
+---------------------------+
|  Living Room (orange)     |  Bedroom (blue)   |
|                           +-------------------+
|                           |  Kitchen (green)  |
+----------------+----------+-------------------+
|   Bathroom (purple)       |
+---------------------------+
         [entrance gap, south wall]
```

### Room positions (A-Frame units, relative to model origin)

| Room | Position | Color |
|---|---|---|
| Floor base | 0, 0, 0 | #8a8a8a |
| Living Room tile | -0.65, 0.03, 0.4 | #FFE4B5 |
| Bedroom tile | 0.85, 0.03, 0.55 | #B0C4DE |
| Kitchen tile | 0.85, 0.03, -0.45 | #90EE90 |
| Bathroom tile | -0.65, 0.03, -0.7 | #DDA0DD |

---

## How to Customise

### Change room colors

In `index.html`, find the room floor tile `<a-box>` elements and change the `color` attribute:

```html
<!-- Living room floor — change #FFE4B5 to any CSS color -->
<a-box position="-0.65 0.03 0.4" ... color="#FFE4B5"></a-box>
```

### Change room labels

Find the `<a-text>` elements and change the `value` attribute:

```html
<a-text value="Master Bedroom" ...></a-text>
```

### Add a new room

1. Add a new colored floor tile `<a-box>` at the desired position
2. Add interior wall divider `<a-box>` elements around it
3. Add an `<a-text>` label above it
4. Adjust outer wall positions/sizes if expanding the footprint

### Change model scale (real-world size)

Edit the `scale` on `#apartment-root`:
```html
<!-- Larger: ~90 cm wide (good for floor placement) -->
<a-entity id="apartment-root" scale="0.30 0.30 0.30" ...>

<!-- Smaller: ~36 cm wide (good for table) -->
<a-entity id="apartment-root" scale="0.12 0.12 0.12" ...>
```

### Change rotation speed

Edit the `animation` component's `dur` value (milliseconds):
```html
<!-- 20 seconds per rotation (slower) -->
animation="property: rotation; to: 0 360 0; dur: 20000; easing: linear; loop: true"
```

### Disable auto-rotation

Remove the `animation` component attribute from `#apartment-root`.

### Change brand colors

Each HTML file has a `:root` block at the top of its `<style>` section:
```css
:root {
  --color-navy: #1a3c5e;
  --color-gold: #c9a84c;
  --color-white: #ffffff;
  --color-light-grey: #f5f5f5;
}
```
Change these values to match the client's brand.

---

## Upgrading A-Frame

If upgrading from 1.5.0, test these areas:
1. `scene.frame` still assigned in render loop (check A-Frame changelog)
2. `renderer.xr.getReferenceSpace()` still available
3. WebXR scene attributes (`webxr`, `requiredFeatures`) syntax unchanged
4. `scene.enterAR()` still a valid method

---

## Known Limitations

- **iOS 15 and below**: WebXR hit-test not supported. Users see a "not supported" message.
- **Firefox**: WebXR AR not supported on Android Firefox as of 2025.
- **Samsung Internet**: Partial WebXR support; recommend Chrome.
- **Local file:// protocol**: WebXR requires HTTPS or localhost — opening `index.html` directly from the filesystem will not work. Must use a local server or GitHub Pages.
- **Lighting**: Poor lighting degrades ARCore/ARKit surface detection. The reticle may not appear in dim rooms.
