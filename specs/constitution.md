# my-audiobooks — Constitution

**Status:** Ratified at kickoff, 2026-10-24 · **Owner:** Isidro

This document is binding for vision, scope and vocabulary. Every spec, test and code identifier uses the Ubiquitous Language below. Durable decisions made after kickoff are ADRs in `docs/decisions.md`.

## 1. Project Snapshot

| Field | Content |
|---|---|
| **Name** | my-audiobooks |
| **Summary** | A personal web player, in the visual style of irodriguez.io, that turns a LibriVox audiobook into one continuous playlist: chapters advance on their own, playback keeps going with the phone screen off, and it resumes where I left off. |
| **Primary business problem** | LibriVox chapters are separate files, so every chapter has to be started by hand. That breaks hands-free, screen-off listening. |
| **Primary users** | Isidro, the only listener, mostly on an Android phone (Chrome and Firefox), sometimes on a laptop |
| **Key stakeholders** | Isidro (owner). External: LibriVox (catalog and book pages) and the Internet Archive (audio files and metadata API). |
| **Delivery context** | Personal side project. Static site on GitHub Pages at `https://irodriguez.io/my-audiobooks/`, from a public repo: TypeScript + Vite, no backend, sharing the irodriguez.io design language. Claude orchestrates; Codex implements. |

## 2. Goals And Success Measures

| Goal | Why It Matters | Success Metric | Target |
|---|---|---|---|
| Hands-free continuous listening | It is the core problem: no tapping between chapters | Share of chapter transitions that start with no tap, including with the screen locked on Android Chrome and Android Firefox | 100% over a 4-hour Screen-Locked Session on each browser, shown by an exported Listening Log |
| Resume where I left off | Long books (Monte Cristo is 54 h over 117 chapters) take weeks of sittings | Gap between where I stopped and where playback resumes when I reopen the book on the same device and browser | Same chapter, within 10 s of where I stopped |
| Every Library book plays | The app is only useful if the books I add actually work | Share of Library Entries that `add-book` resolves to the exact Archive item and that the build turns into a playable Chapter List | 100%; when a book cannot be resolved, `add-book` or the build fails loudly and the book is never published broken |
| Looks and feels like irodriguez.io | All my projects should look polished and consistent | Visual match to the site's design tokens and components, plus accessibility, in English and Spanish | Isidro signs off on the design; Lighthouse accessibility ≥ 95; WCAG AA contrast |

## 3. In Scope

Integration feasibility was checked on 2026-10-24 against live endpoints. Internet Archive metadata (`archive.org/metadata/<id>`) and MP3 downloads return `Access-Control-Allow-Origin: *`, and MP3s answer Range requests with 206. LibriVox pages and the LibriVox API send no CORS headers. Because books come only from the Library, LibriVox and the Archive metadata API are read only by `add-book` and the build. The browser reads only the Library data built into the site and the Archive's MP3s and covers.

1. **Library.** The set of Books kept in the repo, one Library Entry per Book, holding the Archive Identifier and the LibriVox URL. The `add-book <LibriVox URL>` script reads the LibriVox page, resolves the exact Archive item and writes the entry. A merged PR to `main` redeploys the site with the Book. This is the only way a Book enters or leaves the app.
2. **Chapter List.** The build reads each Library Book's Archive metadata and saves its Chapter List into the site: Chapters in track order, each with its title and duration. Audio is the 64 kbps MP3, the same file LibriVox links to.
3. **Continuous playback.** When a Chapter ends, the next starts on its own, including with the screen locked on Android Chrome and Android Firefox.
4. **Lock-Screen Controls.** Through the Media Session API: play/pause, next and previous Chapter, skip back and forward, with the cover and Chapter title on the lock screen.
5. **Resume.** Each Book's Current Chapter and Position are saved in this browser on this device and restored when the Book is reopened. Progress does not sync between devices.
6. **Book Deep Link.** Each Library Book has its own URL (`/my-audiobooks/?book=<Archive Identifier>`) for bookmarking.
7. **Shelf.** The home page lists every Library Book with this device's Progress ("Chapter 12 of 117"). One tap resumes.
8. **Listening Log.** An on-device record of timestamped Log Events: chapter started, chapter ended, Auto-Advance, pause, visibility hidden or visible, and errors. It can be exported as a file for diagnosis, starting with the 4-hour Screen-Locked Session.
9. **Designed interface.** The irodriguez.io design system, built for the phone first and responsive on a laptop.
10. **Bilingual interface.** All interface text in English and Spanish. The language follows the browser, with a switch like irodriguez.io's. Book and Chapter titles stay as published on LibriVox.
11. **Rolling Cache.** The device keeps the Current Chapter and the next two. When a Chapter starts, any missing Chapters in that window are fetched. Playback uses the Cached Chapter whenever there is one.
12. **Download.** Two buttons, and nothing downloads until one is tapped: *Download whole book* fetches every Chapter; *Download remaining chapters* fetches from the Current Chapter to the end. Each shows the size before you confirm, then shows progress, and a clear message if storage runs out. Downloads run while the app is open.
13. **Clear Cache.** Shows how much space Cached Chapters use, and clears them for one Book or for all Books. Never touches Progress.
14. **Listened Chapters are deleted.** One rule covers every Cached Chapter, from the Rolling Cache or a Download: once a Chapter is Listened, it is deleted from the device. Chapters ahead of the Current Chapter are never deleted by the Rolling Cache. Going back to a deleted Chapter streams it again.
15. **Deployment.** A GitHub Actions build that publishes to GitHub Pages at `irodriguez.io/my-audiobooks/` on every merge to `main`.

