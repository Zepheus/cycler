# cycler

A self-hosted, single-file **indoor cycling coach** for Bluetooth smart bikes — built for riding while you watch something else, not a virtual world.

It connects to a Bluetooth FTMS bike (developed against a **Schwinn IC4 / "IC Bike"**) and an optional **Polar H10** heart-rate strap, shows live power/cadence targets, tells you the exact resistance-knob setting to hit them, and floats a compact coach panel over your YouTube/film via Picture-in-Picture. No app store, no account, no server — just open an HTML file in Chrome or Edge.

## Why

Apps like Zwift and MyWhoosh want your attention on a screen. If you'd rather watch entertainment and just be told *"hold 240 W, set resistance to 39,"* this fills that gap — a glanceable (and *speakable*) overlay that adapts to your fitness.

## Features

**Connectivity**
- Web Bluetooth to any **FTMS** indoor bike (power, cadence, speed, resistance).
- Optional **Polar H10** (or any BLE strap) for heart rate + HRV.
- Auto-reconnect when the bike sleeps/wakes; resumes where it paused.

**Coaching**
- Structured workouts scaled to your **FTP**, using the standard 7-zone power model.
- Workout types: **Mixed, Endurance, Sweet spot, Threshold, VO2 30/30, VO2 4×4, Over-unders, Pyramid**, plus an **FTP ramp test** that estimates your FTP.
- Adjustable duration with quick presets (15 / 20 / 30 / 45 min).
- Live **power & cadence targets** with up/down arrows; noisy bike data is smoothed (3 s power, 1.5 s cadence/resistance/speed).
- **Resistance prediction** — learns your bike's `power ≈ f(cadence, resistance)` and tells you the exact knob level for each target (per-bike calibration table; IC4 ships pre-calibrated).
- **HRV readiness gate** — a 2½-min resting H10 measurement (lnRMSSD vs your rolling baseline) auto-scales the day's plan green / amber / red, with manual override.

**Hands-free / glanceable**
- **Voice coaching** (Web Speech API): announces the next block + target resistance + a "3-2-1" countdown so you can ride without looking.
- **Picture-in-Picture** overlay floats the coach over any other window.
- **Auto-start** on first pedal and **auto-pause/resume** when you stop.
- Zone-change beeps; **generative, zone-reactive music** (Tone.js) if you want a soundtrack instead of your own.
- Clickable timeline with per-block wattages; screen wake-lock; responsive down to a portrait split-screen.

**Data**
- **Export `.tcx`** of each ride (power/cadence/HR) — upload to Strava (which can forward to Apple Health, etc.).
- Settings, HRV history, and calibration persist in the browser (localStorage).

## Requirements

- **Desktop Chrome or Edge** (Web Bluetooth is required; Safari and iOS are not supported).
- A Bluetooth **FTMS** smart bike.
- *Optional:* a Polar H10 / BLE heart-rate strap.
- *Optional:* internet on first load (the generative music loads Tone.js from a CDN).

## Getting started

1. Open `coach.html` in Chrome or Edge.
   - If buttons don't respond (some setups block Bluetooth from `file://`), serve it locally instead:
     ```sh
     python3 -m http.server 8000
     # then visit http://localhost:8000/coach.html
     ```
2. (Optional) Wear the H10, then **Connect H10**.
3. Pedal a few strokes to wake the bike, then **Connect bike**.
4. Pick a **Type** and **Duration**, **Build plan**, and just start pedaling — it auto-starts.
5. **Pop out (PiP)** to float the coach over your video.

`ble-probe.html` is a small diagnostic that dumps exactly which Bluetooth fields your bike transmits — handy when adding support for a new bike.

## Calibrating another bike

Resistance prediction is keyed to the bike's Bluetooth name in a small table near the top of the resistance section in `coach.html`:

```js
const CALIBRATIONS = [
  { match:/ic ?4|ic ?bike|schwinn|bowflex|c6/i, label:'Schwinn IC4', a:0.0755, b:0.597 },
];
```

Unrecognized bikes simply **learn live** as you ride (vary the resistance across a few levels and it self-calibrates). Once the on-screen model line stabilizes (`P/cadence = a·R + b`), add a row with your bike's `a`/`b` to bake it in.

## Notes & limitations

- The IC4's reported power is an estimate; treat it as **self-consistent** (repeatable for *you* on *your* bike) rather than lab-accurate.
- FTP is the anchor for every target — set it honestly, or run the ramp test.
- This is a personal project, not medical or professional training advice.

## License

MIT — see [LICENSE](LICENSE).
