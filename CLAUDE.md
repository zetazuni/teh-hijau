# Teh Hijau — Project Notes

A single-page animated "shopfront" art piece, made as a personal gift for **Nurul Athirah** by **Ariffin** (signature in the corner). It plays a ~29s song clip ("Teh Hijau") with synced Indonesian lyrics while a pixel-style scene of a café ("Tiramisu Cafe") moves through the four seasons in step with the song.

- Live: https://tehhijau.netlify.app
- Repo: https://github.com/zetazuni/teh-hijau (public, MIT, branch `main`)
- **Work on `teh-hijau-shopfront-v2.html`.** `teh-hijau-shopfront.html` is the original v1, kept untouched for reference.

## Files

| File | Purpose |
|---|---|
| `teh-hijau-shopfront-v2.html` | **Current version.** All HTML + CSS + inline SVG + JS (~30 KB) |
| `teh-hijau-shopfront.html` | Original v1 (22 KB). Do not edit |
| `teh-hijau.mp3` | Song clip (~705 KB), loaded by `<audio src="teh-hijau.mp3">`. Third-party audio, **not** covered by MIT |
| `gsap.min.js` | Local GSAP 3.12.5 copy; v2 loads it only if the CDN fails |
| `netlify.toml` | Publish dir `.`, rewrites `/` to `teh-hijau-shopfront-v2.html` |
| `README.md` | Public readme (run, features, customise, credits/licences) |
| `LICENSE` | MIT, © 2026 Ariffin (code and artwork only) |
| `.gitignore` | Ignores `.netlify/` (local Netlify link state) |

No build step, no package manager. Open the HTML in a browser (click the start screen to begin; browsers block autoplay). The mp3 must sit next to the HTML.

## External dependencies

- **GSAP 3.12.5** from cdnjs, with `gsap.min.js` as a `document.write` fallback
- **Google Fonts**: Caveat (handwriting: titles, sign, name, end card), Quicksand (lyrics/body), Space Mono (buttons, season tag). Fallback stacks exist but the fonts are not bundled

## Page structure (v2)

Layers, back to front:
1. `#bg-stars` — page-wide twinkling stars (DOM `span`s)
2. `#bg-name-fireflies` — the recipient's name fading in/out at random spots (3 instances, kept inside the viewport)
3. `#bg-glow` — warm radial glow behind the stage
4. `#scene` (flex column, centered):
   - `#start-screen` — dedication + title + `#audio-error`; click to start (z-index 100)
   - `#stage-wrap > #stage` — 8:5 frame (width clamped to viewport width and height) holding the SVG (`viewBox="0 0 480 300"`, `crispEdges`), plus `.season-tag`, `.signature`, `#progress` (click-to-seek bar) and `#end-card`
   - `#lyric-card` — current lyric line
   - `#controls` — pause/play, restart, mute, day/night (hidden until start; wraps on small screens)
   - `<audio id="song">`

### SVG scene (draw order)
sky (`sky-back`, `sky-front`) → `stars-layer` + `shooting-star-layer` (clipped to sky) → `tree-left` / `tree-right` (canopies `canopy-l1/l2/r1/r2`) → `season-particles` → ground (`ground-rect`) → building wall, roof strip, `light-spill` → `fairy-lights` → door + curtain → window (interior, counter, `barista-body` > `barista-head`, `pendant-bulb`, shelf plant) → hanging sign (`shop-sign` text) → bench + cat (`cat-group`, `cat-tail`) → potted plant → `fireflies-layer` (in front).

## JS structure (inline at the bottom of the file)

- **`CONFIG`** (top of script): `title`, `recipient`, `dedication`, `signature`, `shopName`, `endMessage`. `applyConfig()` writes them into the DOM (start screen, signature, sign text, end card, `document.title`, floating name). Shop name must stay short: the sign is 116 SVG units wide.
- **`lyrics`** (`{time, text}` in seconds), **`DURATION = 29`** (loop point), **`SEASON_LENGTH = DURATION / 4`**. If the mp3 is swapped, update `lyrics` and `DURATION` together.
- **`palettes.night` / `palettes.day`**: `skyBack`, `skyFront`, `canopy`, `ground`, each keyed by `spring/summer/autumn/winter`. `mode` holds the current one.
- **`particleConfig`** / `spawnSeasonParticles`: per-season falling/drifting squares.
- **`applySeason(season)`** recolours the SVG from `palettes[mode]`; **`syncSeason()`** picks the season from `audio.currentTime` (4 equal quarters); **`setMode()`** fades stars/shooting stars/fireflies/fairy lights and dims `light-spill` for day.
- **`timeupdate` handler**: loops at `DURATION` (and calls `showEndCard()`), then `syncLyrics()`, `syncSeason()`, updates the progress fill.
- **Start flow**: click → `audio.play().then(begin).catch(showAudioError)`. `begin()` fades out the start screen, fades in controls + progress, builds stars/fireflies/fairy lights, starts idle animation and shooting stars. `showAudioError()` writes to the start screen, or to the lyric line once started.
- **Extras**: `idleAmbience()` (pendant flicker, cat tail, barista bob/nod), cat click → hop + random meow text, mute, day/night, click-to-seek, ending card.
- `prefers-reduced-motion` is respected (fewer stars, no looping tweens, static name, no end card/hop).