## 4. Out Of Scope

| Fenced out | Why |
|---|---|
| Adding, opening or removing Books from inside the app, including pasting an archive.org or LibriVox link | Decided at kickoff (Q5): Books come only from the Library, through a PR |
| YouTube playlists | Mobile browsers pause YouTube with the screen locked unless the account has Premium, and extracting only the audio violates YouTube's terms |
| Audio sources other than LibriVox recordings hosted on the Internet Archive (Audible, Libby, own files, other archive.org audio) | Keeps one verified integration |
| Progress sync between devices, accounts, sign-in, any backend | Sync is not needed; static hosting stays free and simple |
| Other users, sharing, social features | One listener |
| Browsing or searching the LibriVox catalog in the app | Books come from the Library |
| Keeping Listened Chapters on the device | Decided: Listened Chapters are always deleted |
| Background download (download continuing after the app is closed) | Heavier; Downloads run while the app is open |
| Playback speed control | Deferred; can come back as a backlog item |
| Sleep timer | Deferred; can come back as a backlog item |
| Home-screen install (PWA manifest) | Not chosen at kickoff; can come back as a backlog item |
| Translating Book or Chapter titles | They stay as published on LibriVox |
| Bookmarks, notes, transcripts, text sync | Not part of the problem |
| Analytics or telemetry sent anywhere | The Listening Log stays on the device until it is exported |
| Native app or app-store packaging | It is a web app; there are no store builds |

## 5. Users, Actors, And Their Jobs

| Actor | Kind | Job | Notes |
|---|---|---|---|
| Listener | Human (Isidro) | Listen to a Book hands-free, Resume where I left off, and decide what is kept on the device | Main actor in every playback scenario |
| Library Maintainer | Human (Isidro) | Add or remove a Book in the Library: run `add-book` or edit the entry, open a PR, merge to `main` | Same person, separate role, so Library scenarios read clearly |
| Internet Archive metadata API | External system | Provide an item's chapter files (name, title, track, length), the Book title, reader and cover | Read by `add-book` and the build only |
| Internet Archive download servers | External system | Serve Chapter MP3s and covers, with Range and CORS support | Redirect to mirror hosts (`dnNNN.*.archive.org`) |
| LibriVox website | External system | Supply the book page that maps a LibriVox URL to its exact Archive item | Read only by `add-book`, never by the browser |
| Browser media platform (Android Chrome and Firefox) | External system | Keep audio playing with the screen locked, and relay Lock-Screen Controls | The main risk behind the 4-hour goal |
| Browser storage | External system | Keep Progress, the Listening Log and Cached Chapters | Can evict data when the device is low on space; the app must recover cleanly |
| GitHub Actions and Pages | External system | Build the site with each Library Book's Chapter List, and publish on every merge to `main` | |

## 6. Ubiquitous Language

These terms are binding in specs, tests and code identifiers. Do not use synonyms.

