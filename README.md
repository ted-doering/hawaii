# Big Island '26 — Family Trip Planner

A mobile-first trip planner for the Big Island of Hawai‘i: an interactive map of hand-picked
places, a shared day-by-day itinerary, a curated guide, and a curator studio for editing
content. Families sign in with Google and share one plan via a family code.

## Deploy (GitHub Pages)

1. Create a new GitHub repo and add these files (`index.html` at the repo root).
2. In the repo: **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   pick `main` / `root`, save.
3. Your app will be live at `https://<user>.github.io/<repo>/`.

`index.html` is fully self-contained (app code, styles, and the "Ted & Chelsey Recommend"
graphic are inlined). It still loads three services from the internet at runtime:
Firebase (auth + database), Leaflet, and CARTO map tiles.

## Link preview image (when you text the link)

`og-image.png` (1200×630) is the picture that shows when the link is shared in Messages,
WhatsApp, Slack, etc. For it to appear you must:

1. Deploy `og-image.png` next to `index.html` (both at the repo root — already set up in this folder).
2. In `index.html`, replace the two `REPLACE-WITH-YOUR-DOMAIN` placeholders with your live URL,
   e.g. `https://<user>.github.io/<repo>/og-image.png`. (If you re-bundle from source, edit the
   `og:image` / `twitter:image` meta tags in `Big Island Trip.dc.html` instead.)
3. Note: some apps cache link previews. If an old preview sticks, test the fresh URL in a tool
   like opengraph.xyz or send to a new conversation.

## Firebase setup (required for sign-in & sharing)

The app is wired to a Firebase project. To make Google sign-in and family sharing work on
your domain:

1. **Authorized domains** — Firebase Console → Authentication → Settings → Authorized domains →
   add your GitHub Pages domain (e.g. `<user>.github.io`).
2. **Google sign-in** — Authentication → Sign-in method → enable **Google**.
3. **Firestore rules** — allow signed-in users to read/write the collections the app uses:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}        { allow read, write: if request.auth != null; }
       match /households/{id}     { allow read, write: if request.auth != null; }
       match /appdata/{doc}       { allow read: if true; allow write: if request.auth != null; }
     }
   }
   ```

## Curator access

Only these emails see the **Curator studio** (edit places & tips); everyone else gets the
read-only trip app. Edit the `ADMIN_EMAILS` array near the top of the source file:

```js
var ADMIN_EMAILS = ['ted@narrative.church', 'chelsey.doering@gmail.com'];
```

## Editing the app

`index.html` is compiled output — do not edit it directly. Edit `Big Island Trip.dc.html`
(the source) and re-bundle. The dashed **"Preview as curator (skip sign-in)"** button on the
login screen is a convenience for previewing without Google; remove it before going fully live
if you want.
