# Backlog

Ordered. One feature at a time through `/feature-design` → `/feature-implementation` → `/feature-review`.

| ID | Feature | Status | Notes |
|---|---|---|---|
| 000-architecture-spike | Deployed skeleton and screen-locked playback proof | todo | Architecture-defining. Vite + TS skeleton deployed by Actions to `irodriguez.io/my-audiobooks/` (proves A7). Bare player for Monte Cristo v3 with Auto-Advance and a minimal exportable Listening Log. One 4-hour Screen-Locked Session each on Android Chrome and Firefox (A1; a failing browser is dropped, C8). Cached-Chapter playback with seeking, screen locked: service worker 206 vs IndexedDB (A2, Q2). Settles toolchain and commands, player approach and cache approach as ADRs. |
| 001-library-add-book | Library, `add-book` and build-time Chapter Lists | todo | Library Entry format; `add-book <LibriVox URL>` resolves the exact Archive item and fails loudly (A6); the build saves Chapter Lists (A3). Seeds Monte Cristo v3 (Q3). Goal 3. |
| 002-interface-shell | irodriguez.io design system and bilingual shell | todo | Tokens, glass panels, fonts, rain with reduced motion (C6); English/Spanish catalogs, browser-language default and switch (C9). Lighthouse accessibility ≥ 95. |
| 003-continuous-playback | Player and Auto-Advance | todo | Now-playing card, Chapter List, Auto-Advance, Book Deep Link. Goal 1. |
| 004-resume | Progress and Resume | todo | Per-device Progress; Resume within 10 s in the same Chapter. Goal 2. |
| 005-shelf | Shelf | todo | Every Library Book with this device's Progress; one tap resumes. |
| 006-lock-screen-controls | Lock-Screen Controls | todo | Media Session play/pause, next/previous, skip, cover and title. Settles Q7 (skip amounts). |
| 007-listening-log | Full Listening Log | todo | All Log Events, export, retention. Settles Q6. |
| 008-rolling-cache | Rolling Cache and Listened deletion | todo | Current Chapter + next two; Listened Chapters deleted; playback prefers Cached Chapters. |
| 009-clear-cache | Clear Cache | todo | Storage used; clear per Book or all; never touches Progress. |
| 010-download | Download whole book / remaining chapters | todo | Size before confirm, progress, out-of-space message, persistent storage (A4), one Chapter at a time (C4). |
