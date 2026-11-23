# N4 Bracket Control

A single-page tournament bracket and live broadcast control app for Night 4 on November 29, 2026. It supports CODM and MLBB, with team icons stored in `icons/`.

## Start the app

- Open `index.html` in a modern browser, or publish the folder with GitHub Pages.
- Keep `index.html`, `bracket.json`, and the `icons/` folder together so the relative icon paths work.
- The starting CODM and MLBB lineups are defined in the app from `bracket.json`. The app does not currently fetch that JSON file at runtime.

## Run a bracket

- Choose **COD: MOBILE** or **MOBILE LEGENDS** in the game tabs.
- Click a team in a preliminary match to set its winner. The other team becomes the loser.
- Preliminary winners advance to Grand Finals; preliminary losers advance to the Third-Place Playoff.
- Click a team in either playoff to set its winner. The final standings show Champion (1st), 1st Runner-Up (2nd), 2nd Runner-Up (3rd), and 3rd Runner-Up (4th).
- Click the same winner again to clear that match result. Changing a preliminary winner clears downstream playoff results so stale results are not kept.
- Use **Side-by-Side / Dual View** to display both games together.

## Live Mode and keyboard shortcuts

Choose **Live Mode** in the header, or press **L**. Live Mode hides the operator controls and expands the bracket for the broadcast. Press **L** again or **Esc** to return to Control Mode.

While Live Mode is active:

- **C** switches to Call of Duty: Mobile.
- **M** switches to Mobile Legends: Bang Bang.
- Team rows remain clickable, so an operator can advance winners while the stage view is showing.

The animated background and card effects respect the device’s reduced-motion preference.

## Randomize, reset, and transfer state

- **Randomize** shuffles the active game’s four teams. In Dual View, it shuffles both games. If results exist, the app asks before replacing them.
- **Reset Match** restores the selected game’s default lineup and clears its results. In Dual View, it resets both games.
- **Export JSON** downloads the current lineups, results, and active game as a JSON file.
- **Import / Load** restores a previously exported JSON file. The file is checked for valid game keys and team names before it is loaded.

## Autosave

The app saves bracket changes to the browser’s `localStorage` automatically. This works when hosted on GitHub Pages, but the saved state is local to that browser and device. It does not update `bracket.json`, commit changes to GitHub, or sync between devices. Use Export JSON and Import / Load to transfer a match state.

## Publish with GitHub Pages

1. Push this folder to a GitHub repository.
2. In the repository, open **Settings → Pages**.
3. Select the branch and folder that contain `index.html` (commonly `main` and `/ (root)`), then save.
4. Open the Pages URL after GitHub finishes publishing.

Because the app uses relative asset paths, publish the `icons/` folder with the HTML file.
