# Chakra Motion — Bullock Cart Wheel Kinetics

An interactive, single-file web simulation of a traditional Indian bullock cart wheel (*chakra*). You can spin it by hand, drive it at a chosen speed, watch live physics readouts, hear it creak and rumble, and reproduce the famous "wagon-wheel" film illusion in which spokes appear frozen or spin backward.

The whole project is one HTML file with no build step and no required internet connection.

**File:** `chakra_motion_bullock_cart_wheel_kinetics.html` (about 499 KB, including the embedded photo wheel)

---

## Credits

| Role | Who |
|---|---|
| Original idea and direction | **Namdev** |
| First prototype | **Google Gemini (Aragon 4)** |
| Fixes, rewrite and extra features | **Claude Sonnet 5.5** (Anthropic) |

### What each stage contributed

1. **Original idea (Namdev):** a bullock cart wheel with 12 spokes that spins slowly by default, can be flicked by hand, shows real physical numbers, and demonstrates the wagon-wheel optical illusion.
2. **First prototype (Google Gemini, Aragon 4):** the original page, with the canvas-drawn rustic wheel, drag-to-spin, RPM controls, strobe illusion, telemetry, procedural audio code, and a Tailwind CSS plus Lucide icons interface.
3. **Edits (Claude Sonnet 5.5):**
   - Fixed the audio button, which had no click handler, so audio could never turn on.
   - Rebuilt the page so it needs no external scripts. The original loaded Tailwind CSS and Lucide from the internet, and when those were blocked the page showed only plain text.
   - Added a volume control, a speed graph, keyboard shortcuts, proportional friction, motion blur, a slider that follows real speed during braking, and a clearer illusion panel.
   - Added a second built-in default wheel (the photo wheel) and a switch between the two.

---

## Features at a glance

- Spin the wheel by grabbing and flicking it with a mouse or finger.
- Drive it at a target speed from 0 to 150 RPM, clockwise or anticlockwise.
- Realistic heavy-timber inertia and free-spin friction.
- Live telemetry: RPM, linear cart speed, angular velocity, spoke-pass frequency, total revolutions, distance travelled.
- A 10-second speed graph.
- Wagon-wheel illusion with an adjustable camera shutter rate and a numeric explanation.
- Procedural sound (no audio files): rolling rumble plus a wooden creak every time a spoke passes.
- Two built-in default wheels, plus a temporary custom PNG option.
- Spoke #1 ribbon marker to follow a single spoke.
- Soft ground shadow and dust particles.
- Motion blur at high speed.
- Light and dark themes that follow the device setting.
- Keyboard shortcuts.
- Works on desktop and phone widths.

---

## Quick start

