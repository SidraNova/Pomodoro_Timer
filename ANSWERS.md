# ANSWERS.md

## 1. How to Run

Open `index.html` directly in any modern browser — no install or build step required.

For a proper dev server (avoids any font-loading CORS issues):
```bash
npx serve .          # Node.js
# or
python3 -m http.server 8080   # Python
```
Then visit `http://localhost:8080`.

---

## 2. Stack & Design Choices

**Stack:** Vanilla HTML, CSS, and JS — no frameworks, no build tools, one HTTP request per asset. The brief said "use it like a real user"; removing a compile step makes that frictionless.

**Two specific design decisions:**

1. **SVG ring instead of a progress bar.** A circular countdown ring wrapping the time display makes the remaining time viscerally readable at a glance — you see both the number and the shape of time left simultaneously. The ring is drawn with a single `<circle>` whose `stroke-dashoffset` is updated every second, giving a smooth, analogue-clock feel without any canvas or animation library overhead.

2. **Dark editorial theme with warm amber accent.** The timer needs to fade into the background during focus work — harsh whites fight your eyes. A near-black surface (`#0e0d0b`) with warm amber (`#e8a857`) for the active state signals "you're in focus mode" without being clinical. The green (`#7ec8a0`) reserved for break mode creates an immediate, colour-coded emotional signal: amber = work, green = rest.

---

## 3. Responsive & Accessibility

**Responsive behaviour:**
- At 360px the ring scales down via a CSS clamp/viewport unit on font-size and a media-query that reduces ring `width`/`height` to 240px. Padding shrinks to 14px. The config row stays in one line using `flex: 1` on each item. Nothing wraps awkwardly.
- At 1440px the layout stays centred at `max-width: 480px` — a timer is a focused tool, not a dashboard; stretching it wider adds no value.

**Accessibility handled:**
- Full keyboard navigation: every interactive element is reachable via Tab; focus rings are clearly visible (2px amber outline). The Space bar toggles the timer when focus isn't on a button/input.
- `aria-live="polite"` on the ring wrapper announces time changes to screen readers without interrupting speech.
- Mode pills use `role="tablist"` / `role="tab"` with `aria-selected`. Control group has `role="group"` with an `aria-label`. History section has `role="list"` / `role="listitem"`.
- Colour contrast: the amber `#e8a857` on `#0e0d0b` exceeds WCAG AA (4.7:1 ratio). Muted text `#6b6456` is used only for decorative/secondary labels, never for primary content.

**One thing knowingly skipped:** The config `<input type="number">` fields don't announce their updated value to screen readers on `±` button press (would need `aria-live` on the value). Skipped to avoid chatty announcements during rapid clicking; the visual feedback is instant.

---

## 4. AI Usage

Claude was used to:
- Generate the initial HTML structure and CSS layout.

**What I changed:** The AI initially gave the ring a `transition: stroke-dashoffset 0.3s ease` which caused a visible jump when the timer reset (the offset snapped from max back to 0 while the transition was still playing). I switched to `1s linear` for the in-progress countdown and removed the transition entirely during resets by toggling a CSS class that sets `transition: none` before programmatically updating the offset, then re-adds the transition on the next frame.

The AI also suggested `setInterval` with 1000ms for the countdown, which drifts over long sessions. I kept the interval-based approach (sufficient for a Pomodoro's ~25-minute window where drift is under 1 second) but noted this as a known limitation (see Q5).

---

## 5. Honest Gap

**The timer drifts slightly over long sessions.** `setInterval(tick, 1000)` fires ~1000ms apart, but the actual delay varies due to browser throttling (especially if the tab is backgrounded) and JS event-loop congestion. Over a 25-minute session this can accumulate a few seconds of drift.

**What I'd do with another day:** Replace the interval-based countdown with a wall-clock-anchored approach: record `startTimestamp = Date.now()` when the timer starts and each tick, compute `remaining = totalSecs - Math.floor((Date.now() - startTimestamp) / 1000)`. The interval then just triggers UI repaints; the authoritative time source is the system clock. This also handles tab-backgrounding correctly (the Page Visibility API can fire a catch-up tick when the tab regains focus).
