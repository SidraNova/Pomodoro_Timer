
# Pomodoro Timer

A single-screen Pomodoro timer web app with daily session history.

## How to Run

### Option 1 — Open directly (no install needed)
```
open index.html
```
Or drag `index.html` into any browser window.

### Option 2 — Local dev server (recommended to avoid font CORS)
```bash
# With Node.js
npx serve .

# Or with Python
python3 -m http.server 8080
```
Then open **http://localhost:8080** (or the port shown).

### Option 3 — Deployed URL
> If deployed (e.g. Netlify / Vercel / GitHub Pages), the URL would go here.
> Example: `https://your-username.github.io/pomodoro-timer/`

No build step, no dependencies — pure HTML/CSS/JS.

---

## Features
- ⏱ Configurable focus (1–90 min) and break (1–30 min) durations
- ▶ Start / Pause / Resume / Reset / Skip controls
- 🔔 Audible triple-tone cue when a cycle ends
- 📋 Daily history of completed focus sessions (persists across page reloads, resets on new calendar day)
- ⌨️ Keyboard accessible — full tab navigation and focus states
- 📱 Responsive — works on 360px phone screens up to 1440px desktops