1. Download `chakra_motion_bullock_cart_wheel_kinetics.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari). Double-clicking is enough.
3. Grab the wheel and flick it, or use the controls on the left.
4. Click **Audio muted** to switch sound on. Browsers only allow sound after a click, so it starts muted.

Nothing needs installing. The only optional download is the web fonts (see [Fonts](#fonts)).

---

## Controls

### Rotation and drive

| Control | What it does |
|---|---|
| Rotation direction | Clockwise or anticlockwise. |
| Target speed slider | 0 to 150 RPM in steps of 0.5. Default is 2 RPM. |
| Presets | Default low (2 RPM), Oxen walk (25 RPM), Village trot (60 RPM), Strobe sync (120 RPM). |
| Heavy timber inertia | On: the wheel eases toward the target speed. Off: speed changes instantly. |
| Free-spin friction | On: a flicked wheel slows down and settles at 2 RPM. Off: the flicked speed becomes the new target. |
| Spoke #1 ribbon | Draws a crimson marker on one spoke so you can follow its true angle. |
| Brake | Sets the target to 0. The slider follows the real, slowing speed. |
| Default speed (2 RPM) | Returns to clockwise at 2 RPM. |

### Wagon-wheel illusion

| Control | What it does |
|---|---|
| Toggle | Turns the camera-shutter simulation on or off. |
| Camera shutter rate | 10 to 60 FPS, default 24 (cinema). |
| Real turn per frame | How far the wheel really turns between two camera frames. |
| Apparent shift | That turn wrapped into ±15°, which is what the eye perceives. |
| What you see | Frozen spokes, spinning forward, spinning backward, or wheel stopped. |

### Wheel selection

| Control | What it does |
|---|---|
| Wheel 1 · Painted | The canvas-drawn rustic wheel. |
| Wheel 2 · Photo | A photographed wooden wagon wheel, embedded in the file. |
| Wheel image menu → Try a custom PNG | Loads your own PNG, WebP or JPEG for this session only. |
| Wheel image menu → Back to default wheel | Removes the custom image and returns to the selected default. |

### Audio

| Control | What it does |
|---|---|
| Audio button | Turns sound on or off. |
| Volume slider | 0 to 100 percent, default 60. |

### Keyboard shortcuts

| Key | Action |
|---|---|
| Space | Brake. Press again at 0 RPM to resume at 2 RPM. |
| ← / → | Anticlockwise / clockwise. |
| ↑ / ↓ | Target speed plus or minus 5 RPM (limited to 0–150). |
| M | Sound on or off. |
| R | Default speed (clockwise, 2 RPM). |
| W | Switch between the two default wheels. |

Arrow keys are ignored while a slider has keyboard focus, so they can still move that slider. Space is ignored when a button or input has focus.

---

## Physics and formulas

All numbers shown in the interface come from these definitions.

| Quantity | Value or formula |
|---|---|
| Wheel diameter | 1.2 m |
| Circumference | π × 1.2 ≈ 3.77 m |
| Number of spokes | 12 |
| Spoke pitch | 360° / 12 = 30° |
| Degrees per second | RPM × 6 |
| Angular velocity ω | RPM × 2π / 60 (rad/s) |
| Linear cart speed | RPM × circumference × 60 / 1000 (km/h) |
| Spoke-pass frequency | RPM × 12 / 60 (Hz) |
| Revolutions | accumulated from the absolute angle turned each frame, divided by 360 |
| Distance | revolutions × circumference (m) |

At the default 2 RPM, the cart speed is about 0.45 km/h and a spoke passes every 2.5 seconds (0.40 Hz).

### Inertia

With inertia on, each frame moves the current speed toward the desired speed:

```
v += (desired − v) × min(1, dt × 2.2)
```

`dt` is the frame time in seconds, capped at 0.1 s so a slow frame or a returning tab cannot cause a jump. With inertia off, `v = desired` immediately.

### Free-spin friction

After a flick above 3 RPM with friction on, the wheel slows toward ±2 RPM (in its direction of travel) using dry friction plus a drag that grows with speed:

```
decel = (1.5 + 0.35 × |v − base|) × dt        (RPM per frame step)
```

where `base` is ±2 RPM. When the remaining difference is smaller than one step, the wheel settles at the base speed, the target returns to 2 RPM, and the slider and preset highlight update. A hard flick therefore takes several seconds to settle, and a gentle one settles quickly.

### Hand flick

- Pointer angle is measured around the centre of the wheel canvas.
- Each pointer move adds the angle change (wrapped to −180°…+180°) directly to the wheel angle.
- Speed in RPM is estimated as `(deg / dt) / 6` for each move, and the **last 5 samples** are averaged on release.
- The result is clamped to ±150 RPM.
- If the flick is above 3 RPM, the direction buttons follow the flick direction.
- With friction on, the wheel enters free-spin decay. With friction off, the target becomes the flick speed rounded to the nearest 0.5 RPM.

### Wagon-wheel illusion

A camera that records at `fps` frames per second sees the wheel turn `degPerFrame = RPM × 6 / fps` degrees between frames. Because the 12 spokes look identical, only the remainder modulo 30° matters:

```
shift = ((degPerFrame mod 30) + 30) mod 30      → wrapped into (−15°, +15°]
```

| Condition | Shown as |
|---|---|
| \|RPM\| < 0.5 | Wheel stopped |
| \|shift\| < 1.2° | Frozen spokes |
| shift × RPM < 0 | Spinning backward |
| otherwise | Spinning forward |

While the illusion is on, the displayed angle is only refreshed every `1000 / fps` milliseconds, so the screen behaves like a sampled camera. The true angle keeps updating in the background for telemetry and sound.

Example: at 24 FPS, a wheel turning at 120 RPM turns 30° per frame, which equals exactly one spoke pitch, so the spokes look frozen.

---

## Rendering

### The two wheels

- **Wheel 1 (painted):** drawn once on a 900 × 900 canvas with the Canvas 2D API. It includes 12 tapered oak spokes with turned collars, grain lines and knots; a six-segment felloe (wooden rim) with seams; a pitted wrought-iron tyre; 24 domed iron rivets; a hub with radial seasoning cracks, an iron ring with 8 rivets, and a dark axle bore.
- **Wheel 2 (photo):** a photograph with a transparent background, stored inside the HTML as a base64 WebP data URI (about 900 × 882 px, quality 90, roughly 320 KB). It is decoded once at load.

Rotation is done by applying a CSS `transform: rotate(...)` to the canvas, so the wheel is not redrawn every frame. Only the ribbon toggle and wheel changes trigger a redraw.

### Ground shadow and dust

A second canvas (540 × 180) sits at the bottom of the wheel. It draws a soft elliptical contact shadow that pulses slightly with speed. Above 3 RPM, small earth-coloured dust particles spawn at the contact point, thrown opposite to the direction of travel, with more particles at higher speed. Each particle has its own life, drift, gravity and fade.

### Motion blur

Above 60 RPM the wheel gets a CSS blur of `min(2.2, (|RPM| − 60) / 40)` pixels. It is switched off while the camera illusion is active, because blur would hide the sampling effect.

### Speed graph

A canvas chart shows the **signed** speed (so direction is visible) for the last 10 seconds, using 100 samples at 10 samples per second. The scale is ±10 RPM at minimum and grows in steps of 10 RPM. It shows gridlines, a zero line, a filled area and an end-point dot, and redraws on resize and when the system theme changes.

### Theme and layout

- All colours are CSS custom properties. Light values are on `:root`. Dark values apply through `prefers-color-scheme: dark`, and a `data-theme` attribute on the root element can force light or dark.
- Desktop (1024 px and wider): three columns (controls, wheel, telemetry), with the wheel column sticky.
- Narrower screens: one column, with the wheel first, then controls, then telemetry.
- Side gutters are at least 16 px and respect phone safe-area insets. The page does not scroll sideways at 400 px width.

---

## Audio engine

All sound is generated live with the Web Audio API. There are no audio files.

| Part | Details |
|---|---|
| Master gain | One gain node controlled by the volume slider (default 0.6). |
| Rolling rumble | A 2-second looping noise buffer (smoothed, brown-style noise) through a low-pass filter. Filter cutoff is `60 + min(RPM × 2.2, 260)` Hz. Gain is `min(0.28, RPM / 120 × 0.25)`. Fades to silence below 0.5 RPM. |
| Spoke creak | Fires each time the wheel passes a 30° boundary. Below 15 RPM: sawtooth, 110–145 Hz, band-pass near 320 Hz, about 0.11 s. At 15 RPM and above: triangle, `180 + min(RPM, 120)` Hz, band-pass near 520 Hz, about 0.045 s. Band-pass Q is 3.5. Volume is `min(0.18, 0.05 + RPM / 150 × 0.12)`. |
| Throttle | At most one creak every 35 ms, so very fast spins do not create hundreds of audio nodes. |
| Start rule | The audio context is created inside the button click, which browsers require. A short confirmation clack plays when audio is switched on. |
| Tab hidden | The audio context is suspended when the tab is hidden and resumed when it returns, if audio is on. |

---

## Data and storage

| Item | Behaviour |
|---|---|
| Selected default wheel | Saved in the browser's `localStorage` under the key `chakra_motion_default_wheel_v3` (`drawn` or `photo`). If storage is blocked, the page still works and starts on Wheel 1. |
| Custom PNG | Kept only in memory for the current session. It is not saved. |
| Everything else | Not saved. Speed, direction and odometer reset on reload. |

No data is sent anywhere. The page makes no network requests except the optional font download.

---

## Fonts

The page asks Google Fonts for **Inter** (interface), **Playfair Display** (title) and **JetBrains Mono** (numbers). If the fonts cannot load, it falls back to system fonts (`system-ui`, Georgia, and a system monospace) and works the same. Icons are inline SVG, so no icon library is needed.

---

## Project structure (inside the single file)

| Section | Content |
|---|---|
| `<style>` | Colour tokens (light and dark), layout grid, cards, switches, sliders, stage, graph. |
| HTML body | Header (Wheel image menu, audio group), controls column, wheel stage, telemetry column. |
| 0. Inline icons | SVG path table and helper functions. |
| 1. State and parameters | Constants (diameter, circumference, spoke count, storage key, embedded photo data) and the `state` object. |
| 2. Wheel renderer | Canvas drawing of the painted wheel and the ribbon marker, then default-wheel loading and switching. |
| 3. Ground shadow and dust | Second canvas renderer. |
| 4. Web Audio | Engine, creak, rumble, audio button and volume. |
| 5. Drag-to-spin | Pointer events and flick estimation. |
| 6. Controls | Direction, speed, presets, toggles, wheel menu, custom PNG, keyboard. |
| 7. Speed graph | History buffer and chart drawing. |
| 8. Main loop | `requestAnimationFrame` loop that updates physics, sound, rendering and readouts. |

---

## Browser support and requirements

- Any current browser with Canvas 2D, Web Audio, Pointer Events, WebP and CSS `color-mix()`. This covers current versions of Chrome, Edge, Firefox and Safari.
- Touch screens work. The wheel area uses `touch-action: none`, so dragging it does not scroll the page.
- Sound needs one click or tap to start, as all browsers require.

---

## Known limitations

- The Spoke #1 ribbon is placed for Wheel 1. On the photo wheel, it may not sit exactly on a spoke.
- The photo wheel's hub is very slightly off the exact centre of its image, so a faint wobble can be visible at high speed.
- Motion blur is applied to the whole wheel, not per spoke.
- A custom PNG is not remembered after reload.
- The page does not use the device's real frame rate for the illusion. It samples the angle on its own timer, so the effect depends on the display refreshing faster than the chosen shutter rate.

---

## Ideas for the future

- Tune the ribbon position per wheel.
- A second wheel on the same axle to show the cart's linear motion and turning.
- Adjustable wheel diameter for telemetry.
- Save a favourite speed and direction between visits.
- Record the illusion as a short clip.

---

## Licence

No licence has been chosen yet. Add one (for example MIT) before sharing the project publicly.
