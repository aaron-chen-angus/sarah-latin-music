# Sarah's Latin Music

A single-page web app for **building, previewing, and downloading a five-dance
International Latin routine**. It runs entirely in the browser — no backend, no
database, no build step — and deploys as a static site on **GitHub Pages**.
All audio is processed privately on the user's own device; nothing is ever
uploaded to a server.

Championship black-and-gold styling inspired by top Latin festivals.

---

## Features at a glance

**Routine builder**
- Five ordered positions (I–V) with the default line-up **Cha Cha · Samba ·
  Rumba · Paso Doble · Jive**.
- **Flexible line-up:** a dropdown on each position lets you reassign it to any
  of the five dances — e.g. three Cha Chas, then a Samba, then a Rumba. The I–V
  order of the positions themselves is fixed; the *dance* in each is your choice.
- **Optional positions:** you may leave any position empty and still play or
  download. A 30-second break is inserted **only between two filled positions**
  (no orphan or trailing breaks).
- **Category-locked tracks:** a position only accepts tracks of its current
  dance, whether added by drag-and-drop or by the **Add** button (mobile).

**Track library**
- Tracks grouped into the five dance categories, titles auto-cleaned from
  filenames.
- **Vacancy-aware:** each category shows a live count ("2 of 3 open"). You can
  add a dance only if it's in the current line-up, and only up to the number of
  empty positions assigned to it.
- Dances not in the line-up are dimmed and marked "not in line-up"; a full
  category shows "Full"; an already-used track shows "Added". Each track is used
  at most once.

**Per-track controls**
- **Speed** from 100% down to 50% in 5% steps (100, 95, 90, 85, 80, 75, 70, 65,
  60, 55, 50), applied independently to each position. Tempo changes **preserve
  pitch** (no chipmunk vocals). Adjusted length is shown.
- **Preview / Stop / Repeat** on each filled position — auditions that track at
  its selected speed, with a repeat (loop) toggle.

**Countdown breaks**
- A real **30-second countdown clip** plays between consecutive dances (in place
  of silence), in both the live player and the exported file. Falls back to
  silence automatically if the clip is missing.

**Full-routine player**
- Play / pause / resume / stop, previous / next dance, a seek bar, elapsed and
  total time, current section, track, and speed, a live break countdown, and a
  volume control.

**Download**
- Exports the whole routine as one MP3 (tracks at their chosen speeds, countdown
  clips between them, correct order) using in-browser `ffmpeg.wasm`.

**Quality-of-life**
- Your line-up, tracks, speeds, and volume are remembered between visits
  (browser local storage). **Reset** restores the default I–V line-up.
- Responsive (desktop / tablet / phone), keyboard-accessible, honours
  reduced-motion, with a graceful fallback if the hero image is missing.

---

## Folder structure

```
sarahs-latin-music/
├── index.html
├── README.md
└── assets/
    ├── images/
    │   └── ballroom-hero.jpg
    └── audio/
        ├── Countdown_30_Sec.mp3      ← the break clip (no dance prefix)
        ├── C_-_Stand_by_Me.mp3
        ├── S_-_Tico_Tico.mp3
        ├── R_-_Scheherazade.mp3
        ├── P_-_Ole_Torero.mp3
        ├── J_-_In_the_Mood.mp3
        └── ...  (all your song files)
```

## 1. Hero image

Save your picture as exactly `assets/images/ballroom-hero.jpg`.

**Optimise large photos first** — a full-resolution export can be 20 MB+, which
makes the page slow. Resize the long edge to ~2400 px and save at JPEG quality
~82 (usually under 300 KB). If the file is missing or misnamed, the site still
works and shows a dark gold/black gradient instead.

## 2. Song files & naming convention

Drop `.mp3` files into `assets/audio/`. **Prefix each with its dance** — the
first letter, followed by a separator (space, hyphen, or underscore):

| Prefix | Dance       | Example                         |
|--------|-------------|---------------------------------|
| `C`    | Cha Cha     | `C_-_Stand_by_Me.mp3`           |
| `S`    | Samba       | `S_-_Tico_Tico.mp3`             |
| `R`    | Rumba       | `R_-_Scheherazade.mp3`          |
| `P`    | Paso Doble  | `P_-_Ole_Torero.mp3`            |
| `J`    | Jive        | `J_-_In_the_Mood.mp3`           |

