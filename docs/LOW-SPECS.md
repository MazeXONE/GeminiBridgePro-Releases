# Low-spec target: 2 GB RAM / 2 vCPU RDP

Windows x64. Latest recorded application measurements: **1.7.5**, **25 October 2026**. Earlier-version results below retain their historical scope. These are measurement records, not a full-stack 2 GB RDP certification.

## Version 1.7.5 updater

A 10-second visible-window sample on the same development host, with isolated empty data and offline model backends, recorded approximately **144.68 MiB private resident** and **313.49 MiB private committed** memory for the app/WebView2 process group. The test also exercised minimize/restore. Its duration and host activity differ from historical samples, so it is not a controlled percentage comparison or a complete-stack peak measurement. It included no Google login/Camoufox/inference and is not a 2 GB RDP certification. Evidence: `artifacts/resources-v175.json`.

The updater performs native HTTP metadata checks at a persisted daily interval, uses no separate updater service, and downloads only on user action. The Updates panel is loaded on demand (approximately 1.55 KB gzip in the recorded production build); the small controller is approximately 0.83 KB gzip. Native TLS/signature code increases the Windows executable from approximately 3.86 MB to 5.38 MB in these builds; executable size is not a RAM measurement.

## Version 1.7.4

The additional Responses route uses the existing backend/account pool, without new dependencies, timers, or idle services. Its controls load with 9Router, while the offline Guide loads when visited. Native application and six WebView2 processes were sampled with separate data and offline backends/router:

| Mean private resident memory | 1.7.3 | 1.7.4 |
| --- | ---: | ---: |
| Visible, not minimized | 144.56 MiB | 149.10 MiB |
| Minimized | 122.84 MiB | 125.90 MiB |

The recorded visible difference is approximately 4.55 MiB using unrounded samples. Version 1.7.4 private committed memory was approximately 394.50 MiB. The summed working set, approximately 395.76 MiB, counts shared pages repeatedly. These counters describe different things.

The host had 32 GB RAM, not a 2 GB VM. No Google login, Camoufox, or inference ran in this measurement. Windows did not give the test window foreground focus, so the condition is called visible/not minimized rather than active.

The WebView2 normal → low on minimize → normal on restore policy passed. Snapshot counts stayed unchanged while minimized and increased after restore. Local evidence: `artifacts/resources-v174-comparison.md`, `artifacts/resources-v174-exclusive.json`, and `artifacts/resources-v173-exclusive.json`. Local QA output is excluded from GitHub source.

## Intended deployment

For the 2 GB / 2 vCPU target, begin with one eligible active account, Single mode, and one request at a time. Additional accounts, browser login, and parallel requests increase resource use. The app does not silently change your selected mode or impose a heap limit that interrupts longer operations.

Runtime requirements include Windows x64, WebView2, Node.js 22+, Git, and SSD space for backends, the login browser, caches, and Windows updates. Codex CLI is optional outside Codex/T3 workflows.

**Actual 2 GB validation remains pending.** The constrained-process tests below do not constrain host OS memory and did not log in to Google or generate with user accounts. Camoufox login must be included in a real machine's peak budget.

## Changes introduced in 1.7.2

- Only the selected language catalog loads initially. All eight remain available on demand. Loading failures preserve the saved language choice.
- Windows snapshots batch credential decrypts into one operation-scoped helper, rather than three authenticated-state PowerShell processes. Secret reuse ends with the operation and is invalidated by writes/deletion; no cross-operation plaintext cache is stored on disk.
- Inactive WebView2 windows use the LOW memory target and return to NORMAL when used. Unsupported older runtimes can still run the app.
- Explicit minimize notifications pause UI snapshot/proxy polling and animation; restore refreshes status. Backend requests and jobs keep running.
- Version 1.7.1 PATH refresh, direct prerequisite rechecks, and cached Git/Codex discovery remain. Missing optional Codex no longer requires launching PowerShell.

Camoufox fingerprint/login behavior was not changed. The inspected backend manager already used `WAA_BACKEND=go` and one worker by default; it did not launch Camoufox for each model request.

