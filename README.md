# Weekend Cricket Scorecard

A live cricket scorecard for casual (gully) matches with friends — ball-by-ball
scoring, a shared player roster, past-game history, and career statistics.
Hosted as a free static site on GitHub Pages, with Firebase (Firestore) as the
free real-time backend.

## What's in this repo

- `index.html` — the whole app (menu, scoring, scorecard, past games, player
  list, statistics). No build step — it's plain HTML/CSS/JS.
- `firebase-config.js` — **you edit this** with your own Firebase project's
  keys (see setup below). It's safe to commit; see the comment in the file
  for why.
- `README.md` — this file.

## One-time setup (about 10 minutes)

### 1. Create a free Firebase project

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and sign in with any Google account.
2. Click **Add project**, give it any name (e.g. "weekend-cricket"), and finish the wizard (you can skip Google Analytics).
3. Once the project opens, click the **gear icon → Project settings**.
4. Under **Your apps**, click the **web icon (`</>`)** to register a new web app. Give it any nickname. You don't need Firebase Hosting — just finish the wizard.
5. You'll be shown a `firebaseConfig` object with keys like `apiKey`, `authDomain`, `projectId`, etc. Copy these.

### 2. Enable Firestore (the database)

1. In the left sidebar, click **Build → Firestore Database**.
2. Click **Create database**. Choose any region close to you.
3. Start in **test mode** (open read/write) — this app has no login system, so it relies on test-mode rules plus an in-app PIN to gate who can score. Good enough for a group of friends; not meant for the public internet. If you want to tighten it later, Firestore security rules live under **Firestore Database → Rules**.

### 3. Paste your config into this repo

1. Open `firebase-config.js` in this repo.
2. Replace the placeholder values with the real ones you copied in step 1.
3. Commit and push.

### 4. Turn on GitHub Pages

1. In this repo on GitHub: **Settings → Pages**.
2. Under **Build and deployment**, set **Source** to "Deploy from a branch", pick your default branch and the `/ (root)` folder.
3. Save. GitHub will give you a URL like `https://<your-username>.github.io/weekend-cricket-scorecard/` — that's your app, shareable with anyone.

That's it — no server, no npm install, nothing else to run.

## How it works day-to-day

- **Scorer PIN**: the first person to tap "Set up scorer PIN" on the Home
  screen picks a PIN and becomes the scorer on their phone. Anyone else who
  wants to score enters the same PIN under "Enter scorer PIN". Everyone else
  stays in viewer mode and sees the score update live, but can't tap
  anything.
- **New match**: sets up teams, overs, and openers, then starts live
  scoring. Everyone with the link sees it update ball by ball.
- **Past games**: every match that finishes is automatically saved; tap one
  to see its full scorecard.
- **Player list**: a shared roster (reused match after match) split into
  Squad A / Squad B, with optional photos.
- **Statistics**: career batting and bowling numbers computed across every
  saved match.

## Security note

There's no real authentication here — the scorer PIN is a convenience to
stop casual friends from fat-fingering the scoring buttons, not a security
boundary. Firestore is in open test mode, so technically anyone who finds
your Firebase project ID could read or write the data directly. For a
private group playing casual cricket this is a reasonable trade-off against
the complexity of real auth; don't use this for anything sensitive.

## Troubleshooting

- **"One-time setup needed" screen on load** → `firebase-config.js` still has
  the placeholder values. Fill them in and reload.
- **Past games / Statistics error in console mentioning an index** →
  Firestore sometimes asks you to create a composite index the first time you
  run a new query (the `orderBy('finishedAt')` query on the `matches`
  collection). Firestore's error message includes a direct link to create it
  — click it, wait a minute, then reload.
- **Scores not syncing across phones** → double check every phone opened the
  *same* GitHub Pages URL, and that `firebase-config.js` points at the same
  Firebase project on all of them (it's baked into the page, so this is
  automatic once it's pushed).
