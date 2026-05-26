# ANSWERS.md

---

## 1. How to Run

Just open `index.html` in a browser. Drag it into Chrome or Firefox and it works — no npm install, no build step, nothing.

If you want a proper local server:
```bash
npx serve .
```
Then go to `http://localhost:3000`.

I kept it as a single HTML file on purpose so there's zero friction running it. No "oh you need to install X first."

---

## 2. Stack & Design Choices

Went with vanilla HTML/CSS/JS. Honestly considered React for like two minutes then thought — it's a timer, why add a build step? The whole thing is state that fits in a dozen variables. Vanilla was the right call.

Two specific decisions I made:

**The ring instead of a bar.** I tried a progress bar first and it just felt like a loading spinner. The circular ring wrapping around the time display does something different — you see the number *and* the shape of how much time is left at the same time. It's more like a clock face. The implementation is one SVG circle with `stroke-dashoffset` updating every second, no library needed.

**Amber for focus, green for break.** I wanted the mode switch to feel immediate without reading any text. Warm amber = you're working, cool green = you're resting. It's the kind of thing you feel before you consciously process it. Also made the dark background non-negotiable — a timer you're staring at during deep work shouldn't be blasting white light at you.

---

## 3. Responsive & Accessibility

On a 360px phone the ring scales down to 240px, padding tightens to 14px, and the config row stays on one line because each side is `flex: 1`. Nothing stacks weirdly or overflows. On a 1440px desktop the layout stays centred at 480px max-width — stretching a timer wider doesn't add anything useful.

Accessibility thing I handled: keyboard navigation throughout. Every button and input is reachable by Tab, focus rings are clearly visible (2px amber outline, not the browser default which disappears on dark backgrounds). The time display has `aria-live="polite"` so screen readers get updates without interrupting. Mode pills are `role="tab"` with `aria-selected`. Space bar toggles the timer when you're not focused on a button.

One thing I knowingly skipped: the `±` config buttons don't announce the new value to screen readers immediately. You'd need an `aria-live` region just for that. I left it out because it would get noisy if someone holds the button down — you'd get a flood of announcements. Tradeoff I was okay with.

---

## 4. AI Usage

Used Claude to help with initial boilerplate and to sanity-check the SVG ring math (circumference calculation for `stroke-dasharray`).

One thing I changed from what it gave me: the ring transition. Claude's first version had `transition: stroke-dashoffset 0.3s ease` on the progress circle. Looked fine while running but when you hit reset, the ring would animate backwards from wherever it was back to full — like watching a progress bar rewind. Fixed it by setting `transition: stroke-dashoffset 1s linear` for the normal countdown feel, and stripping the transition entirely during resets before snapping the offset back programmatically.

Also the original `handleStartPause` function had a bug where `btnLabel.textContent` was being set based on the `running` variable *before* it was updated by `startTimer`/`pauseTimer`. Swapped the order so the label reflects the actual new state.

---

## 5. Honest Gap

The timer drifts. `setInterval` with 1000ms sounds right but browsers throttle background tabs, and the JS event loop isn't perfectly predictable. Over a full 25-minute session you can lose a few seconds.

If I had another day I'd anchor it to the wall clock instead — save `Date.now()` when the user hits start, then each tick calculate `remaining = totalSecs - Math.floor((Date.now() - startTime) / 1000)`. The interval would just trigger repaints; the actual time math would come from the system clock. Also hook into the Page Visibility API so when you switch back to the tab it catches up immediately instead of silently being behind.
