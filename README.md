# Bar Log

A single-page session logger for the 12-week calisthenics program in [PROGRAM.md](PROGRAM.md).

No backend, no accounts, no build step. Data lives in your browser's `localStorage` and never leaves the device. Works offline, which is the point — the park has no signal.

## What it does that a notes app doesn't

- **One tap logs a set.** Each box is pre-filled with what you did in that set last session, shown dashed. Tap to log it as-is; tap again to type something different. Backpack load carries forward too. Scoped per session — Upper A dips and Upper B dips are prescribed differently, so their histories stay separate.
- **Charts don't lie when you add weight.** Once an exercise has been logged with load, its chart switches from raw reps to estimated 1RM over bodyweight + backpack. Six bodyweight pull-ups becoming four at +10 kg is a rise from 79 to 86 kg, not the cliff a rep count would draw.
- **Applies the double-progression rule.** When you hit the top of a rep range on every set, at the same load, two sessions in a row, the exercise turns amber and tells you to add weight or move to the harder variation.
- **Calls the deload.** Flags when two or more movements have gone backwards three sessions running, or when you've trained 6–8 weeks straight. Starting a deload week halves every set count on the Today screen, same reps, as section 7 asks.
- **Times your rests.** Logging a set starts the prescribed countdown, with a drain bar you can read from arm's length off the bar. Holds a screen wake lock while resting, so the alarm still fires.
- **Tracks the phase gates.** Live progress against the Phase 2 and Phase 3 entry criteria, computed from your best single set. Offers to advance you when you clear all four.
- **Charts bodyweight** against the 0.2–0.3 kg/month target, plus top sets for pull-ups and dips and your rolling sleep average.
- **Shows whether you're actually training.** A week-by-week grid of trained days, missed programmed days and rest days, against the program's 4-day target.
- **Records effort.** Three chips per exercise — easy / 1–2 left / to failure — appearing once you've logged a set. The program asks for 1–2 reps in reserve, and going to failure across a whole session now gets called out.
- **Logs sleep, bodyweight and notes** with the session, not in a separate screen.
- **Bends to the actual session.** Add or drop a set on any exercise when you feel good or the bar is taken.
- **Corrects the record.** Reopen any past session from Progress → Edit; saving replaces it in place and keeps its original date.
- **Shows one movement's whole trajectory.** Tap an exercise name for every session it appears in, across both Upper days and all phases, with loads and a top-set chart.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app — markup, styles, logic |
| `program.json` | The program as data: phases, sessions, exercises, rep ranges, rest times, gates |
| `sw.js` | Offline cache |
| `manifest.json`, `icon*.svg` | Add-to-home-screen metadata |
| `PROGRAM.md` | The human-readable program |

**`program.json` and `PROGRAM.md` are separate copies of the same program.** Editing one does not update the other. `program.json` is what the app reads.

Phases 2 and 3 are written out in full in `program.json`. `PROGRAM.md` describes them as a list of changes to Phase 1, so those session lists are that description applied — worth reading once to check you agree with the interpretation, particularly the leg work.

## Deploy to GitHub Pages

```sh
gh repo create bar-log --public --source=. --push     # or create the repo on github.com and:
# git remote add origin git@github.com:<you>/bar-log.git
# git push -u origin main
```

Then **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**. The URL appears in a minute or two.

On your phone, open the URL and use **Share → Add to Home Screen**. It then launches full-screen and runs with no connection.

## Run it locally

Needs a web server — opening `index.html` as a file won't work, because it fetches `program.json`.

```sh
python3 -m http.server 8000    # then open http://localhost:8000
```

## Changing the program

Edit `program.json`. Each exercise takes:

```json
{
  "id": "pullups",        // stable across phases — this is what links history
  "name": "Pull-ups",
  "sets": 6,
  "type": "reps",         // or "hold" (min/max are then seconds)
  "min": 2, "max": 3,     // the rep range the progression rule watches
  "rest": 180,            // seconds
  "loadable": true,       // shows a +kg field, and enables e1RM charting
  "bwFactor": 1,          // share of bodyweight the movement carries; default 1
  "perSide": false,
  "skill": true,          // adds the Skill tag
  "note": "…"
}
```

Keep `id` stable when a movement carries over between phases, or you'll lose its history and its charts.

## Backup

Data is per-browser and per-device. Clearing site data erases it.

The app asks the browser to mark its storage persistent (granted without a prompt for installed PWAs on most platforms), and nags you on the Today screen once eight sessions have accumulated since your last export. **Data → Export JSON** writes a file; **Import JSON** restores it or moves it to another device. Treat the export as the real backup — the persistence flag is a request, not a guarantee.
