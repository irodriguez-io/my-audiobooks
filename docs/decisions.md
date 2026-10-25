# Decisions

Architecture Decision Records, newest last. Each entry: date, decision, context, consequences.

## D1 — Static site on GitHub Pages, TypeScript + Vite, no backend (2026-10-24)

**Decision:** A static site built with TypeScript and Vite, no UI framework, tested with Vitest and Playwright, deployed by GitHub Actions to Pages at `irodriguez.io/my-audiobooks/` from the public repo `irodriguez-io/my-audiobooks`.
**Context:** One listener, no sync. Internet Archive MP3s allow cross-origin access with Range support. The interface is small, so a framework adds weight without value. "No framework" still means a fully designed interface (C6).
**Consequences:** No running cost. Anything that needs a server, such as reading LibriVox, happens at authoring or build time.

## D2 — Books come only from the Library (2026-10-24)

**Decision:** A Book enters or leaves the app only through a Library Entry merged to `main`. There is no pasted-link loading.
**Context:** LibriVox sends no CORS headers, and Archive search by title is ambiguous (three Monte Cristo recordings). `add-book` resolves the exact item outside the browser.
**Consequences:** No runtime search and no Recording Picker. The browser reads only built-in Library data and archive.org's download servers.

## D3 — Listened Chapters are always deleted (2026-10-24)

**Decision:** Every Cached Chapter, including one from a Download, is deleted once Listened.
**Context:** Isidro, as the only Listener, prefers low storage use to keeping finished Chapters.
**Consequences:** One deletion rule, no pinning. Re-listening streams again.

## D4 — A primary browser that fails screen-locked Auto-Advance is dropped (2026-10-24)

**Decision:** If Android Chrome or Firefox can't Auto-Advance with the screen locked, it is dropped rather than worked around. If both fail, the project stops for a rethink.
**Context:** Keeps the player approach simple, with no per-browser workarounds.
**Consequences:** The architecture spike's Screen-Locked Sessions decide the supported browsers.

## D5 — Public repo, committed as isidro@irodriguez.io (2026-10-24)

**Decision:** The repo is public. Commits use `Isidro Rodriguez <isidro@irodriguez.io>`, set repo-locally. The address forwards to Isidro's personal inbox and is verified on the irodriguez-io GitHub account.
**Context:** GitHub Pages from a private repo needs GitHub Pro, and the content is public domain. A domain address keeps the personal Gmail out of public history and matches the irodriguez.io brand.
**Consequences:** The Library list and the domain address are public. The first push waits until the address is verified on GitHub, so commits link to the profile.
