# Solar Energy Dashboard -- Project Handoff

Last updated: March 6, 2026 (America/New_York)

## Project Overview

A custom solar energy monitoring dashboard for a residential SolarEdge 11.75 kW system. Built as a single-page web app deployed on Vercel, pulling live data from the SolarEdge Monitoring API.

- **Live URL:** https://solar-dashboard-five.vercel.app
- **GitHub repo:** `clarkeheyes-web/solar-dashboard` (private)
- **Address:** 6009 Jordan Woods Dr, Raleigh, NC
- **SolarEdge Site ID:** `4105155`
- **Utility provider:** Duke Energy (North Carolina residential, rate $0.1171/kWh)
- **Latest production deploy:** March 6, 2026
- **Latest app commit:** `b40ae65` ("Add estimated chart datasets and project handoff")

---

## Tech Stack

| Layer | Tech |
|-------|------|
| Frontend | Vanilla HTML/CSS/JS -- single file: `public/index.html` |
| Charts | Chart.js 4.4.1 (via CDN) |
| Backend API | Python serverless functions (Vercel) |
| SolarEdge proxy | `api/solaredge.py` |
| Duke Energy proxy | `api/fetch-duke-energy.py` (currently broken -- see below) |
| Hosting | Vercel (may migrate to Railway.app or Raspberry Pi) |
| Scheduling | `vercel.json` cron: Duke Energy fetch daily at 6 AM |

---

## File Structure

```
solar-dashboard/
+-- public/
|   +-- index.html          # Entire frontend: HTML + CSS + JS (~32,000 chars)
+-- api/
|   +-- solaredge.py        # SolarEdge API proxy (working)
|   +-- fetch-duke-energy.py # Duke Energy proxy (broken on Vercel)
+-- requirements.txt        # pyduke-energy>=1.0.0, aiohttp>=3.9.0
+-- vercel.json             # Cron config for Duke Energy
+-- .env.example            # Environment variable template
```

---

## Environment Variables (set in Vercel dashboard)

```
SOLAREDGE_API_KEY=8D3S390Y9DF76K89RLNBTFAO683SLW2T
SOLAREDGE_SITE_ID=4105155
DUKE_EMAIL=clarke.heyes@gmail.com
DUKE_PASSWORD=[user's Duke Energy password]
```

---

## API Endpoints

### `/api/solaredge` (GET)
Returns bundled JSON with 15-minute server-side cache (`Cache-Control: s-maxage=900`).

Response shape includes:
- `overview` -- current power, lifetime/year/month/day energy totals
- `power_today` -- 96 quarter-hour slots, unit: **W** (divide by 1000 for kW)
- `energy_month` -- 31 daily values, unit: **Wh** (divide by 1000 for kWh)
- `energy_year` -- 13 monthly values, unit: **Wh** (divide by 1000 for kWh)

### `/api/fetch-duke-energy` (GET) -- BROKEN
Returns `{ "error": "pyduke-energy library not available", "status": "dependency_missing" }`
Fails on Vercel due to Python runtime limitations. Also failing in Home Assistant with 403 Forbidden from Duke Energy's updated auth API.

---

## Frontend Architecture (`public/index.html`)

All frontend code in one file. Key sections:

### Charts object
```javascript
const charts = {
  main: Chart.getChart('mainChart'),     // Energy Production vs Consumption
  monthly: Chart.getChart('monthlyChart'), // Monthly Energy Summary
  donut: Chart.getChart('donutChart'),   // Energy distribution donut
  cost: Chart.getChart('costChart')      // Monthly Cost & Savings
}
```

### mainChart datasets
- `[0]` "Solar Production" -- yellow -- populated from SolarEdge data
- `[1]` "Est. Home Consumption" -- blue -- estimated by selected time range
- `[2]` "Est. Grid Export" -- green -- estimated as solar above baseline consumption

### setTimeRange(el) function
Switches main chart between Day/Week/Month/Year. Fetches `/api/solaredge` and slices:
- Day: `power_today` values (W to kW), filters nulls, labels as "6:15 AM"
- Week: last 7 entries of `energy_month` (Wh to kWh)
- Month: all 31 entries of `energy_month` (Wh to kWh)
- Year: `energy_year` values, filters nulls (Wh to kWh)

