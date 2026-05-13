# CLAUDE.md — Developer Notes for Apartment AR Project

## Project Overview

This is a **marker-based WebAR** apartment showcase system built for Indian real estate (Tribhuvan Realty).  
Customers scan a QR code on a brochure or WhatsApp → their phone browser opens → they point camera at the Hiro marker → a 3D apartment floor plan model floats in AR.

**Key constraint:** Zero npm, zero build tools. Pure HTML/CSS/JS. Hosted on GitHub Pages (free).

---

## File Purposes

| File | Purpose | Key Technology |
|------|---------|----------------|
| `index.html` | Main AR experience — the URL that goes in the QR code | A-Frame 1.5.0 + AR.js 3.4.5 |
| `qr.html` | QR code generator and download page | QRious.js 4.0.2 |
| `marker.html` | Hiro marker display + print page | Plain HTML/CSS |
| `README.md` | End-user deployment guide | Markdown |
| `CLAUDE.md` | This file — developer notes | Markdown |

---

## CDN Versions (pinned — do not change without testing)

```html
<!-- A-Frame v1.5.0 -->
<script src="https://cdn.jsdelivr.net/npm/aframe@1.5.0/dist/aframe.min.js"></script>

<!-- AR.js v3.4.5 for A-Frame -->
<script src="https://cdn.jsdelivr.net/npm/ar.js@3.4.5/aframe/build/aframe-ar.js"></script>

<!-- QRious.js v4.0.2 -->
<script src="https://cdn.jsdelivr.net/npm/qrious@4.0.2/dist/qrious.min.js"></script>
```

> **Warning:** AR.js v3.4.5 and A-Frame 1.5.0 are tested together. Upgrading either independently can break detection. Always test on a real device after any CDN version change.

---

## Architecture Decisions

### Why marker-based AR (not WebXR)?

- WebXR world-tracking requires HTTPS AND a user gesture AND Android Chrome 81+ — much stricter.
- Marker-based AR.js works on iOS Safari 12+ and Android Chrome with a standard camera tag.
- Hiro marker is pre-built into AR.js — no custom marker training needed.
- Works reliably in Indian lighting conditions when printed correctly.

### Why A-Frame primitives instead of .glb/.obj?

- No 3D modeling experience needed.
- No file size / CORS issues with GitHub Pages.
- Easier for future Claude sessions to edit — it's just HTML attributes.
- All primitives (box, plane, text) are natively supported by A-Frame.

### Why QRious.js instead of a server-generated QR?

- No backend required — pure client-side canvas rendering.
- `canvas.toDataURL()` enables download without server.
- Error correction level `H` (30%) survives brochure print imperfections.

---

## How to Customize

### Change apartment colors

In `index.html`, find each room floor plane and update the `color` attribute:

```html
<!-- Living Room — change color here -->
<a-plane id="living-floor" color="#FFE4B5" ...>

<!-- Bedroom — change color here -->
<a-plane id="bedroom-floor" color="#B0C4DE" ...>

<!-- Kitchen — change color here -->
<a-plane id="kitchen-floor" color="#90EE90" ...>

<!-- Bathroom — change color here -->
<a-plane id="bathroom-floor" color="#DDA0DD" ...>
```

### Change room names / labels

Find the `<a-text>` elements and update the `value` attribute:

```html
<a-text value="Master Bedroom" ...>
<a-text value="Hall" ...>
<a-text value="Kitchen" ...>
```

Note: Use `\n` for line breaks within A-Frame text values.

### Change model dimensions (apartment size / proportions)

The floor and outer walls define the overall size. The floor is `6x6` units:

```html
<!-- Floor — change width and height to resize the apartment footprint -->
<a-plane id="floor" width="6" height="6" ...>

<!-- Walls — adjust position and dimensions to match the new floor size -->
<!-- North wall: position Z should = -(floor_height/2) -->
<a-box id="wall-north" position="0 0.5 -3" width="6" ...>
<!-- South wall: position Z should = +(floor_height/2) -->
<a-box id="wall-south" position="0 0.5 3" width="6" ...>
```

**Room floor positions** follow the same coordinate system — adjust `position` and `width/height` attributes to match your new layout.

### Change rotation speed

Find the animation component on `#apartment-group`:

```html
<a-entity id="apartment-group"
  animation="property: rotation; to: 0 360 0; dur: 10000; ...">
```

