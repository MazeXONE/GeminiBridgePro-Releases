# Sources and implementation accuracy

## Current guide and application source

Version 1.7.4 maintains the offline Guide from current application controls and verified source contracts. The original setup PDF is absent from current assets/distributions. Its credit reads created by [DesignMD Generator](https://designmdgenerator.dev). The **Developer** button opens [developer contact](https://t.me/mazda_dev).

`src/manual.js` maintains the twelve offline chapters; `resources/assistant-prompt.txt` supplies an English assistance reference. The in-app guide and generated assistance text follow the user's selected language. Examples do not establish model access or current account eligibility.

## Upstream contracts

| Source | Purpose and inspected scope |
| --- | --- |
| [magpie-gemini-web](https://github.com/BUKOWSKIREAL/magpie-gemini-web) | Backend installation, checksums, status, Studio accounts, processes, and configuration paths. Inspected pin: `be9ee431fa7ba6e5c8a5db3abbce10c98d8ee998`. |
| [9Router](https://github.com/decolua/9router) | Dashboard nodes, connections, login, keys, models, and routing. Integration contracts exercised against actual npm packages 0.5.18 and 0.5.104 with isolated fixtures. |
| [Connection update](https://github.com/decolua/9router/blob/master/src/app/api/providers/%5Bid%5D/route.js), [dashboard login](https://github.com/decolua/9router/blob/master/src/app/api/auth/login/route.js), [client keys](https://github.com/decolua/9router/blob/master/src/app/api/keys/route.js) | Reference for updating defaultModel/apiKey, dashboard sessions, and key creation. Master source was inspected on 19 October 2026; executed pinned packages remain the tested contract. |
| [AIStudio2API admin API](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/api/admin.go) | Account checks, refresh/deletion, and service controls at the installed backend revision. |
| [Codex configuration](https://developers.openai.com/codex/config-reference/) | Custom providers/profiles, Responses, environment keys, and transport settings. |
| [Tauri prerequisites](https://v2.tauri.app/start/prerequisites/) and [Windows installers](https://v2.tauri.app/distribute/windows-installer/) | Native toolchain, WebView2, executable resources, and packaging. |

## Routing, keys, and account evidence

Version 1.7.4 adds the managed direct Studio Responses node. It keeps typed Responses tool outputs on the tested router hop instead of converting image data to JSON/base64 text. The separate Chat route remains. See [context investigation](T3-CONTEXT-LIMIT.md); fixture results do not prove Google multimodal success on an untested RDP.

Version 1.6.1 standalone Codex exports use the selected key through `experimental_bearer_token`; the applied app profile uses an environment key supplied by its encrypted launcher. The preview masks the key. Fixture checks cover export values without storing raw keys in UI state.

Version 1.1.1 inspected `PUT /api/keys/[id]` in actual 9Router 0.5.104: `{ isActive: boolean }` changes one selected key. The app rereads the registry to confirm persistence. Creation uses `POST /api/keys`; deletion uses `DELETE /api/keys/{id}`. Tests use isolated data/processes/ports, with the [upstream key route](https://github.com/decolua/9router/blob/master/src/app/api/keys/%5Bid%5D/route.js) as a reference.

One Studio provider points to a shared backend pool, rather than one Google account. Earlier [Magpie rotation documentation](https://github.com/BUKOWSKIREAL/magpie-gemini-web/blob/main/README.en.md#multiple-accounts--usage-cards) and the 20 October 2026 read-only inspection described two ready accounts, but retained generation records for only one. Later execution tests separately proved two-account inference and overlap. Their historical scope is preserved in [keys/pool notes](https://github.com/MazeXONE/GeminiBridgePro/blob/main/docs/KEYS-AND-POOL.md) and [execution modes](EXECUTION.md).

## Parallel execution and account controls

Version 1.2.0 follows pinned AIStudio2API [runtime capacity](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/app/lifecycle.go), [worker management](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/app/runtime.go), and [exclusive account leases](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/aistudio/accounts.go). Capacity is applied through the admin API and saved for restart. Manual endpoints must pause shared generation before acquiring leases. Local live reports are historical developer evidence and are excluded from public source packages.

Account controls follow [admin update/login](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/app/admin.go). PUT replaces account settings, so label/proxy/locale/timezone must be preserved when toggling enabled. Direct admin reads include disabled accounts that a helper filters out. Deletion uses the backend API, not GUI-created directory removal, and rereads IDs afterward.

Catalog `models` and `benefit_tier` are metadata, not generation proof. [Account state implementation](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/app/accounts.go) informs the readiness distinction. Account owners still complete Google consent themselves.

## Public access

The Windows managed Caddy pin is [2.11.7](https://github.com/caddyserver/caddy/releases/tag/v2.11.7), archive SHA-256 `0a1edc0b799512051c57071ce0e798d3f2cf67dc98171366f1d2b326072e3b06`. The archive was downloaded and checked in historical QA. IP certificates follow [ACME profiles](https://caddyserver.com/docs/caddyfile/directives/tls#acme) and [default SNI](https://caddyserver.com/docs/caddyfile/options#default-sni); reload follows [Caddy commands](https://caddyserver.com/docs/command-line).

The inspected 9Router 0.5.104 settings API supports `requireApiKey`. Before publishing listeners, the app checks key enforcement and invalid-key rejection. See [public access](PUBLIC-ACCESS.md) for proxy boundaries, firewalls, and remote proof.

## Client guides and translation

The Zed guide follows [OpenAI-compatible endpoint/key configuration](https://zed.dev/docs/ai/use-api-access#openai-compatible-endpoints). It targets the built-in agent; [external agents](https://zed.dev/docs/ai/use-api-access#what-api-access-applies-to) have separate configuration. Selected endpoint/model values and client context settings remain reviewable.

Chatbox integration inspected [host/path normalization](https://github.com/chatboxai/chatbox/blob/main/src/shared/utils/llm_utils.ts), [custom OpenAI providers](https://github.com/chatboxai/chatbox/blob/main/src/shared/providers/definitions/models/custom-openai.ts), and [catalog retrieval](https://github.com/chatboxai/chatbox/blob/main/src/shared/models/openai-compatible.ts). Historical local evidence compared `/models` 404 with `/v1/models` 200 and checked normal/streaming Chat. The actual router catalog uses an internal node ID as `providerAlias`; public client model IDs retain their routing prefix.

Eight application language catalogs are maintained in `lang/`. Original model output, IDs, URLs, keys, and configuration are not translated. Build chunking follows [Rolldown code splitting](https://rolldown.rs/reference/OutputOptions.codeSplitting).

## Process and verification boundaries

- Inspected `studio.mjs start` already backgrounds the service; no foreground terminal is required.
- The Gemini Web script has no stop subcommand. The app stops only verified backend processes, including a matching listener when no PID file exists.
- Codex uses the `gemini-bridge` profile without replacing another default model. Its provider uses Responses and disables unsupported WebSocket transport.
- Model availability, entitlement, quota, and capabilities require backend verification. Example names are not current account facts.
- The model's claimed identity in its answer is insufficient proof. Requested model, endpoint, terminal response state, and client results are recorded separately.
- A running Web backend without account readiness remains attention-needed. Cookie verification is separate from process state.

## Licenses

Application code uses MIT. Tauri uses MIT/Apache-2.0; React and smol-toml use MIT. Distribution includes third-party notices and license copies. Separately installed magpie, AIStudio2API, gemini-web2api-go, and 9Router retain their own upstream licenses and attribution. No MuseBridge source was copied into this application.

## Native updater (1.7.5)

The pinned tauri-plugin-updater 2.14.0 follows the [official Tauri updater](https://v2.tauri.app/plugin/updater/) contract. Signed-version enforcement and the default no-downgrade behavior protect the package/version pairing. The publisher independently verifies the Minisign prehashed Ed25519 artifact signature and trusted comment using Node crypto; the native plugin also verifies before installation. The public verification key is source configuration; signing secrets remain outside Git.
