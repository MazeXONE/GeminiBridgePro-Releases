Gemini Bridge Pro 1.8.0

Choose 9Router or OmniRoute from the Active router selector. Existing configurations keep 9Router by default.

- Both routers have installation, start/stop, dashboard login, automatic local provider/model setup, client key controls and API tests inside Gemini Bridge.
- Each router keeps separate settings, encrypted credentials, process ownership and route verification. OmniRoute defaults to port 20129 so both routers can run side by side.
- OmniRoute's masked key registry, PATCH updates and provider-model catalog use dedicated adapters. Keys created or explicitly imported in the app remain available through encrypted storage.
- Codex and T3 configurations follow the active router. Reapply the Codex profile and update existing client endpoints/keys after switching.
- Stop Caddy public access before switching routers, then save and verify its upstream configuration again.
- OmniRoute 3.8.52 requires Node.js 22.22.2+ in the 22 series, or Node.js 24 through 26.

Verified with the actual router packages, local deterministic API fixtures, browser UI checks and the built Windows application. These checks do not claim live Google quota or authentication verification.

Windows installer users can install over the existing installation with the same Windows account and location. Portable users should extract the entire new ZIP. Account data and settings remain in their existing data folder.
