# Set up T3 Code manually

The interactive guide is in **Other apps → T3 Code**. Choose the client location and model. Generated TOML follows the chosen endpoint/model and contains no key. The guide does not edit T3 automatically. These instructions reflect the integration documented with Gemini Bridge Pro 1.7.4; T3 labels may vary by version.

## Verify the server route first

On the machine running Gemini Bridge Pro and 9Router:

1. Select AI Studio and an accessible model in **Models & tests**. Keep the pool running and manual account endpoints stopped.
2. Open **9Router → Provider settings → Responses route for Codex & T3**.
3. Keep prefix `studio-responses` or choose a valid distinct prefix. It starts with a lowercase letter and supports lowercase letters, digits, hyphens, and underscores, up to 48 characters; reserved provider names are rejected.
4. Click **Set up Responses route**. Sign in through the app dialog if prompted.
5. Wait for **Responses verified**, inspect the completed nonempty reply, and copy **Model ID for Codex & T3**.

Setup checks backend Responses support, prepares the app-owned node/connection, registers the catalog, prepares an active client key, and verifies a routed reply. The existing `studio/` Chat route remains. Both use the same backend/accounts, without adding quota or context capacity. Retest after changing models.

The additional route's proof is in its setup result. **Models & tests** checks the primary route. Verify T3 tools, images, and streaming separately.

## 1. Choose the endpoint

For T3 and 9Router on the same machine, use the displayed local Base URL, usually `http://127.0.0.1:20128/v1`.

For T3 on another computer, configure **9Router → Public access** on the RDP and use its full HTTPS endpoint, for example `https://api.example.com/v1`. Verify the endpoint from the T3 machine. Loopback refers to the machine running T3's Codex process, not another RDP.

For a remote T3 environment, install the CLI and prepare the configuration/environment variables on that environment's machine. Check router/proxy, ports, certificate, DNS, and firewall when access fails. Do not bypass certificate validation to compensate for a wrong endpoint.

## 2. Install/check Codex CLI

On the T3 environment's machine:

```powershell
codex --version
```

If missing, install Node.js 22+ and then:

```powershell
npm install -g @openai/codex
```

Reopen the terminal and T3 so they inherit PATH, then check the version again. Codex remains optional for other Gemini Bridge Pro client workflows.

## 3. Create a dedicated configuration

In Windows File Explorer, open `%USERPROFILE%`, create `.codex-gemini-bridge-t3`, and save the app's copied TOML there as `config.toml`. In Notepad choose **All files** and **UTF-8**; avoid `config.toml.txt`. Back up any existing configuration and use another dedicated folder if that path is already in use.

The following is an example, valid only after the server route has been created and verified. Use your actual endpoint and full model ID:

```toml
approval_policy = "on-request"
approvals_reviewer = "user"
sandbox_mode = "workspace-write"

model = "studio-responses/gemini-4-argon"
model_provider = "nine-router"

[model_providers.nine-router]
name = "9Router"
base_url = "https://api.example.com/v1"
wire_api = "responses"
requires_openai_auth = false
supports_websockets = false
env_key = "NINEROUTER_API_KEY"
```

The key goes into T3's provider environment below, not this file. Keep `wire_api = "responses"` for the Codex path. A prefix typed into a client does not create a server node or connection.

## 4. Add the T3 provider

Open **Settings → Providers**, choose the target environment, add a **Codex** provider, and select existing CLI/custom configuration if your version offers that choice.

| Field | Value |
| --- | --- |
| Display name | `GEMINI_BRIDGE_SERVER` |
| Binary path | `codex` or its actual executable path |
| CODEX_HOME path | `~/.codex-gemini-bridge-t3` |
| Shadow home path | Empty |
| Launch arguments | Empty |

`~` refers to the user directory on the T3 environment's machine. Match any different folder name from step 3, or use an absolute path. This dedicated CODEX_HOME separates configuration/sessions from other providers. Leave Shadow home empty for this direct custom-home setup.

## 5. Add the active client key

On that provider, open **Environment → Variables → Add variable**. Set name `NINEROUTER_API_KEY`, paste the active key copied from the target 9Router, mark it sensitive/secret if available, and save.

The app's key-copy action uses the router it currently manages. For another server, obtain that server's active client key. Do not use an AI Studio backend key, dashboard password, or Launch arguments for this value.

## 6. Register the model and verify

In the provider's **Models**, add a custom model using the full verified ID, such as `studio-responses/gemini-4-argon`. Save, enable the provider, and refresh status. Start a **new chat**, explicitly choose that provider/model, and send `Reply only OK`.

A completed reply verifies that request and model, not all tools or media capabilities. Changing the model in TOML does not replace an existing chat's selected model. Model catalogs and provider status alone do not establish generation.

After a basic reply, test one small tool result, an image tool result, and streaming as separate checks before a large task. Requests use backend quota.

## Troubleshooting

| Error/symptom | Check |
| --- | --- |
| `config.toml already exists and is not a symlink` | Clear Shadow home and use the dedicated CODEX_HOME directly; preserve old account folders and `auth.json`. Save and refresh. |
| Timeout, `ECONNREFUSED`, TLS | Verify the actual endpoint from the T3 environment, proxy/router state, ports, and certificate. |
| `401` / `403` | Check the active key, exact variable name, provider environment, and target server. |
| `404` / unknown model | Base URL ends `/v1`; full model ID matches the target catalog and chat selection. |
| `No active credentials for provider: studio-responses` | Create/verify the server route and active connection in Gemini Bridge Pro. T3 model registration alone is insufficient. |
| GPT still selected | Register the custom model and explicitly select it in a new chat. |
| Chatbox works but T3 fails | Test Responses, tool results, images, and streaming separately. Chat compatibility is not Responses proof. |
| `context_length_exceeded` after images; new chat works | Read [context recovery](T3-CONTEXT-LIMIT.md); inspect retained history and image-output conversion. |
| `Automatic approval review failed` | Check reviewer/session settings and Launch arguments in the actual CODEX_HOME; this is separate from context overflow. |
| CLI not found | Check `codex --version` on the T3 environment machine and restart T3 after installation. |

Configuration references: [T3 Codex providers](https://github.com/pingdotgg/t3code/blob/main/docs/user/providers-codex.md), [Codex configuration](https://developers.openai.com/codex/config-reference/).
