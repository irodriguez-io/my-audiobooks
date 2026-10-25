# my-audiobooks

A personal web player that turns a LibriVox Book into one continuous playlist,
with Auto-Advance, screen-locked playback and Resume, served at
irodriguez.io/my-audiobooks/. Constitution: specs/constitution.md (binding
vocabulary and scope). Backlog: specs/backlog.md. Decisions: docs/decisions.md.

## Commands
Stack: TypeScript + Vite, Vitest, Playwright, GitHub Actions → Pages (D1).
Exact commands are set by 000-architecture-spike; record them here when it lands.

## Workflow
- Claude orchestrates; Codex (via herdr) implements. Claude never writes product code.
- All feature work runs the pipeline: /feature-design (spec BDD + slice plan +
  plan-mode approval) → /feature-implementation (Codex slices, failing tests first)
  → /feature-review (independent gate + merge).
- One feature at a time. Never edit tests to make them pass.
- Use the exact terms from the constitution's Ubiquitous Language. No synonyms.
- Do not implement anything listed Out Of Scope in the constitution.
- Append an ADR to docs/decisions.md (next D-number) when a durable decision is made.
- Update docs/progress.md at the end of every working session.

## Hard rules
- Public repo: no secrets, no personal data, no audio files (C2, C3).
- The browser talks only to archive.org's download servers. LibriVox and the
  Archive metadata API are read only by `add-book` and the build (C5).
- Every asset path respects the `/my-audiobooks/` base (C7).
- No interface text outside the English and Spanish catalogs (C9).
- The irodriguez.io design system is binding (C6); reference the site's
  `css/base.css` and `css/main.css` in irodriguez-io/irodriguez-io.github.io.
- Screen-locked behaviour is proven only by an exported Listening Log from a real
  Android device, never by CI alone.

## Code style
- Functional core, imperative shell: pure transformations; audio element, storage,
  service worker, network and DOM only at the edges.
- Immutable data: `readonly` types; TypeScript `strict: true`.
- Explicit data flow: pass dependencies, config and clock in. No hidden globals.
- Comments explain why, not what.
