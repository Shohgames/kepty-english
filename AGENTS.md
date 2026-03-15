# Project Guidelines

## Overview

Kepty English is a mobile-first English learning portal (Pre-Alpha). It fetches data from Google Apps Script (GAS) and renders interactive learning modules in the browser. The app is a single-page application with no build step — all code runs directly in the browser.

## Architecture

- **index.html** — Main application. Contains all HTML and JavaScript (`<script>`) in a single file. This is the only file that renders the UI.
- **app.js** — Google Apps Script (GAS) server-side code. Runs on Google's servers, NOT in the browser. Handles `doGet`/`doPost` for data fetching and audio file storage.
- **index.css** — Styles for the portal.
- **content/** — Local assets (audio samples, etc.).
- **README.md** — Version history and changelog in Japanese.

## Key Concepts

### Learning Modules (tabs)
The app has 8 modules accessible via the footer nav: Vocabulary, Pronunciation, Grammar, Shadowing, Reading, Topic Talk, Speaking Form, Sentence Building. Each module has its own rendering logic inside `renderContent()` and `renderExamStep()`.

### Data Flow
- Data is fetched once on page load from a GAS Web App endpoint and stored in `portalData`.
- All rendering is driven by global state variables: `currentApp`, `currentModeIdx`, `currentSubKey`, `currentTheme`, `examIdx`.

### Audio Playback (Shadowing)
- Uses a single `<audio id="global-player">` element shared across the app.
- `playAudio()` handles pause/resume for the same track and restarts for different tracks.
- `stopAudio()` must be called when switching themes or categories to prevent audio leak.

## Code Style

- This project uses vanilla JavaScript — no frameworks, no npm, no build tools.
- Tailwind CSS is loaded via CDN (`cdn.tailwindcss.com`).
- Icons use Lucide via CDN. After any DOM update, call `lucide.createIcons()`.
- UI text is mostly in English; comments and documentation are in Japanese.
- Keep all app logic inside `index.html` `<script>` tags. Do not split into separate JS modules.
- Inline styles and Tailwind classes are preferred over CSS classes in `index.css`.

## Conventions

- **Variable naming**: camelCase for all JS variables and functions.
- **State management**: All state is global `let` variables at the top of the script block. Do not redeclare with `let`/`const` — reuse existing variables.
- **Versioning**: x.y.z format — x = major release, y = feature release, z = bugfix. Update README.md changelog when making changes.
- **Branch**: Default branch is `main`. Always push to `main`.
- **Git email**: Use GitHub noreply email to avoid privacy errors on push.

## Common Pitfalls

- Do NOT declare `let currentAudioUrl` or similar state variables more than once — this causes "Cannot redeclare block-scoped variable" errors that silently break the entire app.
- Always check for extra/missing closing braces `}` after editing functions — a stray brace will break all JS below it.
- When editing `playAudio()`, remember `stopAudio()` is called from `selectTheme()` and `selectSubKey()` to stop audio on tab switch.
- `app.js` is GAS code — it uses `SpreadsheetApp`, `DriveApp`, `ContentService`. Do not add browser APIs to this file.

## Testing

No automated tests. Test manually by opening `index.html` in a browser (or via GitHub Pages / live server) and verifying each tab works correctly.
