# Teh Hijau — Project Notes

A single-page animated "shopfront" art piece, made as a personal gift for **Nurul Athirah** by **Ariffin** (signature in the corner). It plays a ~29s song clip ("Teh Hijau") with synced Indonesian lyrics while a pixel-style night scene of a café ("Tiramisu Cafe") cycles through the four seasons.

## Files

| File | Purpose |
|---|---|
| `teh-hijau-shopfront-v2.html` | **Current version** (v1 + improvements, see "v2 changes" below) |
| `teh-hijau-shopfront.html` | Original v1, kept untouched. The entire project: HTML + CSS + inline SVG + JS (~490 lines, 22 KB) |
| `gsap.min.js` | Local GSAP 3.12.5 copy; loaded only if the CDN fails (v2) |
| `teh-hijau.mp3` | Song clip (~705 KB), loaded by `<audio src="teh-hijau.mp3">`. Third-party audio, **not** covered by the MIT license |
| `README.md` | Public-facing readme (run instructions, features, credits) |
| `LICENSE` | MIT, © 2026 Ariffin (code and artwork only) |

No build step, no package manager. Published publicly on GitHub (account `zetazuni`, repo `teh-hijau`, branch `main`). Open the HTML in a browser to run it (click the start screen to begin, since browsers block autoplay).

## External dependencies (CDN, needs internet)

- **GSAP 3.12.5** (cdnjs) — all animation
- **Google Fonts**: Caveat (handwritten: titles, sign, name), Quicksand (lyrics/body), Space Mono (buttons, season tag)

## Structure of the HTML

Layers, back to front:
1. `#bg-stars` — page-wide CSS twinkling stars (DOM `span`s)
2. `#bg-name-fireflies` — "Nurul Athirah" text that fades in/out at random positions (3 instances)
3. `#bg-glow` — soft warm radial glow behind the stage
4. `#scene` (flex column, centered) containing:
   - `#start-screen` — dedication + title, click to start (z-index 100)
   - `#stage-wrap > #stage` — 8:5 frame holding the SVG (`viewBox="0 0 480 300"`, `crispEdges` for the pixel look), plus `.season-tag` (top-left) and `.signature` (bottom-right)
   - `#lyric-card` — current lyric line
   - `#controls` — pause/play and restart buttons (hidden until start)
   - `<audio id="song">`

### SVG scene (draw order)
sky (`sky-back`, `sky-front`) → `stars-layer` + `shooting-star` (clipped to sky) → `tree-left` / `tree-right` (canopies `canopy-l1/l2/r1/r2`) → `season-particles` → ground (`ground-rect`) → building wall, roof strip, window light spill → `fairy-lights` → door + curtain → window (interior, counter, barista silhouette, `pendant-bulb`, shelf plant) → hanging sign "Tiramisu Cafe" → bench + cat (`cat-group`, `cat-tail`) → potted plant → `fireflies-layer` (in front of everything).

## Structure of the JS

All inline at the bottom of the file.

- **Config constants**: `lyrics` (array of `{time, text}` in seconds), `DURATION = 29` (loop point), `SEASON_INTERVAL = 5000` ms.
- **Season palettes**: `skyBack`, `skyFront`, `canopy`, `ground` — objects keyed by `spring/summer/autumn/winter`. Sky is always night-toned; seasons only shift hue.
- **`particleConfig`**: per-season falling/drifting square particles (petals, fireflies-ish, leaves, snow).
- **Builders** (called once): `buildBackgroundStars` and `buildNameFireflies` run on page load; `buildStars`, `buildFireflies`, `buildFairyLights`, `idleAmbience`, `scheduleShootingStar` run on start click.
- **`applySeason(season)`** — recolors SVG parts by `setAttribute('fill', …)`, dims trees in winter, respawns particles. **`tickSeason`** advances it on a 5s `setInterval`.
- **Lyrics**: `syncLyrics()` runs on the audio `timeupdate` event, finds the latest lyric whose `time <= currentTime`, and `showLine()` fades it in with a blur→sharp GSAP tween. When `currentTime >= DURATION` the audio manually loops back to 0.
- **Start handler**: plays audio, fades out start screen, fades in controls, starts everything.
- Respects `prefers-reduced-motion` (fewer stars, no looping tweens, static name).

## Conventions & style

