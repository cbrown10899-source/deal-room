# Deal Room

A private, single-file web app for vetting business deals against the checklist
prominent family offices use. No accounts, no server, no API keys, no cost — the
analysis is done by the user's **own Claude**. Everything else lives in the
browser's local storage on the device it runs on.

## What it does

- **Installable app** — a single HTML file. Add to Home Screen on a phone (runs
  full-screen via the web manifest + Apple meta tags), open as a file on a
  computer, or drop into Claude and let Claude open it in its browser.
- **Pipeline** — deals grouped by stage (Screening → Diligence → Passed / Invested).
- **Drop the raise files first** — drag-drop (or tap) the pitch deck, financials,
  term sheet onto a deal. Stored locally in IndexedDB, never uploaded by the app.
- **Analyze with your own Claude** — "Copy instructions & open Claude" copies a
  self-contained family-office analyst brief (the weighted scorecard + red-flag
  list + scoring rules + a JSON output shape) and opens claude.ai. The user
  attaches the file in Claude and pastes; Claude returns the scored report and
  asks for anything missing. On mobile, "Send files to Claude app" uses the Web
  Share API. No API key, no paid API calls anywhere in the app.
- **Save the result** — paste Claude's reply back; the app extracts the JSON
  block, sanitizes it, stores it, and shows the scored report with a weighted
  0–100 grade (A–F). Any red-flag deal-killer caps the grade at 49.

## Running it

- Locally: `node serve.js` then open http://localhost:8474 — or just open
  `index.html` directly in a browser.
- The whole app is `index.html`; `serve.js` is only a convenience for local preview.

## Handing it to someone

The gift package is in `../deal-room-handoff/`: `Deal Room.html` (a copy of this
app) + `START HERE.txt` (a plain-language guide covering phone/computer/Claude
use and the vetting workflow). Send both files. For reliable phone home-screen
install, host the file behind a link (e.g. Cloudflare Pages).

## Data & backup

Deals and notes live in IndexedDB + localStorage per browser/device; attached
files live in IndexedDB. Settings → "Export all deals" backs up deals, notes, and
saved reports to one JSON file (attachments are not included). Clearing site data
clears the app.

## Design notes (hardened)

- Ids from imported files are sanitized (`safeId`) before use in inline handlers;
  AI-returned criterion/flag ids are validated against the fixed enums — both
  close XSS-via-untrusted-input in click handlers.
- Debounced saves are tracked per-deal (and flushed on `beforeunload`) so a rapid
  edit to one deal can't drop another's pending write.
- Storage-init failure shows a friendly message instead of a blank page.