Also populates estimated datasets for each time range:
- Day: `AVG_POWER_KW = 3.8` baseline for home consumption, export = `max(0, solar - baseline)`
- Week/Month: `AVG_DAILY_KWH = EST_CONSUME / 30` baseline per day
- Year: `EST_CONSUME = 1150` baseline per month
- On initial page load, the active time button is applied automatically so all three datasets render immediately.

### loadDukeData() function
Falls back to estimating costs from SolarEdge production data:
```javascript
const RATE = 0.1171;        // Duke Energy NC residential $/kWh
const EST_CONSUME = 1150;   // Estimated monthly home consumption kWh
const bill = Math.max((EST_CONSUME - solarKwh) * RATE, 12);
```

---

## Key Constants

```javascript
const RATE = 0.1171;           // Duke Energy NC residential rate $/kWh
const EST_CONSUME = 1150;      // Estimated monthly home consumption kWh
const AVG_DAILY_KWH = 38.3;   // EST_CONSUME / 30
const AVG_POWER_KW = 3.8;     // Estimated average home power draw (kW)
```

---

## What Has Been Built

1. Full dashboard UI: KPI cards, charts, power flow diagram, environmental stats
2. Date format bug fixed -- SolarEdge API date strings now parse correctly
3. LIVE DATA badge -- shows data freshness and last update time
4. Duke Energy credentials added to Vercel env vars
5. Tab navigation fixed -- showTab() function added
6. Monthly Cost & Savings chart fixed -- now uses estimated costs from SolarEdge data
7. KPI card bug fixed -- removed erroneous setText calls overwriting Grid Consumption
8. Time selector buttons fixed -- setTimeRange() function added (Day/Week/Month/Year)
9. Main chart estimate logic added -- `Est. Home Consumption` and `Est. Grid Export` now render for Day/Week/Month/Year.
10. Production deploy completed on March 6, 2026 with chart estimate changes live at https://solar-dashboard-five.vercel.app.
11. GitHub repo updated with same changes in commit `b40ae65`.

---

## Pending Work

### 1. SolarEdge integration for Home Assistant
Raspberry Pi at 192.168.86.113:8123 runs Home Assistant. SolarEdge integration NOT yet configured.
Steps: Settings > Devices & Services > Add Integration > SolarEdge > Site ID 4105155 + API key.

### 2. Duke Energy fix (blocked)
pyduke-energy returns 403 Forbidden from https://api-v2.cma.duke-energy.app/login-services/auth-token
Affects both Vercel function AND the Home Assistant duke_energy integration.
Requires pyduke-energy library update, or switch to Green Button CSV export.

### 3. Infrastructure migration (optional)
Consider Railway.app or running directly on the Raspberry Pi with Flask:
- Convert api/solaredge.py and api/fetch-duke-energy.py from Vercel handlers to Flask routes
- Create app.py as main Flask server
- Add Flask>=3.0.0 to requirements.txt
- Add Procfile: web: python app.py

---

## Infrastructure

- **Hosting:** Vercel (free tier)
- **Raspberry Pi:** 192.168.86.113:8123, running Home Assistant + Tailscale
- **Tailscale:** Already configured on Pi, provides remote access
- **Home Assistant integrations active:** Tuya, Ring, Blink, Duke Energy (failing 403), Roborock, tplink, Bluetooth, Cast, ZHA
- **Home Assistant integrations NOT yet added:** SolarEdge

---

## Notes for Codex

- All chart logic is client-side JavaScript in `public/index.html`. No build step.
- The SolarEdge API key is server-side only (in `api/solaredge.py`). Frontend never sees it.
- `charts.main` / `Chart.getChart('mainChart')` is the primary chart to work with.
- `setTimeRange()` now includes estimated consumption/export population for all time ranges.
- SolarEdge data is in **Wh** (not kWh) -- always divide by 1000 before displaying.
- `power_today` is in **W** (not kW) -- divide by 1000; values are instantaneous power.
- GitHub web editor uses CodeMirror 6. To programmatically edit:
  ```javascript
  const view = document.querySelector('.cm-content')?.cmTile?.view;
  view.dispatch({ changes: { from: insertPos, to: insertPos, insert: newCode } });
  ```
