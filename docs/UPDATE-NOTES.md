Gemini Bridge Pro 1.8.3

Updates the pinned Magpie backend revision to v0.13.0 (AIStudio2API v0.3.5-magpie.1 with SpiderMonkey WAA and gemini-web2api-go v4.28.0-magpie.1) while preserving all existing accounts, settings and routes.

- Native Windows credential reads now decrypt DPAPI entries directly in the desktop host for each operation instead of launching PowerShell on every status snapshot.
- WebView2 runs with consolidated in-process network/utility services, a single renderer limit and disabled browser background bloat while preserving all CSS transitions, splash animations and DevTools verification hooks.
- Automatic working-set trimming releases cold startup and inactive pages across the desktop host, WebView2 child processes and managed Node.js router services.
- Manual endpoint ownership and recovery from 1.8.1 and selectable 9Router/OmniRoute support from 1.8.0 remain intact.

Windows installer users can install over the existing installation with the same Windows account and location. Portable users should extract the entire new ZIP. Account data and settings remain in their existing data folder.
