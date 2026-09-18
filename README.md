# sngdesigns.github.io

The user site for SNG Designs. Publish **this folder** as a new public repository
named `sngdesigns.github.io`; GitHub Pages then serves it at
`https://sngdesigns.github.io/`.

AdMob verification needs exactly one thing from it:

```
https://sngdesigns.github.io/app-ads.txt
```

AdMob reads the **developer website** from the App Store listing — for iOS that is
the Marketing URL — and only requests `https://<hostname>/app-ads.txt`. Any path in
the URL is ignored, which is why a `github.com/sngdesigns/onestick` URL can never
verify: the crawler would ask for `https://github.com/app-ads.txt`.

## Publish

The repository **must** be named exactly `sngdesigns.github.io` (the account name +
`.github.io`), because that is what makes it a *user* site serving the host root. It
must also be public for Pages on the free plan.

With the GitHub CLI (creates the repo, pushes, and enables Pages):

```bash
cd sngdesigns.github.io
git init -b main
git add .
git commit -m "Add app-ads.txt, privacy policy and support pages"
gh auth login   # once, as the sngdesigns account
gh repo create sngdesigns.github.io --public --source=. --remote=origin --push
gh api repos/sngdesigns/sngdesigns.github.io/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'
```

Paste note: run `gh auth login` on its own line, and keep the single quotes around
`source[branch]` / `source[path]`. In zsh an unquoted bracket is a filename pattern, and
text in parentheses after a `#` that zsh does not treat as a comment is read as a glob
qualifier, which fails with `zsh: unknown file attribute` or `unknown sort specifier`.

Or create the empty public repo on github.com first, then:

```bash
cd sngdesigns.github.io
git init -b main
git add .
git commit -m "Add app-ads.txt, privacy policy and support pages"
git remote add origin https://github.com/sngdesigns/sngdesigns.github.io.git
git push -u origin main
```

Then on GitHub: **Settings → Pages → Source: Deploy from a branch → Branch: main,
folder: / (root) → Save**. First publish takes about a minute.

## Verify the file is live

Open `https://sngdesigns.github.io/app-ads.txt`. It must show:

```
google.com, pub-5224839487665895, DIRECT, f08c47fec0942fa0
```

## Then wire up the listings

App Store Connect → **App Information**:

| Field | Value |
| --- | --- |
| Marketing URL | `https://sngdesigns.github.io` |
| Support URL | `https://sngdesigns.github.io/support/` |
| Privacy Policy URL | `https://sngdesigns.github.io/privacy/` |

AdMob: **Apps → app-ads.txt → Check for updates**. AdMob also has to read the public
App Store listing, so verification only completes once the app is live.

## What is on the site

| Path | What it is |
| --- | --- |
| `/` | The landing page every promotional channel links to: App Store button, the 15-second preview clip, today's Daily Challenge, screenshots, feature list, legal links |
| `/privacy/`, `/support/` | Required by App Store Connect, linked from the live listing — never move these paths |
| `/app-ads.txt` | The AdMob crawler record. It must stay at the host **root** |
| `/preview.mp4` | The 15-second onboarding clip, copied from the app's App Store preview |
| `/daily-puzzles.json` | The puzzle catalog with `solutions` stripped. Today's puzzle is resolved in the browser from the same epoch formula the app uses, so the Daily Challenge card never goes stale |
| `/og.png` | 1200 × 630 link preview image for X, Facebook, iMessage and Slack |
| `/shots/*.jpg`, `/icon.png` | Web-sized copies of the app's screenshots and icon |

## Regenerate

This folder is generated from the game repository:

```bash
python3 Scripts/build_github_pages.py
```

Sources: `app-ads.txt` (repository root), `PrivacyPolicy.md`, `support/Support.md`,
`OneStickMathChallenge/Models/Puzzles.swift`, plus the screenshots and preview clip
under `AppStoreScreenshots/` and `AppStorePreviews/` (both git-ignored in the game
repository, so those assets only exist on the machine that captured them — the site
still builds without them and simply drops the blocks that need them).

## Publish an update

Regenerating rewrites the folder but never its `.git`, so publishing is two steps:

```bash
cd sngdesigns.github.io
git add -A
git status          # check nothing else was swept in
git commit -m "Update landing page"
git push
```

Allow a minute for Pages to rebuild, then confirm the page and the crawler record
still answer at `https://sngdesigns.github.io/` and
`https://sngdesigns.github.io/app-ads.txt`.
