# Implementation Context: GitHub Pages Migration

## Decisions
- **2026-04-08**: Switched from Serverless Proxy to Direct Fetch for GitHub Pages compatibility.
- **2026-04-08**: Re-evaluated Vercel vs. GitHub Pages. Decided to stick with **GitHub Pages** for better responsiveness (avoiding Vercel's Cold Start and extra network hop).
- **Context**: User wants to host on GitHub Pages.
- **Problem**: Original code used Vercel Serverless Functions (`api/sheets.js`) which are not supported on GitHub Pages.
- **Solution**: 
  - Bypass the local `/api/sheets` proxy.
  - Use GitHub Actions to perform string replacement in `index.html` at build-time.
  - Directly call the `APPS_SCRIPT_URL` from the browser.

## Performance Analysis
- **Benchmark**: GitHub Pages (Direct) vs Vercel (Proxy).
- **Advantage**: Direct calls reduce latency by ~200-500ms and eliminate 1-2s serverless cold starts.
- **Conclusion**: Best for real-time booking feeling.

## Security Note [Speculation]
- **Risk**: The `APPS_SCRIPT_URL` will be visible in the browser's Network tab.
- **Mitigation**: Ensure Google Apps Script itself has appropriate internal authentication or limit its scope if sensitive data exists.

## Target URL substitution
- **Target**: `fetch('/api/sheets?' + qs)`
- **Replacement**: `fetch('${{ secrets.APPS_SCRIPT_URL }}?' + qs)`
