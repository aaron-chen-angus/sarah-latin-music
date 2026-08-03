# Sarah's Latin Music

A static web app for composing, previewing, and downloading a five-dance
**International Latin** routine — **Cha Cha → Samba → Rumba → Paso Doble →
Jive**, with automatic 30-second competition breaks between dances.

Black-and-gold championship styling, no backend, no build step. Runs entirely
in the browser and deploys to **GitHub Pages**. All audio is processed
privately on the user's device — nothing is uploaded to any server.

---

## What's new in this version

- **All 40 tracks** are pre-listed in the manifest (8 per dance).
- **Optional dances** — you may leave any section empty. The routine plays and
  exports with whatever you've selected, and a 30-second break is inserted
  **only between two chosen dances** (no trailing or orphan breaks).
- **Preview controls per dance** — Preview (▶), Stop (⏹) and Repeat (⟳), all at
  the slot's selected speed with pitch preserved.
- **New black & gold competition theme.**

## 1. Folder structure

```
sarahs-latin-music/
├── index.html
├── README.md
└── assets/
    ├── images/
    │   └── ballroom-hero.jpg
    └── audio/
        ├── C_-_Stand_by_Me.mp3
        ├── S_-_Tico_Tico.mp3
        ├── R_-_Scheherazade.mp3
        ├── P_-_Ole_Torero.mp3
        ├── J_-_In_the_Mood.mp3
        └── ...  (40 files total)
```

## 2. The hero image

Save your picture as exactly:

```
assets/images/ballroom-hero.jpg
```

**Optimise large photos first.** A full-resolution export can be 20 MB+, which
makes the page slow to load. Resize the long edge to ~2400 px and save at
JPEG quality ~82 (usually gets it under 300 KB) before uploading. If the file
is missing or misnamed, the site still works — it falls back to a dark
gold/black gradient.

## 3. Uploading MP3 files

Drop `.mp3` files into `assets/audio/`. **Name each with a dance-prefix** so the
app knows the category (the first letter, followed by a separator):

| Prefix | Dance       | Example                         |
|--------|-------------|---------------------------------|
| `C`    | Cha Cha     | `C_-_Stand_by_Me.mp3`           |
| `S`    | Samba       | `S_-_Tico_Tico.mp3`             |
| `R`    | Rumba       | `R_-_Scheherazade.mp3`          |
| `P`    | Paso Doble  | `P_-_Ole_Torero.mp3`            |
| `J`    | Jive        | `J_-_In_the_Mood.mp3`           |

Spaces, hyphens or underscores after the prefix all work
(`J - Mack the Knife.mp3` and `J_-_Mack_the_Knife.mp3` are both fine). Only the
**title** shows in the app — the prefix and separators are stripped
automatically.

## 4. Updating the track manifest

GitHub Pages **cannot list a folder's contents**, so every song must also be
listed inside `index.html`. Open it, find the block **`2) TRACK MANIFEST`**, and
add one line per song:

```js
const TRACKS = [
  // ---------- Cha Cha (C) ----------
  { file: "C_-_Stand_by_Me.mp3" },
  // ...add more here...
];
```

- The manifest text **must match the real filename exactly**, including
  capital letters and accents (`R_-_Lejanía.mp3`). Accented characters are
  URL-encoded automatically when the file is requested.
- Custom display title (optional): `{ file: "S_-_Peanur_Vendor.mp3", title: "The Peanut Vendor" }`

## 5. Testing locally

Double-clicking the file (a `file://` URL) **won't fully work** — browsers block
audio, `fetch`, and the FFmpeg engine over `file://`. Run a small local server:

```bash
cd sarahs-latin-music
python3 -m http.server 8000     # then open http://localhost:8000
# or:  npx serve .
```

## 6. Publishing to GitHub Pages

1. Push these files to your repository.
2. **Settings → Pages**.
3. *Build and deployment* → **Source: Deploy from a branch**, branch **`main`**,
   folder **`/ (root)`** → **Save**.
4. Live at `https://YOUR-USERNAME.github.io/sarahs-latin-music/`.

### Relative paths matter

All paths are **relative** (`assets/audio/…`), never absolute (`/assets/…`). On
a GitHub *project* site the app lives in a sub-path, so a leading `/` would
break it. Keep paths relative.

## 7. Browser support & performance

Works in current **Chrome, Edge, Firefox, and Safari**, desktop and mobile.
- **Playback** slows tempo while preserving pitch via the browser's
  `preservesPitch` feature (no chipmunk vocals).
- **MP3 export** uses the single-threaded `ffmpeg.wasm` build loaded from a CDN,
  which needs **no special server headers** — ideal for GitHub Pages.
- The first export downloads the ~30 MB FFmpeg engine once (then cached), and
  processing runs on the user's device. It is noticeably **slower on phones** —
  the app says so on screen. Nothing ever leaves the browser.

---

## Verification checklist

- [x] Tracks can be selected per dance (drag-and-drop **or** Add button)
- [x] Wrong-category drops are rejected
- [x] Speeds selectable independently (100%→60%), pitch preserved
- [x] Preview each dance with Play / Stop / Repeat at the chosen speed
- [x] Any dance may be left empty; breaks appear only between chosen dances
- [x] Full playback follows Cha Cha → Samba → Rumba → Paso Doble → Jive order
- [x] Pause / resume / stop / previous / next / seek / volume all work
- [x] MP3 export merges selected tracks + 30-second breaks in order
- [x] Exported MP3 reflects each track's selected speed
- [x] Responsive on desktop and mobile
- [x] Hero image loads from `assets/images/ballroom-hero.jpg`
- [x] Works on a GitHub Pages project site (relative paths)

*Five dances. One floor.*
