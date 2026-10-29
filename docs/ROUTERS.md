# 9Router and OmniRoute

Open the router workspace and choose **Active router**. The navigation label follows the selection. Existing configurations start with 9Router; switching preserves each router's endpoint, install folder, dashboard session, keys, local process and route evidence.

| Setting | 9Router | OmniRoute |
| --- | --- | --- |
| Pinned npm package | `9router@0.5.104` | `omniroute@3.8.52` |
| Default local endpoint | `http://127.0.0.1:20128/v1` | `http://127.0.0.1:20129/v1` |
| Default app install folder | `tools` | `tools-omniroute` |
| Codex provider | `nine-router` | `omniroute` |
| Launcher environment variable | `NINEROUTER_API_KEY` | `OMNIROUTE_API_KEY` |

OmniRoute requires Node.js 22.22.2 or later in the 22 series, or Node.js 24 through 26. Node.js 24 is suitable for both pinned routers. Install through the application or select an existing npm prefix/package folder. Installation needs internet, can take several minutes and verifies the package files before Start becomes available. Managed OmniRoute stores its database and server secrets in `omniroute-data` inside the Gemini Bridge data folder. An already running external instance keeps its own data location.

1. Select the router. Use **Change local address** if an external instance uses another port. Two processes cannot share a port.
2. Install or select its package folder, then start it. The application only stops processes it started and identified.
3. Sign in to the dashboard from Gemini Bridge. Browser sessions are separate. On a new instance, complete any required dashboard password setup.
4. Choose AI Studio or Gemini Web and the node's API type, then **Connect automatically**. The application tests the backend, creates or updates its local node and connection, registers models, creates or reuses an active client key, and verifies routed Responses.
5. Test Chat, Responses and function calling separately in **Models & tests**. A successful Responses check alone does not prove the other protocols.
6. Copy the active endpoint/model/key to clients. Reapply the Codex profile after switching routers. Existing client configurations and terminals need their endpoint, key and environment updated when changing routers.

The optional managed Responses route is available for both routers and retains the primary Chat node. API key controls create, reveal/copy, select, activate/deactivate and delete keys. OmniRoute's server returns masked values by default. Gemini Bridge encrypts full keys it creates or that you explicitly import. For older server keys, use **Codex → Use your own key**, or enable `ALLOW_API_KEY_REVEAL` on an instance you manage. The app respects the server's reveal setting. Activation and deletion do not require revealing a key. **Forget stored key** removes that selected local copy; deleting a server key revokes the key itself.

Switching is blocked while router setup or an application service job is active. Stop Caddy public access before switching, then save and verify the public configuration for the newly selected upstream. Switching the selection does not stop the other local router.

The integrated controls match the Gemini Bridge 9Router workflow. Provider-specific administration beyond the local Gemini routes remains available through **Open dashboard**.

Verified source contracts: [official OmniRoute repository](https://github.com/cheaper-inference/OmniRoute/tree/release/v3.8.52), [provider model API](https://github.com/cheaper-inference/OmniRoute/blob/release/v3.8.52/src/app/api/provider-models/route.ts), [masked key registry](https://github.com/cheaper-inference/OmniRoute/blob/release/v3.8.52/src/app/api/keys/route.ts), [key mutation API](https://github.com/cheaper-inference/OmniRoute/blob/release/v3.8.52/src/app/api/keys/%5Bid%5D/route.ts).
