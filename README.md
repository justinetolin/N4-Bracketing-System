# N4 Bracket Control

A single-page tournament bracket and live broadcast control app for Night 4 on November 29, 2026. It supports CODM and MLBB, with team icons stored in `icons/`.

## Start the app

- Open `index.html` in a modern browser, or publish the folder with GitHub Pages.
- Keep `index.html`, `bracket.json`, and the `icons/` folder together so the relative icon paths work.
- The starting CODM and MLBB lineups are defined in the app from `bracket.json`. The app does not currently fetch that JSON file at runtime.
- Firebase sync is optional. Without Firebase configuration, the app continues to work locally and shows a cloud setup status.

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

The app saves bracket changes to the browser’s `localStorage` automatically. Without Firebase, that state is local to the browser and device. With Firebase configured, an authorized operator’s changes are also written to the shared realtime database, and the viewer receives updates. Local autosave remains a backup. Export JSON and Import / Load are still available for manual transfer.

## Secure live viewer setup (Firebase)

The public viewer link is `https://YOUR_SITE_URL/?view=screen`. It opens in Live Mode with operator controls hidden and team rows disabled. Removing `?view=screen` only changes the page UI: Firebase rules still deny database writes unless the request is authenticated as the one operator UID. The bracket data is public/read-only, so treat it as public information; anyone who can access the site/config can read it, but only the configured operator can write it.

1. Create a Firebase project and a **Realtime Database**.
2. In **Authentication → Sign-in method**, enable **Email/Password**. Create one dedicated shared operator account in the Firebase console. All trusted operators will use the same password; the app asks for only that password.
3. Copy the Firebase Web App config into `firebase-config.js`, including the exact `databaseURL` shown for your database. Set `window.N4_OPERATOR_EMAIL` to the email for the shared Firebase account; operators will not need to enter this email in the app.
4. Find the shared account’s Firebase **UID** in Authentication → Users. Put it in `window.N4_OPERATOR_UID` in `firebase-config.js` and replace `REPLACE_WITH_OPERATOR_UID` in `database.rules.json` with the same UID.
5. Replace the Realtime Database rules with the contents of `database.rules.json` and publish them in the **Rules** tab, or select your Firebase project with the Firebase CLI and run `firebase deploy --only database` (the included `firebase.json` points to `database.rules.json`). Replace any existing permissive root rules; a broader ancestor write rule can override the intended restriction.
6. Add the GitHub Pages hostname to Authentication’s **Authorized domains** list.
7. Deploy the site. On the operator machine, open the normal site URL and choose **Operator Sign In**. Enter the shared password. On the screen machine, open the `?view=screen` URL.

This avoids giving every operator a separate account, but the shared password can be forwarded or reused, and Firebase cannot tell which operator made a change. Rotate or disable the shared account if the password is exposed. The web app config and operator UID are visible to site visitors; they are not passwords. The database Security Rules are the access boundary. Never put a Firebase Admin SDK key or service-account credential in this site. The included rule allows public reads for the viewer and writes only from the configured UID. Verify that the deployed rule contains the real UID before using the app live.

## Publish with GitHub Pages

1. Push this folder to a GitHub repository.
2. In the repository, open **Settings → Pages**.
3. Select the branch and folder that contain `index.html` (commonly `main` and `/ (root)`), then save.
4. Open the Pages URL after GitHub finishes publishing.

Because the app uses relative asset paths, publish the `icons/` folder with the HTML file.
