# Climb 5.12 — self-hosted tracker

Same tracker, running as a normal website instead of inside the Claude artifact
viewer. That one change is what makes saving work on your phone: the page is
now the top-level document on your own domain, so `localStorage` is first-party
and the browser keeps it. Inside the artifact viewer the page runs in a
cross-site iframe, and mobile Safari blocks or throws away storage there.

## Put it on GitHub Pages

1. Create a repo — `climb-512` is fine. Public is required unless you have
   GitHub Pro; Pages on a private repo needs a paid plan.
2. Upload every file in this folder to the **root** of the repo:
   `index.html`, `manifest.webmanifest`, `sw.js`, the four `icon-*.png` files,
   and `.nojekyll` (that last one is hidden — if the GitHub web uploader will
   not take it, skip it; nothing here starts with an underscore, so Jekyll
   leaves the files alone either way).
3. Repo → **Settings** → **Pages** → Source: **Deploy from a branch**,
   Branch: `main`, folder: `/ (root)`. Save.
4. Wait a minute, then open `https://<your-username>.github.io/climb-512/`.

Everything is static. No build step, no dependencies, no server code.

## Then, on your phone

Open that URL in **Safari** (iPhone) or **Chrome** (Android) and use
**Share → Add to Home Screen**. Do this before you start logging.

That matters more than it sounds. iOS clears script-writable storage for
ordinary websites you have not visited in about a week. A site added to the
home screen is treated as installed and exempt. It also launches full screen
with no browser chrome, which is nicer to use mid-session.

## What saving looks like now

Logs live in that browser on that phone, in `localStorage`, under the key
`climb512.data.v2`. Save writes immediately and reports **Saved**. It works
with no signal — the service worker caches the page, so the app opens in a gym
basement.

There is no cloud copy in this build and no account, so nothing syncs between
devices on its own. To move logs, use **Export data** on one device and
**Import data** on the other. Import replaces the logs it finds; export a copy
before you do it anywhere you care about.

Do not use a private/incognito window. Storage is blocked there, and the
Storage card at the bottom of the page will tell you so.

## Updating it later

Replace `index.html` in the repo and push. Your logs are in the browser, not in
the file, so an update never touches them. The service worker fetches from the
network first, so a new version shows up on the next load when you are online.

If you ever change what `sw.js` caches, bump `CACHE = "climb512-v1"` to `-v2`
so old cached files get dropped.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app — plan, logging, timers, progress. Self-contained. |
| `manifest.webmanifest` | Name, colors and icons for Add to Home Screen. |
| `sw.js` | Offline cache. Network first, cache fallback. |
| `icon-180.png` | Home-screen icon for iOS. |
| `icon-192.png`, `icon-512.png` | Icons for Android / desktop installs. |
| `icon-512-maskable.png` | Padded icon for Android's circle/squircle mask. |
| `.nojekyll` | Tells GitHub Pages to serve the files as-is. Optional here. |

## If you want real sync later

The honest options, roughly in order of effort:

- **Export / import.** Zero setup, manual, already built.
- **A private GitHub Gist as the store.** The page reads and writes one JSON
  gist with a personal access token you paste in once and keep in
  `localStorage`. Free and simple, but anyone who gets the token can read that
  gist, and the token sits in your phone's browser.
- **A real backend** — a small Cloudflare Worker with KV, or Supabase. Proper
  multi-device sync with an actual login. An afternoon of work and a service
  to maintain.

Ask and I will build whichever one you want.
