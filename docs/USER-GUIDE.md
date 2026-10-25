# Gemini Bridge Pro user guide

Application reference: **1.7.6**. Updated **25 October 2026**. Menu names below follow the default English interface.

[Install](#install-and-open) · [First setup](#first-setup) · [Clients](#connect-your-client) · [Parallel requests](#single-and-parallel-pool) · [Manual endpoints](#dedicated-account-endpoints) · [RDP](#public-https-and-rdp) · [Troubleshooting](#troubleshooting)

## Install and open

For Windows x64, download the version-matched Setup EXE or portable ZIP from the repository's Releases section after publication. Compare its SHA-256 with the release's `SHA256SUMS.txt`. Run Setup, or extract the entire ZIP and open `GeminiBridgePro/GeminiBridgePro.exe`. Keep its backend folder and supplied DLL beside the EXE.

Install Node.js **22+**, Git, and WebView2. Codex CLI is optional unless you choose a Codex workflow, including T3's Codex provider. You need internet for backend/browser downloads and your own eligible Google account. Accounts, quota, and subscriptions are not supplied by this application.

For source installation and native build requirements, use [BUILD.md](BUILD.md). Building needs Node.js **22.12+**, Rust, and OS-specific development tools.

The app starts in English. The toolbar language picker offers English, Indonesia, Russian, Chinese, Japanese, Korean, Spanish, and German, and remembers your choice. It translates app controls and the offline Guide; original model output, custom prompts, account names, model IDs, and technical configuration retain their contents.

## Understand the connection

```text
Compatible client → 9Router → AI Studio backend/pool → an eligible account
```

Gemini Bridge Pro manages setup, accounts, routing, client keys, model tests, capacity, and configuration guidance. The AI Studio backend handles eligible model requests. 9Router remains part of the routing architecture. Your coding client owns terminal tools, edits, and agent orchestration.

| Route | Usual Base URL | Credential | Model ID |
| --- | --- | --- | --- |
| Direct AI Studio pool | `http://127.0.0.1:2048/v1` | AI Studio backend key | Backend ID without `studio/` |
| 9Router Chat route | `http://127.0.0.1:20128/v1` | Active 9Router client key | Full ID such as `studio/<model>` |
| Additional Studio Responses route | Same 9Router Base URL | Active 9Router client key | Verified ID such as `studio-responses/<model>` |
| Dedicated account endpoint | Copy from that account's endpoint controls | Copy that account's key | Backend ID without `studio/` |
| Public client → RDP | Your verified `https://api.example.com/v1` | Active key from that RDP's 9Router | Full ID registered on that server |

Defaults are examples. Read the actual values from your installation. Backend keys, dashboard/admin access, and client keys have different roles and cannot be interchanged.

## First setup

### 1. Check prerequisites

Open **Guided setup**. Check Node.js and Git, then install or select 9Router. Its managed version is pinned by the application. Codex is an optional final setup step.

After installing Node.js or Git on Windows, click **Recheck prerequisites**. The native helper reads the updated Windows PATH without restarting Gemini Bridge Pro. If a tool still fails, inspect the displayed installation path and version rather than repeatedly reinstalling it.

### 2. Install backends

Review locations in **Settings** and run the backend installation actions. Complete components are skipped. Wait for verification before continuing. Changing a configured installation location does not automatically move or delete an older installation.

AI Studio is the primary workflow. Gemini Web is an optional cookie-based fallback; follow its separate account instructions if you need it. Never send cookies to another person or include them in Git.

### 3. Sign in to AI Studio

Keep **Manual account endpoints** stopped. Open **Gemini accounts → AI Studio**, start the service, and choose **Add Google account**. Complete login and required consent in the browser opened by the app, then wait for synchronization. Add other existing accounts in the same way.

The account controls allow reauthentication, pause/enable, checks, and deletion. Deletion requires confirmation and removes the backend's local account/session; it does not delete your Google account. Paused accounts remain listed and retain their login data.

`RUNNING` means the service is running. A ready session and model catalog describe readiness and metadata. Neither proves generation, current quota, or every model's eligibility. Check the account and run a model test.

### 4. Test a model

Open **Models & tests**, select the backend, and load its actual catalog. Choose an accessible model. Example names such as `gemini-4-argon` are not promises of access.

Use individual **Chat**, **Responses**, and **function calling** tests, or **Test all**. Edit test messages through the disclosure if needed. These are real requests and use backend quota.

Read each API's result separately. A successful generation test requires a completed, nonempty reply, not HTTP success alone. Function calling checks returned function arguments; it does not execute the function. Open result details for the endpoint, requested model, reported model, timestamp, and duration. Stale or mismatched evidence needs a retest.

**Latest AI reply** keeps received text on the page and provides copy controls. A newer manual test and a saved connection result are distinct records. Old truncated results cannot be recovered by merely reopening them; run a new test if needed.

### 5. Connect 9Router

Open **9Router → Connection**. Start the selected managed 9Router installation, choose the backend, and click **Connect automatically**. If prompted, sign in through the app's dashboard dialog. A separate browser login does not supply the app's dashboard session.

The app verifies the backend as needed, creates or updates its owned node/connection, prepares an active client key, loads models, and requests a routed Responses reply. Inspect **Connection results** and its per-stage errors. A responding port or dashboard login alone is not a configured provider.

The adjacent client values show the Base URL and full model ID. Copy the selected client key only into a trusted client. Opening a panel does not require revealing it.

### 6. Add a direct Studio Responses route

For Codex/T3, open **9Router → Provider settings → Responses route for Codex & T3**.

1. Keep the AI Studio pool and 9Router running; stop manual account endpoints.
2. Choose your accessible AI Studio model.
3. Leave the default Responses prefix `studio-responses`, or enter a valid distinct prefix. It must start with a lowercase letter; lowercase letters, digits, hyphens, and underscores are supported, up to 48 characters. Certain provider names are reserved.
4. Click **Set up Responses route** and sign in through the dialog if requested.
5. Wait for **Responses verified**. Inspect setup details and the completed nonempty reply.
6. Copy **Model ID for Codex & T3** and use that ID in the client. Verify again after changing models.

This creates or reuses the app's additional Responses node, connection, catalog, and client key. It retains the existing `studio/` Chat route and uses the same backend/account pool. A conflict with another node is rejected rather than silently overwritten.

The upper **Node API type** controls the main connection when **Connect automatically** is used. It does not determine the lower additional Responses-route action. Creating the extra route does not require replacing the working Chat node.

A prefix is a route name, not extra quota, a new entitlement, or a larger context window. Typing the prefix into T3 alone does not create the server route. **Models & tests** checks the primary route; the additional prefix's proof is in its Responses setup result. Test client tools, images, and streaming separately.

## Connect your client

### Codex CLI

After routed Responses succeeds, open **Codex**. Choose the project folder and selected router key, review the configuration, then **Apply profile**. The app backs up the configuration before merging its `nine-router` provider and `gemini-bridge` profile. Other default models/providers are preserved semantically, though TOML formatting/comments may change. Conflicting provider definitions are rejected.

Run **Test Codex CLI**, inspect the real result, then use **Open Codex**. The launcher supplies the saved key as an environment variable. Changing the model or endpoint requires applying the profile again.

**Copy configuration** is different: it exports a standalone configuration containing the selected raw bearer key, while the on-screen preview masks it. Treat clipboard contents as credentials. The T3 guide deliberately produces key-free TOML and places its key in the provider environment instead.

### T3 Code

Use **Other apps → T3 Code** and the full [T3 Code guide](T3-CODE.md). Select the client location and endpoint, prepare a dedicated CODEX_HOME, leave Shadow home empty, paste generated TOML, add `NINEROUTER_API_KEY` to that provider's environment, and register the exact verified model ID. Start a new conversation and verify a reply.

The guide prepares values; it does not automatically edit T3. A working Chatbox conversation does not prove the Responses path required by T3's Codex driver.

### Chatbox and Zed

In **Other apps**, use the guide for your client and copy the actual values. For Chatbox:

| Setting | Value |
| --- | --- |
| API Mode | OpenAI API Compatible |
| API Host / Base URL | Router URL ending in `/v1` |
| API Path | `/chat/completions` |
| API key | Active client key from that router |
| Model | Full catalog ID, including `studio/` or the relevant prefix |

For a default local installation, the request preview is `http://127.0.0.1:20128/v1/chat/completions`; the catalog is `/v1/models`. Enter the whole model ID. A friendly nickname does not replace it.

The Zed tab prepares an OpenAI-compatible provider configuration for Zed's built-in agent. Paste it into the appropriate settings and enter the key through Zed's key control. External-agent configuration is a separate workflow. Check the selected model's capabilities in the client itself.

## Single and Parallel pool

Open **Tasks & parallel → Pool through 9Router**. Choose **Single** for one request at a time, or **Parallel** for independent requests across several eligible accounts. Set the worker limit and select **Apply mode**. Wait for the server-confirmed value; new workers may need warm-up time.

For two eligible active accounts and two overlapping requests, a limit of 2 permits concurrency. Each account remains limited to one concurrent request. The UI caps the pool limit at 32 and requires enough enabled accounts. Useful capacity also depends on resources, quota, readiness, and model access.

Single/Parallel also affects incoming requests through the shared Studio connection in 9Router. One request does not fan out to all accounts. Additional accounts do not combine context windows or guarantee more available quota.

Keep manual endpoints stopped in this mode. Wait for active tasks/service operations before changing capacity.

## Local task messages

Open the task queue disclosure in **Tasks & parallel**. Add independent messages, choose automatic pool assignment or specific accounts, and run the queue. The model is the app's selected queue model.

Tasks on the same account wait for one another. Failures retain their own errors and do not erase other results. Each task needs a completed nonempty Chat reply. Messages, assignments, statuses, and received text persist locally. Cancel prevents unsent work; an in-flight request waits for its result.

Explicit account assignments can start task-owned endpoints and temporarily pause the pool. Mixed automatic/manual assignments use available account endpoints. The queue closes endpoints it created when finished; endpoints you started yourself remain running.

The queue does not execute tools, edit files, run terminals, or create autonomous coding agents. Tasks do not automatically share conversation history. Use a compatible external client for a continuing chat or coding workflow.

Hide results without deleting them by collapsing the last-results section. Deletion requires confirmation after the batch finishes and removes stored messages/replies, including prompt copies in finished helper records; operation summaries remain.

## Dedicated account endpoints

Use this optional mode only when a client must use a particular existing account.

1. Finish login and wait for shared-pool requests/tasks to complete.
2. Open **Tasks & parallel → Manual account endpoints**.
3. Start the desired account's endpoint and wait for readiness.
4. Copy that row's current endpoint and account key. Use a backend model ID without `studio/`.
5. Configure the compatible external client and verify generation.
6. Repeat with another account only if needed.
7. Stop every manual endpoint when returning to shared-pool/9Router use.

The first manual endpoint pauses shared-pool generation because the account runtime has an exclusive lease. A `STOPPED` pool with ready sessions can therefore be expected. Shared 9Router requests must wait while clients use the dedicated endpoints.

An endpoint serves exactly the account on its row. Its harness settings show the URL, masked key, and model selector. The model selector prepares copied configuration; the external client's request still chooses the model. Use current copy values after restarting an endpoint because its address/key may change.

Closing the app does not stop these endpoints. Pool start/stop/restart/login and backend-folder changes are refused while endpoints run. **Models & tests** uses the shared pool, not manual endpoints. Test a manual endpoint using a task explicitly assigned to that account or directly from a compatible client.

### Restore the pool

Stop sending requests from clients and wait for active work. Stop **all** manual endpoints. The app restores a pool it originally found running after the last endpoint stops. If shown, use **Restore pool** and wait for confirmation. An originally stopped pool stays stopped; start AI Studio manually after endpoints stop. Wait for `RUNNING`, then retest the router/model.

## Public HTTPS and RDP

On the RDP hosting AI Studio and 9Router, open **9Router → Public access**. Install/import Caddy, enter the server's public IP/domain, save and validate, then start the managed proxy. Allow the needed ports in the host/provider firewall and configure DNS/NAT where applicable.

Run **Test public connection**. It checks HTTPS certificates, dashboard isolation, key enforcement, and the authenticated model catalog. The optional Responses test consumes backend quota. Copy the HTTPS Base URL, exact model ID, and active key into the client on the other computer.

`127.0.0.1` on your local computer is not the RDP. Use the public HTTPS endpoint there and verify a request from that client machine. A server-local test alone does not prove external reachability. See [PUBLIC-ACCESS.md](PUBLIC-ACCESS.md).

## Keys, settings, and maintenance

The key registry is shared between the 9Router and Codex pages. You can create named keys, select an active one, enable/disable, briefly reveal/copy, or revoke after confirmation. The app rereads server state. Disabling/deleting a selected key invalidates its test proof; reactivation needs a new test. Forgetting a local saved key is separate from server revocation.

Settings and results remain in the app data directory shown in **Settings**. OS credential storage protects selected secrets; backend session data belongs to the upstream backend. Changing router address clears old local router credentials so they are not sent to a different instance. Keep runtime directories and backups out of Git.

Themes and typography do not change accounts/routes. Quick settings supports right-click, Shift+F10, and the toolbar button. Text scale is 60–125%, with automatic system-following behavior. Full font preview/save/cancel controls live in **Settings → Typography**; see [TYPOGRAPHY.md](TYPOGRAPHY.md).

The header maintenance control previews eligible app-owned cache/temp/history files before cleanup. It preserves accounts, credentials, backend installations, configuration, and current task results. Restart refreshes the app while backend services remain running. Cleanup and restart are blocked during app jobs/tasks. Cleanup frees disk space, not a guaranteed amount of RAM.

The offline **Guide** includes searchable current instructions. Its footer reads created by [DesignMD Generator](https://designmdgenerator.dev), and its top-right **Developer** button opens [developer contact](https://t.me/mazda_dev).

## Troubleshooting

| Symptom | Action |
| --- | --- |
| Newly installed Git/Node not detected | Recheck prerequisites and inspect the reported executable/path. |
| Ready account but no reply | Finish consent; check model eligibility, quota, and detailed test errors. |
| Parallel selected but only one account busy | Check the saved worker limit and whether independent requests actually overlap. |
| Shared pool will not start | Stop all manual endpoints, restore the pool, then retest. |
| `401` / `403` | Use an active client key from the target router, not a backend/admin key. |
| Unknown prefix / no active credentials | Create/verify that node and connection on the target server; client names alone do not create routes. |
| Chatbox works, T3 fails | Check Responses, tools/images/streaming, and the T3 provider configuration separately. |
| `context_length_exceeded` | Reduce/compact request context; for image-tool conversion use the verified Responses route and a fresh session. Parallel does not enlarge context. |
| Remote timeout/TLS failure | Check public endpoint, router/proxy state, certificate, host/provider firewall, DNS, and NAT from the client machine. |
| Settings changed but old success remains | Inspect evidence timestamp/model/endpoint and retest. |

For an issue report, include version, OS, selected route/model, and a redacted error. Do not attach keys, cookies, account directories, or private replies. For deeper detail, use [execution](EXECUTION.md), [T3 recovery](T3-CONTEXT-LIMIT.md), [platform status](PLATFORMS.md), and [resource limits](LOW-SPECS.md).

## Application updates

Open **Settings → Updates** to check, download, and install a signed release. Checks also run automatically with a daily interval. Downloads and installation require your choice. Finish active work and confirm before installing. Users of 1.7.4 run the first updater-enabled installer once; portable users use release downloads. See the [update guide](UPDATES.md).
