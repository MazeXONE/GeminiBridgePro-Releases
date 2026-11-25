Gemini Bridge Pro 1.8.4

Adds automatic reconnection for 9Router/OmniRoute and the dedicated Studio Responses route (`studio-responses/`) when routes become unverified, 24-hour proofs expire, accounts or models change, or the local router process stops.

- New **Auto-reconnect when disconnected (including Responses route)** setting in **9Router → Backend & connection** and **Settings → General**.
- One-click **Connect automatically** dialog option to verify both the primary Chat/Responses route and the dedicated Studio Responses route together so Guided setup reaches 5/6 immediately.
- Exponential retry backoff and manual-stop protection prevent unwanted background loops when upstream quota is exhausted or the router is stopped intentionally.
- Preserves all existing accounts, client keys, Magpie v0.13.0 backends and UI workflows.

Windows installer users can install over the existing installation with the same Windows account and location. Portable users should extract the entire new ZIP. Account data and settings remain in their existing data folder.