| Term | Definition | Avoid Saying | Notes |
|---|---|---|---|
| Book | One LibriVox recording of a work in the Library, identified by its Archive Identifier | audiobook (in code), item, album, playlist | Different recordings of a work (Monte Cristo v1–v3) are different Books |
| Archive Identifier | The Internet Archive item id of a Book, e.g. `count_montecristo_1308_librivox` | id, slug, item id | The key for Progress, cache and the Book Deep Link |
| LibriVox URL | A Book's page on librivox.org | link, source URL | Only `add-book` reads it |
| Chapter | One audio file of a Book, in track order | track, section, part, episode, file | LibriVox says "section" and the Archive says "track"; translate only at the integration edge |
| Chapter List | A Book's Chapters in order, with title and duration | playlist, tracklist | Built into the site by the build |
| Current Chapter | The Chapter being played, or the one to Resume | active track, now playing | |
| Position | Seconds into the Current Chapter | offset, timestamp, time | |
| Progress | A Book's Current Chapter and Position on this device | bookmark, state, save | Per device and browser; never synced |
| Resume | Start playback at the saved Progress | restore, continue | |
| Auto-Advance | Start the next Chapter when one ends, with no tap | autoplay, skip, next | The core behaviour |
| Listened | A Chapter that played to its end or that the Listener moved past | finished, played, done | Triggers deletion of its Cached Chapter |
| Library | The Books kept in the repo and built into the site | catalog, collection | Changes only by a PR to `main` |
| Library Entry | One Book's record in the Library: Archive Identifier and LibriVox URL | manifest, config | Written by `add-book` |
| Book Deep Link | `/my-audiobooks/?book=<Archive Identifier>` | permalink, share link | |
| Shelf | The home page: every Library Book with its Progress | home, dashboard, list | |
| Cached Chapter | A Chapter's audio stored on the device | offline file, download (as a noun) | |
| Rolling Cache | Automatic caching of the Current Chapter and the next two | prefetch, buffer | |
| Download | Caching started by the Listener: *Whole Book* or *Remaining Chapters* | sync, save offline | Its Cached Chapters are still deleted once Listened |
| Clear Cache | Deleting Cached Chapters for one Book or all Books | purge, reset | Never touches Progress |
| Listening Log | The on-device, exportable record of Log Events | telemetry, analytics, debug log | |
| Log Event | One timestamped entry in the Listening Log | event, trace | e.g. chapter started, Auto-Advance, visibility hidden |
| Lock-Screen Controls | Media Session controls on the lock screen and in the notification shade | media controls, remote | |
| Screen-Locked Session | A real-time test run with the phone screen locked, proved by an exported Listening Log | soak test, overnight test | The 4-hour goal is measured with one per browser |

## 7. Bounded Contexts

| Context | Owner | Purpose | Owned Concepts | Inputs | Outputs |
|---|---|---|---|---|---|
| Library | This product | Keep the curated Books in the repo and build their Chapter Lists into the site | Library, Library Entry, Archive Identifier, Chapter List, `add-book` | LibriVox URL; Archive metadata (authoring and build time) | Library data with Chapter Lists, built into the site |
| Playback | This product | Play Chapters continuously, including with the screen locked | Current Chapter, Position, Auto-Advance, Listened, Lock-Screen Controls | Chapter List, Cached Chapter or stream URL, Progress to Resume | Position updates, Listened signals, Log Events |
| Progress | This product | Remember where each Book stands on this device and show it on the Shelf | Progress, Resume, Shelf, Book Deep Link | Library data; Position updates and Listened signals from Playback | Resume point, Shelf entries |
| Offline Storage | This product | Hold Chapter audio on the device and enforce the deletion rule | Cached Chapter, Rolling Cache, Download, Clear Cache | Current Chapter, Listened signals, Download and Clear Cache requests | Cached audio for Playback, storage usage, Log Events |
| Diagnostics | This product | Record and export what happened during listening | Listening Log, Log Event, Screen-Locked Session | Log Events from Playback and Offline Storage | Exported Listening Log file |
| Internet Archive | External | Host LibriVox audio, covers and item metadata | item, file, track (their terms) | — | Metadata JSON (build time), MP3s with Range support, covers |
| LibriVox | External | Publish book pages | section, book page (their terms) | — | Book page HTML (authoring time only) |
| Browser platform | External | Media playback, Media Session, storage, service worker | audio element, Cache Storage, localStorage/IndexedDB | — | Background playback, lock-screen events, quota and eviction |
| GitHub | External | Repo, PRs, Actions build, Pages hosting | — | Merges to `main` | Published site at `irodriguez.io/my-audiobooks/` |

## 8. Business Constraints And Assumptions

