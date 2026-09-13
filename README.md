# Lux — Job Application Assistant

A privacy-first, local-only Chrome extension (Manifest V3) for tracking job applications, analyzing job descriptions against your profile, and generating ATS-friendly resumes on the fly.

No accounts. No cloud. No telemetry. All data stays in your browser.

## Features

- **JD Analyzer** — Open any LinkedIn job posting and see an instant match score with a sidebar breakdown of matched and missing skills.
- **Resume Tailoring** — Generate a truthful, ATS-optimized resume (PDF) for a specific role using your master profile. Two built-in templates: `modern` and `ats-classic`.
- **Application Tracker** — Every application you log is stored locally with metadata (platform, role, resume version, status, notes) and visible in the options dashboard.
- **CSV Export** — Dump the entire application history for spreadsheet analysis.
- **Profile Manager** — One canonical master profile; every tailored resume is derived from it.

## Architecture

Vanilla HTML/CSS/TypeScript. No React, no Vue, no Svelte. Chrome extensions that inject into host pages (LinkedIn, Naukri, etc.) need to be lightweight — a virtual DOM is overkill for a sidebar + popup + options page.

| Layer | Where |
|-------|-------|
| Presentation | `src/popup/`, `src/options/` |
| Business logic | `src/features/*/` (profile, analyzer, resume, tracker) |
| Storage | IndexedDB via Dexie (`src/core/storage/idb.ts`) + `chrome.storage.local` for settings |
| AI | Gemini REST endpoint, called with the user-supplied key (`src/core/api/ai.provider.ts`) |

See [`Documentation/ARCHITECTURE.md`](Documentation/ARCHITECTURE.md) for the full breakdown and [`Documentation/DECISIONS.md`](Documentation/DECISIONS.md) for the ADRs.

## Quick start

```bash
npm install
npm run dev          # Vite dev server with HMR
npm run build        # Produces dist/ — load this folder as an unpacked extension
npm run test         # Vitest, 45 tests
npm run typecheck    # tsc --noEmit, strict mode
npm run lint         # ESLint
```

To load the built extension:

1. `npm run build`
2. Open `chrome://extensions`
3. Enable **Developer mode**
4. Click **Load unpacked** and select the `dist/` directory

## Configuration

The extension needs a **Gemini API key** to run the resume tailoring engine. Get one from [Google AI Studio](https://aistudio.google.com/) and paste it into the Options page. Everything else (profile data, applications, resumes) is stored locally — never sent anywhere.

## Project status

This is **v1.0.0** — the MVP is feature-complete and verified. See [`RELEASE_AUDIT.md`](RELEASE_AUDIT.md) for the verification checklist and [`Documentation/ROADMAP.md`](Documentation/ROADMAP.md) for what's next.

## Tech stack

- TypeScript (strict, no `any`)
- Vite + `@crxjs/vite-plugin`
- Dexie 4 (IndexedDB)
- pdf-lib (PDF generation)
- Zod (runtime schema validation)
- Vitest (testing) + `fake-indexeddb` + `happy-dom`
- ESLint + `typescript-eslint`

## Privacy

Job hunting data is sensitive. Lux:

- Stores everything in your browser (IndexedDB + `chrome.storage.local`).
- Calls the Gemini API directly from the extension with your key — no proxy, no relay.
- Has no analytics, no remote logging, no crash reporting.

## License

[MIT](LICENSE)
