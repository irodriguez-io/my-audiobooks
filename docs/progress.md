# Progress log

## 2026-10-24 — Kickoff

**Established**
- Git repo initialized, with the repo-local identity `isidro@irodriguez.io` (D5). `.gitignore` covers Node, Vite and Playwright output.
- `specs/constitution.md` ratified through a section-by-section interview: snapshot, goals, scope (items 1–15), out of scope, actors, Ubiquitous Language, bounded contexts, constraints C1–C10, assumptions A1–A7, questions Q1–Q7.
- Integration feasibility checked against live endpoints. Archive metadata and MP3s return `Access-Control-Allow-Origin: *`, and MP3s return 206 for Range requests. LibriVox sends no CORS headers, so it is read only at authoring and build time.
- Stack chosen: static GitHub Pages, TypeScript + Vite, at `irodriguez.io/my-audiobooks/` (D1). Books come only from the Library (D2). Listened Chapters are always deleted (D3). A browser that fails screen-locked Auto-Advance is dropped (D4).
- The visual style follows irodriguez.io (C6); the interface is in English and Spanish (C9). Targets are Android Chrome and Firefox (C8).
- Backlog seeded (000–010). Slim `CLAUDE.md`.

**Open**
- Q6 Listening Log retention → 007-listening-log. Q7 skip amounts → 006-lock-screen-controls.
- A1 and A2 are the riskiest assumptions → 000-architecture-spike. Each Screen-Locked Session is an overnight run on Isidro's phone, with the Listening Log exported and shared.
- Public repo `irodriguez-io/my-audiobooks` created on 2026-10-26 and `main` pushed (`eace913`, authored as the verified `isidro@irodriguez.io`). A push over SSH failed only because the passphrase-protected key wasn't in the ssh-agent; it is now loaded from Keychain, and `origin` uses SSH.
