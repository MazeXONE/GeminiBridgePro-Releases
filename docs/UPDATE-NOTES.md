Gemini Bridge Pro 1.8.4

Adds direct in-app updates for both Windows Installer and Portable editions, formatted Release Notes, automatic reconnection for 9Router/OmniRoute, dedicated Studio Responses route sync, and the headless Linux VPS CLI edition.

- **Direct in-app updates (`Settings → Updates`)**: One-click **Download & install** verifies Ed25519 package signatures and updates both Windows Installer (`Setup.exe`) and Portable (`ZIP`) editions directly inside the app with automatic restart.
- **Clean Release Notes view**: Formatted, scrollable **Release notes** card in **Settings → Updates** with clean headings, lists, and inline code badges.
- **9Router / OmniRoute auto-reconnect**: New **Auto-reconnect when disconnected (including Responses route)** setting automatically restores managed router processes, Chat routes, and dedicated Studio Responses routes (`studio-responses/`).
- **One-click Responses sync**: **Connect automatically** verifies both the primary route and the Studio Responses route together so Guided setup reaches 5/6 immediately.
- **Preserves existing setup**: Keeps all existing Google accounts, client keys, Magpie `v0.13.0` backends, and settings in your data folder.