### Constraints

| # | Constraint | Kind |
|---|---|---|
| C1 | No recurring cost: GitHub Pages and GitHub Actions on the free tier, no paid services, no backend | Commercial |
| C2 | The repo is public: no secrets, no personal data, no audio | Operational |
| C3 | Audio and covers are streamed and cached from archive.org, never re-hosted in the repo or on Pages | Commercial / good citizenship |
| C4 | Use the Internet Archive politely: Downloads fetch one Chapter at a time, and `add-book` and the build send a descriptive User-Agent | Operational |
| C5 | No data leaves the device except requests to archive.org; no analytics, no third-party scripts | Privacy |
| C6 | The irodriguez.io design system is binding: navy-to-indigo gradient (`#070B2B` → `#2B2170`), whitesmoke text, `#877ad7` accent, dark glass panels (blur, 16px radius, faint violet edge), Ubuntu and Sacramento, the CSS rain animation, reduced-motion support, WCAG AA contrast | Product |
| C7 | Served at `irodriguez.io/my-audiobooks/`: the repo is named `my-audiobooks` and every asset path respects the `/my-audiobooks/` base | Operational |
| C8 | Primary targets are current stable Chrome and Firefox on Android. Desktop Chrome, Firefox and Safari are secondary. iPhone is not a target. A primary browser that fails Auto-Advance with the screen locked is dropped rather than worked around (Q1) | Platform |
| C9 | All interface text ships in English and Spanish; no text is hard-coded outside the translation catalogs | Product |
| C10 | Claude orchestrates and Codex implements, through `/feature-design` → `/feature-implementation` → `/feature-review` | Operational |

### Assumptions

`// review` marks the risky ones to validate early.

| # | Assumption | Validated by |
|---|---|---|
| A1 | Android Chrome and Android Firefox keep an audio-playing page alive with the screen locked for 4 hours, and Auto-Advance on `ended` still works in the background `// review` | Architecture spike: one Screen-Locked Session per browser. A browser that fails is dropped (C8); if both fail, the product premise fails and the project stops for a rethink |
| A2 | Cached Chapters can be played back with seeking, with the screen locked, on both browsers: through a service worker answering Range requests with 206 from Cache Storage, or, if that fails, from audio stored in IndexedDB (Q2) `// review` | Architecture spike |
| A3 | Every LibriVox item on the Archive has a 64 kbps MP3 for each Chapter, and the `track` field gives the right Chapter order and titles `// review` | `add-book` and the build, starting with the first Library Book |
| A4 | Chrome and Firefox on the phone allow a Whole Book Download (about 1.5 GB for the longest Books) without eviction once persistent storage is granted. Firefox asks permission the first time | Download feature, on the phone, on both browsers |
| A5 | archive.org endpoints stay free, open to cross-origin requests, and keyless | Checked 2026-10-24; failures show in the Listening Log and the build |
| A6 | The LibriVox book page keeps linking to its Archive item, which `add-book` depends on | `add-book` fails loudly when the link is missing |
| A7 | A project repo under `irodriguez-io` is served at `irodriguez.io/my-audiobooks/` automatically, because the user site has the custom domain | First deploy |

## 9. Open Questions

### Resolved at kickoff

| # | Question | Decision |
|---|---|---|
| Q1 | If Auto-Advance fails with the screen locked on one browser, work around it or drop the browser? | Drop the browser (C8) |
| Q2 | If a service worker cannot serve seeks from cached audio, may Cached Chapters live in IndexedDB instead? | Yes (A2) |
| Q3 | Which Books seed the Library? | The Count of Monte Cristo (version 3), read by David Clarke: `https://librivox.org/the-count-of-monte-cristo-version-3-by-alexandre-dumas/` → `count_montecristo_1308_librivox` |
| Q4 | Interface language? | English and Spanish (In Scope item 10, C9) |
| Q5 | Can Books be added or removed from inside the app? | No. Books come only from the Library through a PR; pasted links are Out Of Scope |

### Still open

| # | Question | Blocks | Proposed default |
|---|---|---|---|
| Q6 | How much Listening Log history is kept on the device? | Diagnostics feature | The last 7 days, oldest Log Events dropped first; revisit after the first Screen-Locked Session shows real log size |
| Q7 | Skip-back and skip-forward amounts for the player and Lock-Screen Controls? | Lock-Screen Controls feature | Back 15 s, forward 30 s |
