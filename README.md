# AR Apartment Viewer

A WebAR apartment showcase for real estate businesses. Customers scan a QR code (from a brochure or WhatsApp), their phone camera opens a webpage, and a 3D apartment model appears floating on any flat surface — **no app download, no marker, no printout required**.

---

## How It Works

1. Customer scans QR code or taps a WhatsApp link
2. Browser opens (Chrome on Android, Safari on iOS)
3. Customer taps **Start AR** and allows camera access
4. Points phone at any flat surface (table, floor, counter)
5. A gold ring appears when a surface is detected
6. Customer taps — the 3D apartment model appears on the surface
7. They walk around it to explore all rooms

---

## Files

| File | Purpose |
|---|---|
| `index.html` | Main WebAR experience (the AR viewer) |
| `qr.html` | QR code generator — paste your URL, download QR as PNG |
| `marker.html` | Customer instructions page (how to use the AR viewer) |
| `README.md` | This deployment guide |
| `CLAUDE.md` | Notes for future Claude sessions |

---

## Compatible Devices

| Device | Requirement |
|---|---|
| Android | Chrome 81+, ARCore installed (free, pre-installed on most phones) |
| iPhone / iPad | Safari on iOS 16.4 or later |
| Hosting | Must be served over **HTTPS** — GitHub Pages does this automatically |

---

## Step 1 — Test Locally

WebXR requires HTTPS or `localhost`. To test locally:

1. Install a simple local server. The easiest option — open a terminal in the project folder and run:
   ```
   npx serve .
   ```
   Or use VS Code's **Live Server** extension.

2. Open `http://localhost:3000` (or whichever port) in Chrome on your computer.

3. To test the AR on your phone, connect your phone to the same Wi-Fi and open the local IP address (e.g. `http://192.168.1.5:3000`).  
   **Note:** AR (WebXR) requires HTTPS even on local network — use GitHub Pages for real testing on phones.

---

## Step 2 — Deploy to GitHub Pages

### 2a. Create a GitHub account
Go to [github.com](https://github.com) and sign up if you don't have an account.

### 2b. Create a new repository
1. Click the **+** icon → **New repository**
2. Repository name: `apartment-ar` (must match exactly)
3. Set to **Public**
4. Click **Create repository**

### 2c. Upload your files
1. On the new repository page, click **Add file** → **Upload files**
2. Drag and drop all 5 project files:
   - `index.html`
   - `qr.html`
   - `marker.html`
   - `README.md`
   - `CLAUDE.md`
3. Scroll down, add a commit message like `Initial upload`, click **Commit changes**

### 2d. Enable GitHub Pages
1. Go to your repository → **Settings** tab
2. In the left sidebar, click **Pages**
3. Under **Source**, select **Deploy from a branch**
4. Branch: **main** | Folder: **/ (root)**
5. Click **Save**
6. Wait 1–2 minutes, then refresh the page
7. A green banner will show your URL:  
   `https://YOUR-USERNAME.github.io/apartment-ar/`

---

## Step 3 — Generate Your QR Code

1. Open `https://YOUR-USERNAME.github.io/apartment-ar/qr.html`
2. Replace the placeholder URL with your actual GitHub Pages URL
3. Click **Generate**
4. Click **Download QR (PNG)** — saves `apartment-ar-qr.png` to your device

---

## Step 4 — Add to Brochure

- Recommended QR code print size: **minimum 3 × 3 cm** (larger is better)
- Place the QR code in a visible spot on the brochure
- Add text near it: *"Scan to see this apartment in 3D AR"*
- No marker or extra printout needed — just the QR code

---

## Step 5 — Share on WhatsApp

**Option A — Share QR image:**
1. Download the QR PNG from `qr.html`
2. Send the image in WhatsApp chat or post to WhatsApp Status
3. Customers tap the image to open the link

**Option B — Share URL directly:**
1. Open `qr.html` → click **Copy Link**
2. Paste the URL directly in WhatsApp messages
3. Customers tap the link to open it in their browser

---

## Step 6 — Troubleshooting

**"AR Not Supported" message on Android**
- Make sure the customer is using **Chrome** (not Samsung Browser or Firefox)
- Install **ARCore** from the Play Store if prompted
- ARCore requires Android 7.0+ and a compatible device

**"AR Not Supported" message on iPhone**
- Requires **Safari** (not Chrome on iOS)
- Requires **iOS 16.4 or later** — go to Settings → General → Software Update
- iOS 15 and below cannot run WebXR AR

**Camera permission denied**
- Android: Settings → Apps → Chrome → Permissions → Camera → Allow
- iPhone: Settings → Safari → Camera → Allow

**Gold ring (reticle) not appearing**
- Move to a better-lit area
- Slowly pan the phone across the surface
- Plain surfaces (white table) work better than patterned ones
- Hold the phone at a slight downward angle, not straight down

**Model appears too small or too large**
- This is a miniature architectural model — it's designed to sit on a table like a scale model
- Default size is approximately 54 cm wide
- To change the size, edit the `scale` attribute in `index.html` on the `#apartment-root` entity

**HTTPS error when testing locally**
- WebXR requires HTTPS (except on `localhost`)
- Deploy to GitHub Pages for phone testing, or use `localhost` with a local server

---

## Customisation Quick Reference

| What to change | Where | What to edit |
|---|---|---|
| Room colors | `index.html` | `color` attribute on room floor `<a-box>` elements |
| Room labels | `index.html` | `value` attribute on `<a-text>` elements |
| Model size | `index.html` | `scale` on `#apartment-root` entity |
| Rotation speed | `index.html` | `dur` in `animation` component (ms, lower = faster) |
| Brand colors | Any `.html` file | CSS `:root` variables at top of `<style>` |

See `CLAUDE.md` for detailed customisation instructions.