## Conventions & style

- Palette: dark brown `#241812`, cream `#f5ebd8`, lamp amber `#f0b86e`; scene uses warm browns/greens with `#3a281c` outline, 2–3px strokes.
- Pixel/crisp aesthetic: keep `shape-rendering: crispEdges`, simple rects/shapes, no gradients in the scene except `warm-glow`.
- GSAP with `sine.inOut`, `repeat:-1, yoyo:true` for ambient loops; random delays to desynchronise. GSAP overwrites an element's `transform` attribute, so animate inner groups (as done for the barista), not ones that already carry a `transform`.
- Section-banner comments (`/* ---------- Title ---------- */`); match them.
- UI is English, lyrics are Indonesian (`lang="id"`).

## Known quirks / gotchas

- Filename case matters on Linux hosts: keep `teh-hijau.mp3` lowercase to match the HTML `src`.
- `DURATION` is hardcoded and seasons are equal quarters of it (not aligned to lyric lines).
- Title says "Teh Hijau" but the shop sign says "Tiramisu Cafe" — apparently intentional; confirm before changing.
- The page is `overflow:hidden` and sized to the viewport; the day/night visuals, end-card timing and phone layout were only checked with a syntax check and a headless load, never eyeballed on a real device.
- Headless Chrome screenshots are taken before GSAP fades finish, so they can't verify animation states.

## Ideas / not done

- Split into `index.html`, `style.css`, `script.js` if it grows.
- `og:image` for link previews (needs a hosted image URL; title/description meta already exist).
- Tie seasons to specific lyric lines rather than equal quarters.

## Licensing

- MIT covers code and artwork only. The README says the mp3 and on-screen lyrics belong to their rights holders and are excluded. Keep that wording if the README changes, and remove or replace the audio if a rights holder objects.
- GSAP keeps its own GreenSock licence; fonts are Google Fonts (OFL).

## Deployment (Netlify)

- Site `tehhijau`, id `bf21806f-d3f2-4793-9b11-41c62141ec80`, team Zeta Solutions. The folder is linked via `netlify link` (state in the gitignored `.netlify/`).
- Continuous deploy runs in Netlify's **manual** mode (the GitHub OAuth flow failed): repo `git@github.com:zetazuni/teh-hijau.git`, branch `main`, no build command, publish dir `.`. Pieces: a read-only deploy key on the repo (GitHub key id `164248420`, Netlify key id `6ab44b9c4bc7bfa619f5a913`), a GitHub push webhook (id `684594483`) that POSTs to a Netlify build hook (`https://api.netlify.com/build_hooks/6ab44bce7d37297d4cf1243b`). **A push to `main` goes straight to production.**
- Verified with a real push: commit `002f2e9` produced a published deploy about 30 s later.
- Manual mode gives no GitHub commit statuses or PR deploy previews. Connecting through Netlify's GitHub app in the dashboard would add those and could replace this setup.
- Gotcha: `repo_url` must be the full SSH URL. With a bare `owner/repo` path, builds fail with `fatal: repository 'zetazuni/teh-hijau' does not exist`.
- Everything in the repo root is published (README, CLAUDE.md, v1 included).
- If auto-deploy stops: check the repo's Settings > Webhooks and Deploy keys still exist; trigger a build with `POST` to the build hook URL; or deploy by hand: stage a folder with v2 as `index.html` plus the mp3 and `gsap.min.js`, then `netlify deploy --prod --dir <folder> --site bf21806f-d3f2-4793-9b11-41c62141ec80`.
- Shell notes (Windows PowerShell 5.1): passing JSON to `netlify api ... --data` needs quotes escaped (`'{\"site_id\":\"...\"}'`, or `.Replace('"','\"')`), and values with spaces get split.

## Workflow

Edit `teh-hijau-shopfront-v2.html`, leave v1 alone, update this file and the README when behaviour changes. The repo is the owner's own; commit freely but confirm before pushing to `main`, since a push deploys to the live site. End commit messages with the Claude co-author line.

## History

- v1 (`teh-hijau-shopfront.html`): seasons on a 5 s `setInterval` unrelated to the audio, `audio.play()` unhandled, only pause/restart controls, name text could clip at the viewport edge, mp3 file was named `.MP3` while the HTML asked for `.mp3`.
- v2 fixed all of the above and added: `CONFIG` block, progress bar/seek, mute, day/night toggle, clickable cat, barista idle animation, ending card, local GSAP fallback, font fallbacks, mobile/short-screen layout, description/Open Graph meta tags.