`C_-_Song.mp3`, `C - Song.mp3`, and `C-Song.mp3` all work. Only the **title**
shows in the app (prefix and separators are stripped, so `C_-_Stand_by_Me.mp3`
displays as "Stand by Me").

## 3. The countdown break clip

This is the audio that plays between dances. It is **not** a dance track, so:

- Name it **`Countdown_30_Sec.mp3`** and place it in `assets/audio/`.
- **No dance prefix** (it must not start with `C`/`S`/`R`/`P`/`J` + separator).
- **Do NOT add it to the `TRACKS` manifest.** It's referenced by its own line
  near the top of the script:
  ```js
  const BREAK_FILE = "Countdown_30_Sec.mp3";
  ```
  That single line is the only place to change if you ever rename it.

The app reads the clip's real length and uses that for each break, so keep it
close to 30 seconds. If the file is absent, breaks fall back to 30s of silence.

## 4. Updating the track manifest

A static site can't list a folder's contents, so **every song must also be
listed** inside `index.html`. Open it, find the block **`2) TRACK MANIFEST`**,
and add one line per song under the right dance:

```js
const TRACKS = [
  // ---------- Cha Cha (C) ----------
  { file: "C_-_Stand_by_Me.mp3" },
  // ...add more here...
];
```

- The text **must match the real filename exactly**, including capitals and
  accents (`R_-_Lejanía.mp3`). Accented characters are URL-encoded
  automatically when the file is requested.
- Optional custom display title: `{ file: "S_-_Peanur_Vendor.mp3", title: "Peanut Vendor" }`
  (keeps the real filename, fixes only what's shown).

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
3. Under *Build and deployment* → **Source: Deploy from a branch**, branch
   **`main`**, folder **`/ (root)`** → **Save**.
4. Live at `https://YOUR-USERNAME.github.io/sarahs-latin-music/`.

**Keep paths relative.** Everything uses `assets/...`, never `/assets/...`. On a
GitHub *project* site the app lives in a sub-path, so a leading `/` would break
it.

## 7. Browser support & performance

Works in current **Chrome, Edge, Firefox, and Safari**, desktop and mobile.
- **Playback** slows tempo while preserving pitch via the browser's
  `preservesPitch` feature.
- **MP3 export** uses the single-threaded `ffmpeg.wasm` build loaded from a CDN,
  which needs **no special server headers** — ideal for GitHub Pages. The first
  export downloads the ~30 MB engine once (then cached) and processes on-device,
  so it's noticeably **slower on phones**; the app says so on screen.

---

## How to build a routine (quick tour)

1. Press **Build the Routine** to jump to the builder.
2. (Optional) Use each position's **dance dropdown** to set your line-up — e.g.
   change positions II and III to Cha Cha for three Cha Chas in a row.
3. From the library, **drag a track** onto a matching position, or press
   **Add** to drop it into the first open position of that dance.
4. Set each position's **speed** if needed, and use **Preview / Stop / Repeat**
   to audition.
5. Press **Play Full Routine** to hear it end-to-end with countdown breaks, or
   **Download Routine MP3** to export it.
6. **Reset** clears everything back to the default line-up.

---

## Verification checklist

- [x] Each position can be reassigned to any dance via dropdown (order I–V fixed)
- [x] A position accepts only its current dance (drag-drop and Add)
- [x] Library gates by line-up: add up to the number of open positions per dance
- [x] Dances not in the line-up are blocked with a clear prompt
- [x] Positions may be left empty; breaks appear only between filled positions
- [x] Speeds 100%→50% in 5% steps, independent per position, pitch preserved
- [x] Preview / Stop / Repeat work per position at the selected speed
- [x] 30-second countdown clip plays between dances (falls back to silence)
- [x] Full playback: play / pause / stop / prev / next / seek / volume
- [x] MP3 export merges tracks + countdowns in order at chosen speeds
- [x] Line-up, tracks, speeds, and volume persist between visits
- [x] Responsive on desktop and mobile; hero image loads from the relative path
- [x] Works on a GitHub Pages project site

*Five dances. One floor.*
