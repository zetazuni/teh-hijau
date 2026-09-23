# Teh Hijau

A small animated pixel-style tea shop, made as a gift. A night scene of "Tiramisu Cafe" plays out while a short clip of a song plays with synced Indonesian lyrics, and the scenery moves through spring, summer, autumn and winter along with the music.

Single HTML file, no build step.

**Live site:** https://tehhijau.netlify.app

## Run it

1. Clone or download this repo.
2. Open `teh-hijau-shopfront-v2.html` in a browser (internet needed for the fonts; GSAP falls back to the local `gsap.min.js`).
3. Click the start screen to begin (browsers block autoplay until you click).

`teh-hijau.mp3` must sit in the same folder as the HTML file.

## Features (v2)

- Seasons change with the song position, so pause, restart and loop stay in sync
- Synced lyrics, progress bar (click to seek), pause/restart/mute
- Day / night toggle
- Clickable cat, idle-animated barista, twinkling stars, fireflies, shooting stars, falling petals/leaves/snow
- Ending card each time the song loops
- Friendly message if the audio can't be played
- Respects `prefers-reduced-motion`; works on small screens

## Make it your own

Edit the `CONFIG` block at the top of the `<script>` in `teh-hijau-shopfront-v2.html` (title, recipient, dedication, signature, shop name, end message). Lyrics and timings are the `lyrics` array right below it; `DURATION` is the loop length in seconds. If you swap the song, update `lyrics` and `DURATION` together.

## Files

| File | What |
|---|---|
| `teh-hijau-shopfront-v2.html` | Current version |
| `teh-hijau-shopfront.html` | Original v1, kept for reference |
| `teh-hijau.mp3` | Song clip |
| `gsap.min.js` | Local GSAP 3.12.5 fallback |
| `CLAUDE.md` | Developer notes on how the project works |

## Credits and licenses

- Code and artwork: © 2026 Ariffin, released under the [MIT License](LICENSE).
- **The audio is not covered by the MIT license.** `teh-hijau.mp3` and the lyrics shown on screen belong to their original artist and rights holders and are included here only as part of a personal gift project. Replace them with your own audio and lyrics if you reuse or redistribute this project, and if you are a rights holder who wants them removed, please open an issue.
- [GSAP](https://gsap.com) is © GreenSock and used under its own [standard license](https://gsap.com/standard-license/).
- Fonts (Caveat, Quicksand, Space Mono) are from Google Fonts under the SIL Open Font License.
