# Masroof — مصروف 💸
Personal spending tracker PWA — works on Android & iPhone as an installable app.

## Features
- Add cash or digital expenses with emoji categories (matching your Excel format)
- Supports Arabic names (right-to-left auto-detection)
- Stats page with category and daily breakdown charts
- Export to Excel (.xlsx) in the exact same format as your existing files
- Export to CSV
- Backup/restore all data as JSON
- Works offline after first load
- Installable on home screen (Android + iOS)
- Auto-fill from bank SMS via MacroDroid (Android)

---

## How to host (free, 2 minutes)

### Option 1 — Netlify Drop (easiest)
1. Go to https://app.netlify.com/drop
2. Drag the entire `spending-pwa` folder onto the page
3. You get a live URL like `https://random-name.netlify.app`
4. Open that URL on your phone → "Add to Home Screen"

### Option 2 — GitHub Pages
1. Create a free GitHub account at github.com
2. New repo → upload all 6 files
3. Settings → Pages → Deploy from main branch
4. URL: `https://yourusername.github.io/masroof`

### Option 3 — Vercel
1. `npm i -g vercel`
2. Inside this folder: `vercel`
3. Follow prompts → free live URL

---

## Install on Android
1. Open the hosted URL in **Chrome**
2. A banner will appear: "Install app on home screen" → tap Install
3. Or: tap Chrome menu (⋮) → "Add to Home Screen"

## Install on iPhone
1. Open the hosted URL in **Safari** (must be Safari)
2. Tap the Share button (□↑)
3. Scroll down → "Add to Home Screen"
4. Tap Add

---

## Auto-detect bank SMS on Android (MacroDroid)
1. Install MacroDroid (free) from Play Store
2. New Macro → Trigger: **SMS Received**
   - From number: your bank's SMS sender (e.g. CIB-Egypt, NBEBANK)
3. Action: **Launch Website**
   - URL: `https://your-app-url.netlify.app/?amount={sms_amount}&name=Bank+Payment`
   - Use MacroDroid's variable extraction to pull the amount from the SMS
4. The app will open with the amount pre-filled, ready to confirm

### SMS regex for Egyptian banks
Most bank SMS look like: `Your account was debited EGP 250.00`
Regex to extract amount: `EGP ([\d,]+\.?\d*)`

---

## File structure
```
spending-pwa/
├── index.html      ← Main app
├── sw.js           ← Service worker (offline support)
├── manifest.json   ← PWA manifest (makes it installable)
├── icon-192.svg    ← App icon
├── icon-512.svg    ← Large app icon
└── README.md       ← This file
```
