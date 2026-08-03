# Sarah's Latin Music

A static web app for building, previewing, and downloading a five-dance
**International Latin** competition routine — **Cha Cha → Samba → Rumba →
Paso Doble → Jive**, with four mandatory 30-second breaks between dances.

No backend, no database, no build step. It runs entirely in the browser and
deploys to **GitHub Pages**. All audio is processed privately on the user's
device — nothing is ever uploaded to a server.

---

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
        └── ...  (up to ~40 files)
```

## 2. The hero image

Put your ballroom picture here and name it exactly:

```
assets/images/ballroom-hero.jpg
```

If the image is missing the site still works — it shows a soft pink/gold
gradient in place of the photo.

## 3. Uploading MP3 files

1. Drop your `.mp3` files into `assets/audio/`.
2. **Name each file with a dance-prefix** so the app knows the category:

   | Prefix | Dance       | Example filename                |
   |--------|-------------|---------------------------------|
   | `C`    | Cha Cha     | `C_-_Stand_by_Me.mp3`           |
   | `S`    | Samba       | `S_-_Tico_Tico.mp3`             |
   | `R`    | Rumba       | `R_-_Scheherazade.mp3`          |
   | `P`    | Paso Doble  | `P_-_Ole_Torero.mp3`            |
   | `J`    | Jive        | `J_-_In_the_Mood.mp3`           |

   The prefix is just the **first letter** followed by separators. So
   `C_-_Song.mp3`, `-C-Song.mp3`, and `C-Song.mp3` all work.

## 4. Updating the track manifest (important)

GitHub Pages **cannot list a folder's contents**, so every song must also be
listed in the manifest inside `index.html`. Open `index.html`, find the block
labelled **`2) TRACK MANIFEST`**, and add one line per song:

```js
const TRACKS = [
  // ---------- Cha Cha (C) ----------
  { file: "C_-_Stand_by_Me.mp3" },
  { file: "C_-_The_Boy.mp3" },
  // ...add more here, one per line...
];
```

- The displayed title is generated automatically (prefix removed, `_`/`-`
  turned into spaces). `C_-_Stand_by_Me.mp3` shows as **Stand by Me**.
- To force a custom title: `{ file: "C_-_Stand_by_Me.mp3", title: "Stand By Me (2024)" }`
- The filename in the manifest **must match the file in `assets/audio/` exactly**
  (including capitalisation).

## 5. Testing locally

Opening `index.html` by double-clicking (a `file://` URL) **will not fully
work** — browsers block audio/`fetch` and the FFmpeg engine over `file://`.
Run a tiny local web server instead:

```bash
# Python 3 (built in on macOS/Linux)
cd sarahs-latin-music
python3 -m http.server 8000
# then open http://localhost:8000
```

```bash
# or Node
npx serve .
```

## 6. Publishing to GitHub Pages

1. Create a repository (e.g. `sarahs-latin-music`) and push these files.
2. On GitHub: **Settings → Pages**.
3. Under *Build and deployment*, set **Source: Deploy from a branch**, branch
   **`main`**, folder **`/ (root)`**, then **Save**.
4. Your site appears at:
   `https://YOUR-USERNAME.github.io/sarahs-latin-music/`

### Relative paths (why this matters)

All paths in the app are **relative** (`assets/audio/…`, `assets/images/…`),
never absolute (`/assets/…`). On a GitHub *project* site the app lives in a
sub-path, so a leading `/` would break it. Keep paths relative.

## 7. Browser support

Works in current **Chrome, Edge, Firefox, and Safari** on desktop and mobile.
- **Pitch-preserving playback** uses the browser's `preservesPitch` feature.
- **MP3 export** uses `ffmpeg.wasm` (single-threaded build) loaded from a CDN,
  which does **not** require any special server headers — ideal for GitHub Pages.

## 8. A note on browser-based MP3 processing

Building the merged MP3 happens on the user's own device, so:
- The first export downloads the ~30 MB FFmpeg engine once (then it's cached).
- Processing five tracks + four breaks can take from ~20 seconds to a couple of
  minutes. **It is noticeably slower on phones** — the app says so on screen.
- Everything stays private; audio is never sent anywhere.

---

*Dancing with my best friends ♡*