- `dur: 10000` = 10 seconds for one full rotation.
- Increase to `dur: 20000` for slower rotation.
- Remove the `animation` attribute entirely to disable rotation.

### Change wall height

All `<a-box>` walls have `height: 1`. Change to `height: 1.5` for taller walls.  
Also raise the roof `<a-box id="roof">` Y position from `1.02` to `1.52` to match.

### Add more rooms

Copy an existing room floor plane and interior divider, adjust position/width/height:

```html
<!-- Example: Add a second bedroom -->
<a-plane
  id="bedroom2-floor"
  rotation="-90 0 0"
  position="X Y Z"
  width="W" height="H"
  color="#ADD8E6">
</a-plane>

<a-text
  value="Bedroom 2"
  position="X 0.6 Z"
  rotation="-90 0 0"
  align="center"
  color="#1a3c5e"
  width="2">
</a-text>
```

### Change the branding name

In `index.html`, update the loading screen logo:

```html
<div class="loading-logo">🏠 Your Company Name</div>
```

In `index.html`, update the top banner:

```html
<div id="top-banner">
  <span>Apartment AR View</span> — Your custom subtitle
</div>
```

---

## How to Replace Hiro Marker with a Custom Marker (Advanced)

The Hiro marker is convenient but widely known — anyone with an AR.js app can trigger your marker.  
For exclusivity, use a custom marker:

### Step 1 — Create marker image
- Design a simple, high-contrast black-and-white image (at least 50% black border).
- Use [AR.js Marker Training](https://ar-js-org.github.io/AR.js/three.js/examples/marker-training/examples/generator.html) to generate `.patt` and `.mind` files.

### Step 2 — Host the `.patt` file
Upload `custom.patt` to your GitHub Pages repository.

### Step 3 — Update `index.html`

Replace:
```html
<a-marker preset="hiro">
```

With:
```html
<a-marker type="pattern" url="https://YOUR-USERNAME.github.io/apartment-ar/custom.patt">
```

### Step 4 — Update `marker.html`
Replace the Hiro marker `<img>` `src` with your custom marker image URL.

### Step 5 — Update `qr.html`
Update the marker preview `<img>` to show your custom marker.

---

## A-Frame Coordinate System Reference

```
Y (up)
|
|_____ X (right)
 \
  Z (toward viewer / south)
```

- Entities in `<a-marker>` are positioned relative to marker center = (0, 0, 0)
- Positive Y is UP from the marker surface
- The apartment model sits at Y=0 on the marker plane
- Floor planes need `rotation="-90 0 0"` to lie flat (A-Frame planes are vertical by default)

---

## Known Issues / Gotchas

1. **iOS HTTPS requirement** — `index.html` must be served over HTTPS for camera access on iPhone. Local `file://` won't work for testing on iOS.

2. **A-Frame text font** — `<a-text>` uses a custom SDF font loaded from `cdn.aframe.io`. If the CDN is slow, text labels may render late. This is normal.

3. **AR.js debug panel** — if you see a grey debug box, check that `debugUIEnabled: false` is set in the `arjs` attribute.

4. **Model clipping** — if parts of the model disappear at close range, add `renderer="logarithmicDepthBuffer: true"` to `<a-scene>` (already done).

5. **Animation loop** — the `animation` component loops by default (`loop: true`). If you want the model to only rotate once, remove `loop: true`.

6. **Marker detection distance** — optimal range is 20–60 cm. Beyond 80 cm, tracking degrades significantly, especially on lower-end phones.

---

## Testing Checklist

- [ ] Camera permission granted on test device
- [ ] Hiro marker fully visible in frame (no corners cut off)
- [ ] Model appears and rotates within 3–5 seconds of marker detection
- [ ] Room labels are readable
- [ ] Help modal opens and closes correctly
- [ ] QR code generates and downloads correctly in `qr.html`
- [ ] Marker prints cleanly at 10×10 cm minimum from `marker.html`
- [ ] Tested on Android Chrome
- [ ] Tested on iPhone Safari (requires GitHub Pages URL)

---

## Future Improvements (Beyond MVP)

- Add multiple apartment floor plans (switchable via URL parameter)
- Add property info overlay (price, area, contact) as HTML overlay
- Custom marker per apartment unit for unique QR-to-model mapping
- Add ambient occlusion and point lighting for better visual quality
- WhatsApp Business API integration for auto-sending AR links
- Analytics via Google Analytics 4 to track QR scans and AR sessions
