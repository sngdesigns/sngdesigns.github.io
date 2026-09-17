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

## Regenerate

This folder is generated from the game repository:

```bash
python3 Scripts/build_github_pages.py
```

Sources: `app-ads.txt` (repository root), `PrivacyPolicy.md`, `support/Support.md`.
