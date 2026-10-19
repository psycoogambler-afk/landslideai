# FloodGuard AI – Netlify live dashboard

ESP32 (your Wi-Fi) ──HTTPS POST every 3 s──▶ Netlify Function `/api/ingest` ──▶ stored in Netlify Blobs
Browser ◀── polls `/api/data` every 2 s ── Netlify Function ◀── Blobs

## 1. Deploy the website (Netlify)
1. Put this folder on GitHub (or drag-and-drop is NOT enough – functions need a build, so use GitHub / Netlify CLI).
2. Netlify → Add new site → Import from Git → pick the repo. Build settings are read from `netlify.toml`.
3. Site settings → Environment variables → add `FG_KEY` = a secret word (example: `exhibit2026`).
4. Deploy. Open `https://YOUR-SITE.netlify.app` – it shows "Waiting for the ESP32".

CLI alternative: `npm i -g netlify-cli` → `npm install` → `netlify deploy --prod`

## 2. Flash the ESP32
Open `firmware/FloodGuard_ESP32/config.h` and edit:
```
FG_WIFI_SSID   "your wifi name"        (must be 2.4 GHz)
FG_WIFI_PASS   "your wifi password"
FG_NETLIFY_URL "https://YOUR-SITE.netlify.app/api/ingest"
FG_API_KEY     "exhibit2026"           (same as FG_KEY in Netlify)
```
Upload with Arduino IDE (board: ESP32 Dev Module). Open Serial Monitor at 115200:
you should see `[wifi] connected` and `[push] HTTP 200` every 3 seconds.

## Troubleshooting
- `[push] HTTP 401` → FG_API_KEY ≠ FG_KEY.  `HTTP -1` → Wi-Fi/URL wrong (URL must start with https://).
- Phone hotspot works fine as the Wi-Fi for the exhibition.
- Calibration: edit `FG_CAL_*` values in config.h (dashboard calibration buttons were removed).

## Accuracy demo (uses the CSV)
Open `https://YOUR-SITE.netlify.app/accuracy.html`. It runs the trained model on the 1,800 held-out rows of
`test_samples.csv` (never used for training) and shows AI 93.4 % vs simple rule method 77.7 %, plus the confusion matrix.
The data is synthetic (simulated flood events), not measured at a real river.
