# limon-legal

Public static site holding limon's privacy policy, terms of use and support page.
Plain HTML and CSS, no build step — GitHub Pages serves these files as they are.

These pages exist because the App Store requires a reachable privacy policy URL
and a support URL for every app, and because the app links to them from the
sign-in and profile screens (`constants/legal.ts` in the `limon` repo).

```
index.html     Landing page linking to the three documents
privacy.html   Privacy policy
terms.html     Terms of use
support.html   Support / FAQ, used as the App Store support URL
style.css      Shared styles, using limon's own palette
```

> **Not legal advice.** These documents were drafted to describe what the app
> actually does. Have them reviewed if limon starts taking payments or grows
> beyond a personal project.

## Before you publish

Every placeholder is wrapped in `[[ FILL: ... ]]` and rendered with a yellow
highlight so it is impossible to miss on the page. Find them all with:

```bash
grep -rn "FILL:" .
```

Still outstanding:

1. **Country of establishment** — one placeholder in `privacy.html` section 1 and
   one in `terms.html` section 15. Everything else is filled in.

Already set: the operator is **Tomas Olalde**, and the contact address is
**olaldetomas1@gmail.com**. To change the address later:

```bash
grep -rln "olaldetomas1@gmail.com" . | xargs sed -i '' 's/olaldetomas1@gmail\.com/you@example.com/g'
```

Also confirm the two factual claims in `privacy.html` still hold when you ship:
that there is no analytics or advertising SDK in the app (true today), and that
you are on the **paid** Gemini API tier, since the free tier does allow Google to
use submitted content to improve its models.

## Publishing to GitHub Pages

GitHub Pages needs a **public** repo on the free plan, which is why this is
separate from the private `limon` and `limon-server` repos. There is nothing
sensitive here.

Create the repo under your personal account, then:

```bash
git add .
git commit -m "Add privacy policy, terms of use and support pages"
git branch -M main
git remote add origin git@github-olaldetomas:olaldetomas/limon-legal.git
git push -u origin main
```

Then in the repo on GitHub: **Settings → Pages → Source: Deploy from a branch →
`main` / `/ (root)` → Save**. The site appears at
`https://olaldetomas.github.io/limon-legal/` after a minute or so.

To preview locally first:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Wiring the URLs into the app

Add these to the `limon` app's `.env.local`, using whatever your published base
URL turns out to be:

```bash
EXPO_PUBLIC_PRIVACY_URL=https://olaldetomas.github.io/limon-legal/privacy.html
EXPO_PUBLIC_TERMS_URL=https://olaldetomas.github.io/limon-legal/terms.html
EXPO_PUBLIC_SUPPORT_EMAIL=you@example.com
```

Without these, `constants/legal.ts` falls back to `https://limon.app/privacy`,
which does not exist.

The same URLs also go into **App Store Connect**, in two separate places that
are easy to miss:

- **App Information → Privacy Policy URL** → `privacy.html`
- **App Information → Support URL** → `support.html`

The **App Privacy** questionnaire has to match `privacy.html`. Based on what the
app does today, that means: contact info (name, email) and user content (recipes,
photos, audio) are collected and linked to the user, for app functionality; and
**no** data is used for tracking or advertising.

## If you later buy the limon.app domain

Put the domain in **Settings → Pages → Custom domain**, add the `CNAME` file
Pages generates, and point DNS at GitHub. Then update the two `EXPO_PUBLIC_*`
URLs and the App Store Connect fields. Nothing in the HTML needs to change,
since all links between pages are relative.