- Palette: dark brown `#241812`, cream `#f5ebd8`, lamp amber `#f0b86e`; scene uses warm browns/greens with `#3a281c` as outline color, 2–3px strokes.
- Pixel/crisp aesthetic: keep `shape-rendering: crispEdges`, use rects and simple shapes, no gradients in the scene except the `warm-glow` radial gradient.
- Animation: GSAP with `sine.inOut`, `repeat:-1, yoyo:true` for ambient loops; random delays to desynchronize.
- Comments are section-banner style (`/* ---------- Title ---------- */`); match that.
- UI language is mixed: English UI text, Indonesian lyrics (`lang="id"`).

## Known quirks / gotchas

- Filename case: the mp3 was renamed from `.MP3` to lowercase `.mp3` to match the HTML `src` (case-sensitive hosts need this).
- `audio.play()` is called without handling the returned promise (no `.catch`); fine after a click, but errors are silent if the mp3 is missing.
- Seasons rotate on a wall-clock timer (5s), **not** tied to the audio; pausing the song does not pause the seasons, and restart does not reset them. The 29s loop ≈ 5.8 season steps, so seasons drift relative to lyrics.
- `DURATION` (29) is hardcoded; if the mp3 is swapped, update it and the `lyrics` timings together.
- `#bg-name-fireflies` text uses `left/top` percentages, so it can clip at the viewport edge.
- The SVG sign text ("Tiramisu Cafe") relies on the Google Font loading; falls back to generic cursive offline.
- The page is `overflow:hidden` and sized to viewport, so it isn't scrollable on very small screens; stage width is `min(92vw, 820px)`.
- Title/heading says "Teh Hijau" but the shop sign says "Tiramisu Cafe" — apparently intentional, but worth confirming before changing.

## Ideas for future development

- Add `.catch` on `audio.play()`.
- Tie seasons to lyric lines or song progress instead of a fixed timer; pause/reset with the audio.
- Add a progress bar or mute/volume control.
- Make the barista/cat interactive (click the cat, etc.), or add a day/night toggle.
- Split into `index.html`, `style.css`, `script.js` if it grows; host on GitHub Pages/Netlify as a shareable link.
- Add a dedicated `lyrics.json`/config block at the top so timings and name/dedication text are easy to edit.

## v2 changes (in `teh-hijau-shopfront-v2.html`)

- **`CONFIG` block** at the top of the script: title, recipient, dedication, signature, shop name, end message. `applyConfig()` writes them into the DOM. Lyrics/`DURATION` sit right below it.
- **Seasons follow the song**: `syncSeason()` picks the season from `audio.currentTime` (4 equal quarters of `DURATION`). No timer.
- **Audio errors**: `play()` promises handled; error shown on the start screen (or as a lyric-line message once started).
- **Controls**: pause/play, restart, mute, day/night, plus a click-to-seek progress bar along the bottom of the stage.
- **Day/night**: `palettes.night` / `palettes.day` per season, `setMode()` fades stars, shooting stars, fireflies and fairy lights and dims the window light spill.
- **Fun extras**: clickable cat (hop + random meow text), barista idle bob/nod (`#barista-body`, `#barista-head`), ending card that shows on each loop (`showEndCard`).
- **Robustness**: local GSAP fallback, font fallback stacks, viewport-clamped name fireflies, stage size clamped to viewport height, small-screen media query, `description`/Open Graph meta tags (no `og:image` yet; needs a hosted URL).
- **Not verified in a real browser**: only a syntax check and headless load; day/night visuals, end card timing and mobile layout deserve a manual look.
- Still not done: splitting into separate html/css/js files; og:image.

Note: the "Known quirks" list above describes v1; season-timer and `play()` issues are fixed in v2.

## Repo & licensing notes

- Public GitHub repo, MIT-licensed. The README states that the mp3 and on-screen lyrics belong to their original rights holders and are excluded from the MIT grant. Keep that wording if the README is edited, and remove/replace the audio if a rights holder objects.
- GSAP (`gsap.min.js`) keeps its own GreenSock license; fonts are Google Fonts (OFL).
- Workflow: edit `teh-hijau-shopfront-v2.html`, keep v1 untouched, update this file and the README when behaviour changes, then commit and push to `main` (the owner's own repo; push only when asked).
