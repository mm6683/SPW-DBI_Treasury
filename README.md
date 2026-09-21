# Kurkenemmer

A team cork/cash-tracking app (admin panel, team sign-in, ledger) built as a single
self-contained web page.

## Structure

```
index.html                     the whole app (HTML + CSS + JS, no build step)
SPW_DeBoomIn_Huisstijl/         brand assets used by the app
  ├─ logos/                     logo image
  ├─ graphics/                  background + banner images
  ├─ fonts/                     Junegull + AustinText font files
  └─ color_palette.txt          brand colors reference
```

## Running it locally

No install, no build step. Just open `index.html` in a browser, or serve the folder
with any static file server, e.g.:

```
python3 -m http.server 8000
```

## Deploying on GitHub Pages

1. Push this folder to a repo.
2. Repo Settings → Pages → set source to the branch/root containing `index.html`.
3. Your site is live at `https://<username>.github.io/<repo>/`.

## Current status / known limitations

- **Admin password is a placeholder.** Search `index.html` for `ADMIN_PASSWORD` and set
  a real value before this goes in front of real users. Note this is a client-side
  check only (visible in page source) — fine as a soft gate, not real security.
- **Data does not sync between devices.** State is stored in the browser's
  `localStorage`, so each phone/laptop has its own separate copy. See the chat where
  this was generated for backend/sync recommendations to fix this.
