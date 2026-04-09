# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **meeting room booking system** (住商總部會議室預約系統) for a corporate office. The entire frontend is a single file: `index.html` (~1700 lines of vanilla HTML/CSS/JS). There is no build step — changes to `index.html` are deployed directly.

## Development

**Local preview:**
```bash
npx serve .
```

**Deploy:** Push to `main` — GitHub Actions injects the secret API URL and deploys to GitHub Pages automatically.

There are no lint, test, or build commands.

## Architecture

```
Browser → fetch(APPS_SCRIPT_URL) → Google Apps Script → Google Sheets
```

- **Frontend:** Single `index.html` — Tailwind CSS (CDN), vanilla JS, no framework
- **Backend:** Google Apps Script web app (external, not in this repo)
- **Database:** Google Sheets (managed by the Apps Script)
- **Hosting:** GitHub Pages

### The `APPS_SCRIPT_URL` secret

`index.html` contains the placeholder `const APPS_SCRIPT_URL = ...`. During deployment, GitHub Actions performs a `sed` replacement to inject the real URL from the `APPS_SCRIPT_URL` repository secret. **Never hardcode the real URL in source.** For local development, you must manually set this variable in a local copy of the file (do not commit).

### `index.html` structure

The script section is organized in this order:
1. Constants & room/time configuration (line ~347)
2. App state (`state` object)
3. `apiCall()` — all backend communication
4. Auth helpers
5. Date/time utilities
6. View renderers: `renderBookView`, `renderOverviewView`, `renderMyBookingsView`, `renderReportView`, `renderAdminView`
7. Modal handlers (booking create/edit/detail)
8. Event handlers & `init()`

### Key implementation details

- **Timezone:** Always use local date methods (`getFullYear()`, `getMonth()`, `getDate()`, `getHours()`) — never UTC methods. Taiwan is GMT+8 and there is no timezone conversion logic.
- **Booking IDs:** Bookings use a `_uiId` field for frontend state and conflict detection. This is distinct from the backend row ID.
- **Conflict detection:** `getConflicts()` uses `timesOverlap()` to check overlapping bookings across rooms.
- **Lunch break:** 12:00–12:30 is excluded from bookable slots.
- **Auth:** Controlled by `REQUIRE_LOGIN` constant. Session is stored in `sessionStorage` as `mrb_session`.

### Room configuration

Defined in `index.html` around line 349. Physical rooms (3F, 13F, 15F) and ZOOM rooms (ZOOM_00/01/02) are configured there.

### API actions

All backend calls go through `apiCall({ action, ...params })`. Actions: `getAll`, `login`, `addBooking`, `editBooking`, `deleteBooking`, `addUser`, `editUser`, `deleteUser`.

## Planning docs

- [`.planning/PROJECT.md`](.planning/PROJECT.md) — vision and requirements
- [`.planning/CONTEXT.md`](.planning/CONTEXT.md) — technical decisions (explains the Vercel → GitHub Pages migration)
- [`.planning/STATE.md`](.planning/STATE.md) — development progress
