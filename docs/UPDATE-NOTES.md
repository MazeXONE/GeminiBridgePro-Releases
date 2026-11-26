Gemini Bridge Pro 1.8.4

Adds direct in-app updates for both Gemini Bridge Pro (Installer & Portable) and managed 9Router / OmniRoute installations, formatted Release Notes, automatic reconnection for 9Router/OmniRoute, dedicated Studio Responses route sync, and the headless Linux VPS CLI edition.

- **Direct in-app app updates (`Settings → Updates`)**: One-click **Download & install** verifies Ed25519 package signatures and updates both Windows Installer (`Setup.exe`) and Portable (`ZIP`) editions directly inside the app with automatic restart.
- **Managed 9Router / OmniRoute updates (`9Router`)**: Check for newer router versions automatically on start/check or via **Check 9Router updates**, view formatted release notes, and run safe staged updates via **Update 9Router** with automatic rollback protection and `.next-cli-build` timestamp hardening.
- **Clean Release Notes view**: Formatted, scrollable **Release notes** card in **Settings → Updates** and the **9Router** panel with clean headings, lists, and inline code badges.
- **9Router / OmniRoute auto-reconnect**: New **Auto-reconnect when disconnected (including Responses route)** setting automatically restores managed router processes, Chat routes, and dedicated Studio Responses routes (`studio-responses/`).
- **One-click Responses sync**: **Connect automatically** verifies both the primary route and the Studio Responses route together so Guided setup reaches 5/6 immediately.
