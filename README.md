# Apartment AR — WebAR Apartment Viewer

A **zero-install** Augmented Reality apartment showcase system for real estate brochures and WhatsApp.  
Customers scan a QR code → browser opens → 3D apartment model floats above the Hiro marker. No app download needed.

---

## 📁 File Structure

```
apartment-ar/
├── index.html     ← Main WebAR experience (share this URL)
├── qr.html        ← QR code generator and download
├── marker.html    ← Hiro marker display / print page
├── README.md      ← This guide
└── CLAUDE.md      ← Developer / AI session notes
```

---

## 🚀 Deployment Guide — Step by Step

### Step 1 · Test Locally

1. Download or clone this folder to your computer.
2. Open **Chrome** (desktop or Android).
3. Open `index.html` directly (`File → Open`) — no server needed because AR.js marker-based mode does **not** require HTTPS locally.
4. Allow camera access when prompted.
5. Print the Hiro marker or open `marker.html` on a second phone/tablet.
6. Point your camera at the Hiro marker — the 3D apartment model should appear.

> **iPhone / Safari note:** iOS requires HTTPS for camera access. Test locally with a tool like `npx serve` or deploy to GitHub Pages first.

---

### Step 2 · Create a GitHub Account (if needed)

1. Go to [https://github.com](https://github.com).
2. Click **Sign up** → enter email, password, username.
3. Verify your email.

---

### Step 3 · Create a Repository

1. Click the **+** icon → **New repository**.
2. Repository name: `apartment-ar` (exact — this forms your URL).
3. Set to **Public** (required for free GitHub Pages hosting).
4. Click **Create repository**.

---

### Step 4 · Upload Files

**Option A — Browser upload (no Git needed):**

1. Open your new repository.
2. Click **Add file → Upload files**.
3. Drag and drop all 5 files (`index.html`, `qr.html`, `marker.html`, `README.md`, `CLAUDE.md`).
4. Click **Commit changes**.

**Option B — Git CLI:**

```bash
git init
git add .
git commit -m "Initial WebAR apartment viewer"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/apartment-ar.git
git push -u origin main
```

---

### Step 5 · Enable GitHub Pages

1. In your repository, click **Settings** (top menu).
2. Scroll down to **Pages** (left sidebar).
3. Under **Source**, select branch: `main`, folder: `/ (root)`.
4. Click **Save**.
5. Wait ~60 seconds, then refresh. A green banner shows your URL:

```
https://YOUR-USERNAME.github.io/apartment-ar/
```

Copy this URL — you'll use it in the next step.

---

### Step 6 · Generate the QR Code

1. Open `https://YOUR-USERNAME.github.io/apartment-ar/qr.html`.
2. Paste your GitHub Pages URL into the input field.
3. Click **Generate QR**.
4. Click **⬇ Download QR PNG** — saves a 400×400 px print-quality image.

---

### Step 7 · Add to Brochure

- **Minimum QR size:** 3 × 3 cm on printed page.
- **Recommended:** 4 × 4 cm for reliability.
- Place the Hiro marker on the same page (download from `marker.html`) at **minimum 10 × 10 cm**.
- Add text next to QR: *"Scan with your phone camera to see this apartment in 3D AR"*
- Caption under marker: *"Point your camera here after scanning"*

---

### Step 8 · Share on WhatsApp

**Option A — URL in chat:**
```
View this apartment in 3D AR — no app needed!
👉 https://YOUR-USERNAME.github.io/apartment-ar/
```
Then send the `marker.html` link or the printed marker image separately.

**Option B — QR Image:**
- Send the downloaded QR PNG image in the WhatsApp chat.
- Customer **taps the link in the image preview** (on some Android versions) or screenshots and scans with camera.
- Also send: `https://YOUR-USERNAME.github.io/apartment-ar/marker.html` so they can open the marker on screen.

---

## 🔧 Troubleshooting

### iPhone / Safari — camera not working
- Requires HTTPS → must be hosted on GitHub Pages (not local file).
- Go to **Settings → Safari → Camera** → set to **Allow**.
- iOS 12+ supported. If below iOS 12, upgrade is required.

### Android Chrome — camera blocked
- In Chrome, tap the **lock icon** in address bar → allow camera.
- Or go to **Settings → Site Settings → Camera** → find the AR URL → allow.

### Model not appearing
- Make sure the **full Hiro marker is visible** in camera frame — no cutoff.
- Try better lighting — avoid dark rooms and heavy shadows on the marker.
- Hold phone **20–50 cm** from the marker for optimal detection distance.
- Slow down movement — fast motion breaks tracking.

### Marker image not loading in `marker.html`
- Requires internet connection (image loads from GitHub CDN).
- Alternatively, download the marker image and save it locally:
  ```
  https://raw.githubusercontent.com/AR-js-org/AR.js/master/data/images/hiro.png
  ```
  Save as `hiro.png` in the project folder and update `marker.html` `src` attribute.

### QR code not scanning
- Ensure QR is at least 3 × 3 cm when printed.
- High contrast printing — black ink on white background.
- No glare on printed surface.

---

## 📊 CDN Versions Used

| Library    | Version | CDN URL |
|------------|---------|---------|
| A-Frame    | 1.5.0   | `https://cdn.jsdelivr.net/npm/aframe@1.5.0/dist/aframe.min.js` |
| AR.js      | 3.4.5   | `https://cdn.jsdelivr.net/npm/ar.js@3.4.5/aframe/build/aframe-ar.js` |
| QRious.js  | 4.0.2   | `https://cdn.jsdelivr.net/npm/qrious@4.0.2/dist/qrious.min.js` |

---

## 📄 License

This project is free to use for personal and commercial real estate purposes.  
A-Frame: MIT | AR.js: MIT | QRious: MIT
