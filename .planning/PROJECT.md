# Project: 會議室預約系統 (Meeting Room Booking)

## Vision
一個基於 Google Sheets 作為後端資料庫的輕量化會議室預約系統，提供視覺化格點預約界面。

## Tech Stack
- **Frontend**: Vanilla HTML/JS + Tailwind CSS (via CDN).
- **Backend/DB**: Google Apps Script (GAS) + Google Sheets.
- **Hosting**: GitHub Pages (Initially Vercel, now migrating to Static Hosting).

## Deployment Architecture
- **Environment Management**: Using GitHub Actions to inject secrets at build time.
- **Data Flow**: Frontend (Browser) -> Directly fetch Google Apps Script URL (CORS enabled).
