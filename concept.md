Here is a complete **Concept Plan** for a single-file (or modular) **Cyberpunk Esports Bracket & Broadcast Control HTML App** tailored to **Night 4 (November 29, 2026)** for **Call of Duty: Mobile (CODM)** and **Mobile Legends: Bang Bang (MLBB)**.

---

## 1. Project Folder & Asset Mapping

Keep everything self-contained in a single folder so relative paths work reliably on stream PCs, laptops, or local servers:

```text
night4-bracket/
├── index.html          # Self-contained UI, styles, and state logic
└── icons/
    ├── Aurelia.PNG
    ├── Florea.PNG
    ├── FrostveiI.PNG   # Note: exact filename has capital 'I' before '.PNG'
    └── Solaris.PNG

```

### Important Filename & Rendering Notes

* **Exact Filename Match:** Notice `FrostveiI.PNG` uses a capital `I` (or double-check whether your file system has an uppercase `I` vs lowercase `l`). Define a canonical team registry in JavaScript so filenames are mapped in one place:
* `Aurelia` → `./icons/Aurelia.PNG`
* `Florea` → `./icons/Florea.PNG`
* `Frostveil` → `./icons/FrostveiI.PNG`
* `Solaris` → `./icons/Solaris.PNG`


* **1:1 Icon Framing:** Since all icons are `1:1`, render them inside a crisp `44px × 44px` or `52px × 52px` square/chamfered frame with `object-fit: cover` (or `contain` if your PNGs have transparent edges/logos that shouldn't crop) and a subtle neon border that lights up when a team is selected or advances.
* **Fallback Guard (`onerror`):** If an image fails to load on a live broadcast machine, fall back cleanly to a 2-letter monogram badge (`AU`, `FL`, `FR`, `SO`) instead of a broken image icon.

---

## 2. Cyberpunk Visual Direction & Layout

### Aesthetic & Color System

* **Background:** Deep obsidian / void black (`#07090F`) with a subtle tactical grid (`repeating-linear-gradient`) and faint scanline overlay (`pointer-events: none` so clicks are never blocked).
* **Primary Accents:**
* **Electric Cyan (`#00F0FF`)** for active slots, bracket connectors, and CODM highlights.
* **Cyber Magenta / Hot Pink (`#FF2A85`)** for MLBB highlights, live indicators, and reset/danger actions.
* **Volt Yellow (`#FFE600`)** for Finals slots, Champions, and Third-Place medals.
* **Muted Slate (`#141926` / `#6E7B94`)** for eliminated/dimmed teams.


* **Typography:**
* Display / Headers: `Orbitron` or `Rajdhani` (bold, wide tracking, uppercase callouts).
* Body / Match Data: `JetBrains Mono` or `Share Tech Mono` (tabular numbers for timestamps and match IDs).



### Screen Zones (Single-Screen Broadcast + Control View)

1. **Top Command Bar:**
* Event badge: `NIGHT 4 // NOV 29, 2026`
* Game Switcher Tabs: **`COD: MOBILE`** (7:15 PM–10:20 PM blocks) and **`MOBILE LEGENDS`** (8:10 PM–11:30 PM blocks) — plus a **"Side-by-Side / Dual View"** toggle for hosts/producers.
* Action buttons: **Randomize Bracket**, **Export JSON**, **Import/Load JSON**, **Reset Match**, and **Stream/Clean Mode** (hides control hints for OBS/stream capture).


2. **Schedule & Phase Strip:**
* Shows the exact Night 4 time slots for the selected game (e.g., `Prelim 1: 7:20 PM`, `Prelim 2: 7:45 PM`, `3rd Place: 9:35 PM`, `Finals: 10:00 PM`) and highlights the currently active/next match.


3. **Interactive Bracket Canvas:**
* **Column 1 — Preliminary Round (Semifinals):** Match 1 (`Team 1 vs Team 2`) and Match 2 (`Team 3 vs Team 4`).
* **Column 2 — Split Paths:**
* Upper path leads to **Grand Finals** (Winner 1 vs Winner 2).
* Lower path leads to **Third-Place Playoff** (Loser 1 vs Loser 2).


* **Column 3 — Final Standings:** Displays **Champion** (1st) and **3rd Place** podium cards with team icons.



---

## 3. Core Interactive Features & State Flow

### A. Built-In Bracket Randomizer

* Uses a **Fisher-Yates shuffle** on `["Aurelia", "Florea", "Frostveil", "Solaris"]` independently for **CODM** and **MLBB** (or one-click "Randomize Active Game" vs. "Randomize Both").
* **Visual Roll Feedback:** Briefly cycles team names/icons for ~600ms with a neon flash before locking in `Team 1`, `Team 2`, `Team 3`, and `Team 4`.
* **Safety Guard:** If results have already been entered in that bracket, prompt or warn before overwriting unless "Force Shuffle" is clicked.

### B. Updating Winners & Propagating Down the Bracket

* Each match card displays two clickable team rows with their `1:1` icon, team name, and a **"Set Winner"** / click-to-advance trigger (plus optional score/MVP notes if you want them later).
* **Automatic Propagation:**
* Selecting a winner in **Prelim 1** automatically sends:
* **Winner 1** → **Finals (Slot 1)**
* **Loser 1** → **Third-Place Playoff (Slot 1)**


* Selecting a winner in **Prelim 2** automatically sends:
* **Winner 2** → **Finals (Slot 2)**
* **Loser 2** → **Third-Place Playoff (Slot 2)**


* Selecting a winner in **Third-Place Playoff** locks **3rd Place**.
* Selecting a winner in **Finals** locks **Champion** and highlights the winning card with a neon gold/cyan glow.


* **Changing a Mind / Cascade Recalculation:** If an operator clicks a *different* winner in an earlier round by mistake, downstream slots recalculate cleanly (clearing stale downstream winners if a feeder team changed).

### C. Export & Load Match State (JSON)

* **Export Match:** Downloads a timestamped `.json` file (e.g., `night4-bracket-2026-11-29.json`) containing:
* Metadata (`event: "Night 4"`, `date: "2026-11-29"`, `exportedAt`)
* `codm` state (seed order, Prelim 1/2 winners, 3rd place winner, Finals winner)
* `mlbb` state (seed order, Prelim 1/2 winners, 3rd place winner, Finals winner)
* Active tab (`codm` or `mlbb`)


* **Load Match:** Uses a `<input type="file" accept=".json">` to restore any saved match state immediately—handy if a browser refreshes mid-event or if you want to prepare/test brackets ahead of time.
* **Auto-Save Backup:** Silently saves to `localStorage` on every click as a crash safety net, while **Export / Load** lets you move states between devices or archive Night 4 results.

---

## 4. Data Structure Blueprint

```json
{
  "event": "Night 4",
  "date": "2026-11-29",
  "activeGame": "codm",
  "brackets": {
    "codm": {
      "seeds": ["Solaris", "Frostveil", "Florea", "Aurelia"],
      "winners": {
        "p1": null,
        "p2": null,
        "third": null,
        "finals": null
      }
    },
    "mlbb": {
      "seeds": ["Aurelia", "Florea", "Frostveil", "Solaris"],
      "winners": {
        "p1": null,
        "p2": null,
        "third": null,
        "finals": null
      }
    }
  }
}

```

---

## 5. Build Roadmap (Recommended Order)

1. **Scaffold & Styling:** Build the dark cyberpunk layout, clipped corners (`clip-path: polygon(...)`), neon borders, and game switcher (`CODM` / `MLBB`).
2. **Team & Icon Registry:** Wire up `Aurelia.PNG`, `Florea.PNG`, `FrostveiI.PNG`, and `Solaris.PNG` with `1:1` aspect ratio and monogram fallback.
3. **Randomizer & Bracket Engine:** Implement Fisher-Yates shuffling and winner/loser propagation into `3rd Place` and `Finals` with cascade clearing on edits.
4. **Export / Load & Persistence:** Add JSON download, JSON file upload with validation (checking that team names and keys are valid), and `localStorage` auto-save.
5. **Broadcast Polish:** Add a `Stream Mode` toggle and verify readability at 1080p for shoutcasters and OBS capture.
