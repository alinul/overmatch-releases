# Overmatch

A free OW2 overlay that recommends counter-picks based on the enemy team.

[![Latest Release](https://img.shields.io/github/v/release/alinul/overmatch-releases?label=Download&color=E0A000)](https://github.com/alinul/overmatch-releases/releases/latest)
[![Ko-fi](https://img.shields.io/badge/support-Ko--fi-FF5E5B?logo=kofi&logoColor=white)](https://ko-fi.com/alinul)

> Screenshot / GIF coming soon

## What it does

- Reads the scoreboard when you hold Tab in OW2
- Detects all 5 enemy heroes via image matching against in-game portraits
- Recommends the top 3 counter picks per role — Tank, Damage, Support — with arrow strength indicators
- Shows in real-time which slots were detected cleanly (green border) vs missed (red border)

Counter data is synthesized from `rankedboost.com`, `dotesports`, `esports.gg`, `charlieintel`, `gamerant`, `1v9.gg`, and pro/coach guides. Covers all 51 current OW2 heroes (May 2026 roster).

## Install

1. Download the latest [`Overmatch-X.Y.Z.zip`](https://github.com/alinul/overmatch-releases/releases/latest)
2. Extract anywhere (Documents, Downloads, wherever)
3. Run `Overmatch.exe`

No installer, no admin rights, no .NET install needed — fully self-contained Windows binary.

## Use

The overlay starts hidden. Press **F2** to toggle it visible. While visible, it auto-refreshes every 600 ms; opening the scoreboard with all 5 enemies alive triggers the matching pipeline.

| Hotkey | Action |
|---|---|
| `F2` | Toggle overlay |
| `F3` | Open the Ko-fi support page |
| `Ctrl+Shift+F2` | Quit Overmatch |

`F1` is intentionally not used — it's OW2's "Hero Help" key.

## Requirements

- **Windows 10 or 11**
- **OW2 in Borderless Windowed** display mode (Settings → Video → Display Mode). Exclusive Fullscreen prevents screen capture.
- Tested at 1920×1080 and 2560×1440. Other resolutions should work since detection scales proportionally; open an issue if not.

## Privacy & anti-cheat

Overmatch reads pixels from the screen via standard Windows APIs (`BitBlt` / `Graphics.CopyFromScreen`). It does **not**:

- Read OW2's process memory
- Inject DLLs into the game
- Modify game files
- Send game data to any server

This is the same approach OBS Game Capture, the Discord overlay, and similar tools use. Defense Matrix detects memory tampering and code injection, not screen reading. No reported anti-cheat issues, but use at your own risk — Blizzard's policies can change.

## How detection works

When the overlay is visible:

1. Captures the OW2 window every 600 ms via screen-capture API
2. Checks if the SCOREBOARD tab is highlighted (skips if not — no wasted matching)
3. Locates the red enemy panel by scanning for its left edge
4. Crops the 5 enemy portrait slots
5. For each slot:
   - Skips if it's a pre-pick "?" silhouette (all slots look the same → silhouette state)
   - Skips if it's a death/respawn countdown overlay (dark uniform circle in the center)
   - Otherwise runs OpenCV template matching against all 51 hero portraits at multiple scales, picks best match
6. **Suggestions only refresh on a complete 5/5 detection** — partial reads (4 alive, 1 dead) keep the previous suggestions stable instead of thrashing

Detection accuracy is around 90% on a clean scoreboard. The ENEMIES row in the overlay shows you which slots matched (green) and which didn't (red) so you can see when to trust the picks.

## Auto-update

Overmatch checks this repo's releases on every launch. When a new version is published you'll get a one-click "Install Now" prompt — download → file swap → restart, no manual reinstall needed.

## Support development

Overmatch is free because there are decent free alternatives and gatekeeping a counter-pick tool felt wrong. But it's not maintenance-free — every patch shifts the meta, every new hero needs data + portrait + counter research, every Blizzard UI tweak can break detection.

If Overmatch helps you climb, consider [buying a coffee on Ko-fi](https://ko-fi.com/alinul). Donations buy time to keep this current.

## Issues, bug reports, hero requests

Open an issue on this repo. Include:

- Your screen resolution
- A screenshot of what you saw vs what you expected
- The contents of the `debug/` folder next to `Overmatch.exe` if it's a detection issue

## Credits

- Hero portraits via [Liquipedia](https://liquipedia.net/overwatch/) — © Blizzard, used as reference templates only
- Counter data synthesized from community guides (see "What it does")
- Built in C# / WPF on .NET 8 with [OpenCvSharp](https://github.com/shimat/opencvsharp) for image matching
