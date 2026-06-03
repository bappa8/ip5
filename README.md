# 🌐 Internet Speed Test

A lightweight, single-file web app that measures your **internet connection speed** — **ping, download, and upload** — and displays your **public IP address and geolocation** (country, city, ISP). It features three live animated **circular gauges** and a clean, color-coded interface.

Built with plain **HTML, CSS, and vanilla JavaScript** — no frameworks, no build step, no dependencies.

---

## ✨ Features

- **⚡ Ping / latency** — averages 5 lightweight requests (with a warm-up) using a CORS-free image technique that works on restrictive networks.
- **⬇ Download speed** — measures *actual* bytes received via a streamed response, updating live as data arrives.
- **⬆ Upload speed** — runs a **sustained ~20-second** test, uploading chunks continuously for an accurate, stable result with live updates.
- **Three live gauges** — animated circular speed meters for Ping, Download, and Upload, each color-coded.
- **"Both" mode** — runs Ping → Download → Upload in a single click.
- **IP & geolocation lookup** — shows public IP, country, city, and ISP.
- **Resilient lookups** — chains multiple providers (with JSONP fallback) so it works even from `file://` and on restrictive networks.
- **Fully self-contained** — one `.html` file, opens directly in any modern browser.
- **Colorful, customizable UI** — color-matched labels, values, and gauges.

---

## 📸 Interface Overview

Layout, top to bottom:

| Section | Details |
|---|---|
| **Heading** | "Internet Speed Test" |
| **IP Info** | IP Address (deep orange, bold), Country (green), City (purple), ISP (teal) |
| **Live readout** | Large current value in **Mbps** + the active test type |
| **Gauges** (one row) | ⚡ Ping (teal) · ⬇ Download (blue) · ⬆ Upload (purple) |
| **Results** | Ping (teal) · Download (blue) · Upload (purple) — bold |
| **Buttons** | Download · Upload · Both |
| **Footer** | © Bappa Ghosh. All rights reserved. (blood red) |

### 🎨 Color scheme
| Item | Color |
|---|---|
| IP Address | Deep orange `#cc5500` |
| Country | Green `#008000` |
| City | Purple `#8e24aa` |
| ISP | Teal `#00838f` |
| Ping (gauge + result) | Teal `#00897b` |
| Download (gauge + result) | Blue `#1565c0` |
| Upload (gauge + result) | Purple `#6a1b9a` |
| Copyright footer | Blood red `#8a0303` |

---

## 🚀 Getting Started

### Option 1 — Open directly (simplest)
1. Download `speedtest.html`.
2. Double-click it to open in your browser (Chrome, Firefox, Edge, Safari).

> Most features work over `file://` thanks to JSONP / image-based fallbacks. Some browsers may still block a few cross-origin requests under `file://`.

### Option 2 — Run a local server (recommended, most reliable)
From the folder containing the file:

```bash
# Python 3
python3 -m http.server 8000
```

Then open: **http://localhost:8000/speedtest.html**

This avoids `file://` restrictions and gives the most accurate, reliable results.

---

## 🛠️ How It Works

### IP & Geolocation
Tries several providers in order and stops at the first that succeeds:

1. **JSONP providers** (immune to CORS, work on `file://`):
   - [`ip-api.com`](http://ip-api.com) — IP, country, city, ISP
   - [`geojs.io`](https://get.geojs.io) — IP, country, city, organization
2. **CORS `fetch` providers** (work over http/https):
   - [`ipwho.is`](https://ipwho.is)
   - [`ipapi.co`](https://ipapi.co)
3. **Last resort:** [`api.ipify.org`](https://api.ipify.org) (IP only)

### Speed & Latency Measurement
| Test | Method | Key setting |
|---|---|---|
| **Ping** | Times an `Image()` load (not CORS-restricted) to `cloudflare.com/favicon.ico`; averages 5 pings after one warm-up. | 5 iterations |
| **Download** | Streams a 25 MB payload from `speed.cloudflare.com/__down` and counts real bytes received. | `bytesToFetch = 25 MB` |
| **Upload** | Repeatedly POSTs 2 MB random chunks to `speed.cloudflare.com/__up` for ~20 s, accumulating bytes. | `TEST_MS = 20000`, `chunkSize = 2 MB` |

Speed formula: `Mbps = (bytes × 8) / seconds / 1,000,000`.
Upload payloads are generated quickly with `crypto.getRandomValues` in 64 KB chunks.

### The Gauges
Each gauge is an SVG ring driven by `stroke-dashoffset`:
- **Download / Upload:** fuller = faster. Full ring at **200 Mbps** (`METER_MAX`).
- **Ping:** fuller = *lower* latency (better). Empty at **300 ms** (`PING_MAX`).

---

## 🌍 External Services Used

| Service | Purpose |
|---|---|
| `speed.cloudflare.com` | Download & upload throughput testing |
| `www.cloudflare.com` (favicon) | Ping / latency |
| `ip-api.com`, `geojs.io`, `ipwho.is`, `ipapi.co`, `api.ipify.org` | IP & geolocation lookup |

> All requests go to public, third-party APIs. An active internet connection is required.

---

## 📂 Project Structure

```
.
├── speedtest.html   # The entire app (HTML + CSS + JS in one file)
└── README.md        # This file
```

---

## ⚠️ Accuracy & Limitations

Browser-based speed tests are **approximate** and should be treated as a rough guide:

- "Ping" is a full round-trip resource load (DNS + TLS + request), **not** true ICMP latency.
- Single-connection tests can **understate** speed on very fast lines (professional tools use multiple parallel connections).
- Results depend on the test-server location, network conditions, ad-blockers, and browser CORS policy.
- Free geolocation/speed endpoints may rate-limit or return `403` on some networks; the multi-provider fallback chain mitigates this.

---

## 🎨 Customization

All styling lives in the `<style>` block; all logic in the `<script>` block of `speedtest.html`.

- **Field/label colors:** `#ipAddress`, `#country`, `#city`, `#isp`, and the `.ip-row:nth-child(n) .ip-label` rules.
- **Result & gauge colors:** `.result-item:nth-child(n)`, `#pingResult`/`#downloadResult`/`#uploadResult`, and `.meter-fill-*` / `.meter-label-*`.
- **Heading size:** `h1 { font-size: ... }`.
- **Footer:** the `.copyright` rule (blood red `#8a0303`).
- **Test parameters (JS):**
  - Download size → `bytesToFetch`
  - Upload duration → `TEST_MS` · chunk size → `chunkSize`
  - Gauge scales → `METER_MAX` (Mbps) and `PING_MAX` (ms)

---

## 🔒 Security Note

This project intentionally contains **no third-party tracking or obfuscated scripts** — only the documented public APIs above. If you obtain a version of this file containing an unfamiliar external `<script src="...">` (especially with a long, random-looking URL), **do not run it** and remove that line; it is not part of this project.

---

## 🌐 Browser Support

Works in modern browsers supporting `fetch`, streaming response bodies (`ReadableStream`), SVG, and `crypto.getRandomValues` — i.e. recent Chrome, Firefox, Edge, and Safari.

---

## 📄 License

© Bappa Ghosh. All rights reserved.
