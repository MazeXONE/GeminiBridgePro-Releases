# Execution modes in Gemini Bridge Pro 1.2.1

For current user instructions, including setup order and recovery when manual endpoints block Start AI Studio, see the [user guide](USER-GUIDE.md). This document retains the version-scoped execution evidence below.

Open **Tasks & parallel** (Indonesia: **Tugas & paralel**).

The shared pool through 9Router is the primary panel. Manual account endpoints and the app's local task queue are optional native disclosure panels with remembered open/closed preferences. Each manual account has a harness settings sheet with its actual URL, explicit key-copy actions, and an account-catalog model selector. That selector prepares copied settings; the external harness still chooses the model in each request. No credential is requested merely by opening a panel.

Choose **Single** for one pool worker or **Parallel** for several accounts. Set the pool worker limit and apply it. The application reads the actual AIStudio2API runtime configuration back before accepting the change. Every account remains limited to one concurrent request. Parallel uses round-robin selection; Single uses fill-first selection. These settings persist in the backend and also affect requests through the existing Studio connection in 9Router. Newly enabled workers need time to warm up; requests may queue during that time.

The worker limit must not exceed the number of enabled accounts and is capped at 32 in this interface. Actual useful concurrency depends on ready accounts, model eligibility, quota, warm-up and computer resources. Applying a mode while tasks or service operations are running is refused. The app does not create duplicate 9Router connections, change Google credentials or increase account quota.

## Task queue

Add up to 32 independent task messages. Choose automatic pool selection or explicitly assign an enabled account to each task. Run the queue. Single runs one task at a time; Parallel allows the configured number of concurrent tasks. Tasks assigned to the same account wait for one another. Individual failures do not discard successful results or prevent later independent tasks from running. Each task requires a completed Chat response with nonempty text; HTTP success by itself is insufficient.

Each task is one model request. This queue does not execute model tools, edit source files, launch a terminal or split a large project into autonomous coding agents. External coding-agent applications can use the account endpoints described below and manage their own tools and worktrees.

Messages, account assignments, timestamps, terminal states and complete received text stay in the app's local data directory. Known credentials are redacted from replies. The last queue remains visible after reopening the app. An unexpectedly terminated task process appears as interrupted. Cancel stops tasks that have not been sent; an in-flight model request waits for its result. The task results are separate from setup, routed Responses and Codex verification evidence.

Last results can be collapsed without deleting their stored contents. Explicit deletion requires confirmation and the displayed batch ID, rejects running work, removes the persisted batch and cancellation marker, and strips submitted prompts from finished task helper records while preserving operation summaries. Deletion works independently of the backend's current availability.

## App maintenance

The header's maintenance button opens an on-demand scan; it does not introduce another background timer. Cleanup only traverses the app's npm cache, known atomic-write leftovers and archived operation records. Cache files need to be at least an hour old, temporary writes 24 hours old, and archived operations seven days old outside the latest eight. Traversal skips symlinks and junctions; each file is checked again before removal. Accounts, credentials, backend installations, configurations and the current task batch remain outside cleanup. Active app jobs and batches block cleanup and restart. The app's job-notification map is bounded to the current snapshot.

Native restart uses Tauri's application restart and leaves backend services running. Browser preview reloads its document. This starts a fresh UI session and does not claim to reclaim backend memory. Cleanup reports actual file and byte counts, including skipped files.

## Manual account endpoints

Each manual endpoint runs the existing installed AIStudio2API executable against exactly one existing account directory. It uses a separate loopback port, API key, runtime and log directory. The app authenticates to its admin API and verifies the single account ID before using or exposing it. Login sessions are not copied. Account proxy, locale, timezone and entitlement remain owned by the original account directory.

AIStudio2API protects each account's WAA runtime with an exclusive cross-process lease. Therefore, starting the first manual endpoint **temporarily stops generation on the shared pool**. The admin service remains available. Existing clients pointing to the shared pool, including its 9Router connection, must wait until the pool is restored. Agents in manual mode use their account's dedicated endpoint and key directly, with a backend model ID without `studio/`.

Starting manual mode is refused while the pool is processing requests. After its generation service stops and releases its account runtimes, each account endpoint starts and must finish generation-service initialization. A live HTTP listener or a readable account registry does not count as readiness. The last manual endpoint's stop restores the shared pool if the app originally found it running. The app verifies that generation is RUNNING before clearing its recovery record. If recovery is interrupted, **Restore pool** is available after the account endpoints are stopped. An originally stopped pool stays stopped.

The task queue automatically creates any endpoints needed by manual assignments. If manual and automatic assignments are mixed, it assigns automatic tasks among the available account endpoints. After the queue finishes, it closes only the endpoints that it created and restores the pool when no manual endpoints remain. Endpoints explicitly started by the user remain running until stopped. Closing the desktop window does not stop them. These external endpoints are independent of the shared pool's worker limit; external agent applications control their own task concurrency. The upstream account lease still prevents simultaneous use of the same account.

Pausing, deleting or relogging an account first stops its manual endpoint. Backend folder changes are refused while account endpoints run. Shared Studio start, stop, restart and login are refused until manual endpoints are stopped. Stopping an endpoint verifies the executable path, PID, exact auth-directory argument and unique listener argument before terminating its process. Unrelated services are left running.

## Evidence

`artifacts/execution-live-qa.json` records actual Google-backed `gemini-3.8-flash` verification on AIStudio2API `v0.2.13-magpie.1`: two account-attributed shared-pool requests overlapped; Single requests used one account at a time; two manually assigned tasks completed concurrently on independently verified account endpoints. Both accounts produced nonempty completed replies. Task-owned endpoints were stopped and the original capacity was restored. This verifies those accounts/model and the tested requests, not every model or arbitrary future account status.

`artifacts/execution-router-live-qa.json` additionally verifies two completed Responses requests through the user's existing 9Router provider and selected client key, with both Google accounts observed running at once. The router's providers and keys are not modified; the backend's original capacity is restored afterward.

Regression coverage includes capacity validation and preservation, confirmation failures, per-account serialization, bounded task concurrency, partial failures, unfinished replies, cancellation and interrupted-process recovery. UI fixtures are explicitly distinguished from the real account test. The new screen is checked in eight languages, both themes and desktop/narrow layouts.

Implementation references: the installed Magpie `lib/studio-native.mjs`; the pinned [runtime capacity implementation](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/app/lifecycle.go), [worker runtime](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/app/runtime.go), [account store and cross-process leases](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/aistudio/accounts.go), and [admin API](https://github.com/Mag1cFall/AIStudio2API/blob/a784c5193b3ba5c4894d536b2f4cd73e87f99475/internal/api/admin.go).
