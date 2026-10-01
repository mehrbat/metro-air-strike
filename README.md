# Low Level Strike

A browser game: fly a strike fighter at rooftop height through a procedurally generated city at dusk,
destroy the command center (marked by a red light beam) and survive the air defenses on the way.

Everything is in `index.html`: the city, models, textures, particles and sound are all generated in
code. The only external dependency is Three.js, loaded from the jsDelivr CDN (so an internet
connection is needed on first load).

## Run

Open `index.html` directly in Chrome/Edge/Firefox, or serve the folder:

```bash
python -m http.server 8765
```

then browse to http://localhost:8765. (`.claude/launch.json` has the same config for the Claude
desktop preview pane.)

## Controls

| Key | Action |
|---|---|
| `W` / `S` (or ↑ ↓) | Climb / dive. `I` inverts. |
| `A` / `D` (or ← →) | Bank and turn |
| `Shift` | Afterburner (drains a meter; locks out at empty until 30% recharged) |
| `X` | Air brake: slower and turns tighter |
| `Space` | Fire missile. Homes on whatever is locked, otherwise flies straight. |
| `F` | Flares |
| `C` | Toggle chase / close camera |
| `P` / `Esc` | Pause |
| `M` | Mute |

## How the defenses work

- **Line of sight is real.** Every SAM site, flak gun and missile seeker checks a straight line to
  you against the city's buildings. Fly down a street between tall buildings and nothing can see you
  (the HUD shows `RADAR: MASKED`).
- **SAM sites** need to hold a lock for about 2.4 s (faster on later missions) before they launch.
  Below 45 m, ground clutter roughly halves the lock speed.
- **SAM missiles** are faster than you but turn wider. If a missile loses sight of you for about 1 s
  (for example behind a building), it loses track and flies straight. Each flare has about a 70%
  chance to pull away each missile that is tracking you.
- **Flak guns** (on rooftops and at intersections) fire bursts aimed at where you will be, with
  timed fuses. Holding a steady flight path is what gets you hit.
- **Your missiles** lock on whatever is closest to your nose (within about 20°, 1.5 km, and in line
  of sight), with priority for the command center. Three hits destroy it; one hit, or a near miss
  within 30 m, destroys a SAM site or flak gun.

Each mission adds more SAM sites and flak guns, makes them more accurate, and generates a new city
(the city layout is seeded per mission, so a retry gives the same city).

## Scoring

Command center +1000, SAM site +200, flak gun +100, +12/s while flying below 50 m inside the city,
plus a time bonus and a hull bonus when you complete a mission.