## Reading memory counters

The initial 1.7.1 report summed working sets, including repeatedly counted shared DLL pages. Its approximately 407 MiB was not private active RAM. A separate baseline recorded approximately 145 MiB private resident, 403 MiB summed working set, and 377 MiB private committed memory.

Private resident is closer to Task Manager's default process Memory column, but OS/shared allocations still need RAM. Committed memory is not the amount of physical RAM resident at that instant. Evidence: `artifacts/resources-baseline-v1.7.1-private.json`.

With equal window/sample conditions, 30 seconds per state:

| Mean app/WebView2 private resident | 1.7.1 | 1.7.2 |
| --- | ---: | ---: |
| Active window | 145.15 MiB | 143.19 MiB |
| Minimized | 140.45 MiB | 109.07 MiB |

The minimized reduction was approximately 22.3%; active memory was essentially unchanged. Native checks confirmed NORMAL → LOW → NORMAL, no new snapshots during 30 seconds minimized, and refresh on restore. These tests used offline backends and a fresh WebView profile. Evidence: `artifacts/resources-v1.7.2-private.json`.

The original user's login screenshot showed Camoufox at approximately 545 MB and GeminiBridge at 92 MB. It was one snapshot, not a peak guarantee or proof of a leak.

## Historical constrained tests

Initial production JavaScript decreased from 1,066,614 to 514,905 bytes, approximately 52%. This concerns loading/parsing, not a 52% total RAM reduction. Language loading/failure/recovery checks were recorded in `artifacts/frontend-resources/loading-qa.json`.

A real DPAPI fixture check recorded three decrypts at approximately 1,433 ms versus one batch at 755 ms in one host session. This measured credential access, not complete snapshot duration (`artifacts/backend-resource-tests.log`).

The Windows Job Object harness started a suspended workload, assigned two logical CPUs and a combined 1,024 MiB commit limit, then resumed it. App, WebView2, helpers, and services had to belong to the job, without breakaway; the CDP driver and monitor were outside it. Cumulative CPU accounting included finished child processes.

The final combined test passed with **954.65 MiB peak committed memory** inside that 1,024 MiB budget. Five groups covered real empty services, native startup, Recheck, eight navigation pages, settings save, language switching, profile preview, repeated snapshots, and 30 seconds idle. Required PIDs were in the job; no memory/affinity violation occurred. Owned processes were cleaned up afterward. This is process committed memory, not all physical RAM used by Windows.

Services used actual AI Studio/Gemini Web binaries and 9Router 0.5.104 with fresh data, without user accounts. Evidence: `artifacts/low-memory-harness-selftest.json`, `artifacts/low-memory-backend-qa.json`, and `artifacts/low-memory-native-qa.json`.

At that release, 111 of 112 tests passed with one POSIX-only skip, along with four Rust tests, 25 browser workflow groups, and eight native groups. These historical counts are not current-run claims. Sampled-process CPU remains a lower bound because short processes can be missed; constrained tests use cumulative job accounting.

## Validate the real target

Install on an actual Windows 2 vCPU / 2 GB RDP, log in one Google account, run real Chat/Responses, and use it for at least 30 minutes, including Camoufox login. Record available RAM, commit, paging, CPU, latency, and recovery. Only that test can establish a supported minimum for the complete stack on that machine.

Developer scripts:

```powershell
node scripts/qa-resource-regression.mjs
node scripts/qa-prerequisites-native.mjs
node scripts/qa-resources-native.mjs current src-tauri/target/release/gemini-bridge.exe --inactive --seconds=30
node scripts/qa-low-memory.mjs
```

Native checks need a Windows desktop session. Some harnesses require explicit local QA binaries/archives and are not end-user installation steps.

References: [WebView2 memory target](https://learn.microsoft.com/en-us/microsoft-edge/webview2/reference/win32/icorewebview2_19), [working set](https://learn.microsoft.com/en-us/windows/win32/procthread/process-working-set), [Job Object accounting](https://learn.microsoft.com/en-us/windows/win32/api/winnt/ns-winnt-jobobject_extended_limit_information).
