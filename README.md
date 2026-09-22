# Kurkenemmer

A team cork/cash-tracking app (admin panel, player PIN sign-in, bankier role, teamkas
leaderboard) built as a single self-contained web page. All game state lives in a
shared Firestore document, so every device sees updates live.

## Structure

```
index.html                     the whole app (HTML + CSS + JS, no build step)
SPW_DeBoomIn_Huisstijl/         brand assets used by the app
  ├─ logos/                     logo image, incl. Stempels3cm.jpg.jpg (site favicon)
  ├─ graphics/                  background + banner images
  ├─ fonts/                     Junegull + AustinText font files
  └─ color_palette.txt          brand colors reference
```

## Roles & groups

- **Bankiers** — two fixed people who can fund/take on anyone's personal account
  and view the full roster.
- **Grenswacht** — a group (any number of members) with the same fund/take/roster
  powers as bankiers, plus a "Kurken boven de 50 innemen" screen: pick anyone with
  more than the threshold (default 50 kurken) on their personal account, and the
  excess is clipped straight into the Grenswacht teamkas. Their teamkas also shows
  up on the leaderboard alongside the four teams.
- **Teams** — a leider + members, each with a personal balance and a shared teamkas.
  Members can deposit into their own teamkas, and (new) send kurken directly to any
  other person or to any teamkas, not just their own.

## Admin panel extras

Alongside the full game reset, the admin panel now has:
- **Codes resetten** — clear one specific person's PIN (they'll set a new one next
  time they sign in) without touching anyone else's.
- **Iemand verplaatsen** — move any person between groups (a team, Bankiers, or
  Grenswacht). Their personal balance always stays attached to them — while they're
  Bankier or Grenswacht it's simply not shown anywhere, and it reappears as soon as
  they land in a team again.

## Admin password

The default login is `placeholder`. As long as that's still the active password,
logging in with it prompts you to set a real one on the spot (with a "later" option
to skip) — no code edits needed. The chosen password is stored in the shared game
document (`S.adminPassword`) so it works from every device, and takes over from
`placeholder` from then on. A full game reset clears it, so a fresh game starts back
on `placeholder` and nudges again next login.

## One-time setup: connect Firebase (free)

The app needs a Firestore database to sync between devices. This takes about
5 minutes and stays on Firebase's free Spark plan for a game this size.

1. Go to https://console.firebase.google.com, sign in, click **Add project**,
   give it any name (e.g. `kurkenemmer`), and finish the wizard.
2. In the project, click **Build → Firestore Database → Create database**.
   Pick a location close to Belgium (e.g. `eur3`) and start in **production mode**
   (we'll set our own rule in step 3).
3. Click the **Rules** tab and replace the contents with:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /kurkenemmer/{doc} {
         allow read, write: if true;
       }
     }
   }
   ```
   then click **Publish**.

   ⚠️ This makes the game data open to anyone who has your site's URL and looks at
   the page source (there's no login system in this app). That's a reasonable
   trade-off for a kurken-counting game with no real money or personal data — but
   it does mean a curious player *could* tamper with balances if they wanted to.
   If that ever matters, the fix is adding Firebase Authentication + rules that
   check the signed-in user's role, which is a bigger change than this app has today.
4. Back on the project Overview page, click the **`</>`** (web) icon to register
   a web app, name it anything, and skip Firebase Hosting (you're using GitHub
   Pages). It will show you a `firebaseConfig` object.
5. Open `index.html`, find `firebaseConfig` near the top of the `<script>` block,
   and paste in your own `apiKey`, `authDomain`, `projectId`, `storageBucket`,
   `messagingSenderId`, and `appId` values.

That's it — commit the change and every device that loads the page now reads and
writes the same shared game.

## Running it locally

No install, no build step. Just open `index.html` in a browser, or serve the folder
with any static file server, e.g.:

```
python3 -m http.server 8000
```

## Deploying on GitHub Pages

1. Push this folder to a repo (after filling in `firebaseConfig` above).
2. Repo Settings → Pages → set source to the branch/root containing `index.html`.
3. Your site is live at `https://<username>.github.io/<repo>/`.

## Current status / known limitations

- **`firebaseConfig` still has placeholder values.** Until you fill it in (see
  above), the app will sit on "Verbinden…" / show a connection error.
- **Admin password starts as a placeholder, on purpose.** The app nudges whoever
  logs in with the default `placeholder` password to set a real one (see "Admin
  password" above). This is still a client-side check only (visible in page
  source) — a soft gate, not real security.
- **Firestore rules are wide open** by design for simplicity — see the security
  note in step 3 above.
