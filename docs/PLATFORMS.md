# Platform build and verification

Source configuration 1.7.4, 25 October 2026. Earlier version-specific notes below retain their historical scope.

Windows x64 is the native platform exercised in this workspace. Linux and macOS have platform-specific build configuration and runtime adapters, but neither native build nor complete GUI operation has been verified on those operating systems here. There is no usable Linux distribution/VM or macOS host in this environment. The prepared `.github/workflows/native-platforms.yml` has not been executed on GitHub during this task.

## Platform behavior

| Area | Windows | Linux desktop | macOS |
| --- | --- | --- | --- |
| Installer targets | NSIS | Debian package, AppImage | App bundle, DMG |
| Webview | WebView2 | WebKitGTK 4.1 | WKWebView |
| Backend binary names | `.exe` | No suffix | No suffix |
| Credential store | DPAPI CurrentUser | Secret Service through `secret-tool` | Login Keychain through `security` |
| Open URL or folder | Explorer | `xdg-open` | `/usr/bin/open` |
| Caddy sign-in startup | Current-user Startup shortcut | XDG desktop autostart | User LaunchAgent |
| Firewall controls | Scoped Windows rules | OS firewall settings | OS firewall settings |
| Codex terminal | PowerShell | Installed terminal emulator | Terminal.app |
| Helper scripts | PowerShell `.ps1` | Node `.mjs` | Node `.mjs` |

The native shell supplies the data directory to every backend invocation. Its defaults follow Tauri's application identifier: Windows Local AppData, Linux `$XDG_DATA_HOME/app.geminibridge.desktop` or `~/.local/share/app.geminibridge.desktop`, and macOS `~/Library/Application Support/app.geminibridge.desktop`. Direct Node execution uses the same Linux/macOS paths; its historical Windows fallback remains `LocalAppData/GeminiBridge`. An absolute `GEMINI_BRIDGE_DATA_DIR` can isolate an instance.

Node.js 22+ is required at runtime and is not bundled. The native launcher checks PATH and common Homebrew, system, Volta, npm-global, fnm and nvm locations on POSIX desktops. `GEMINI_BRIDGE_NODE` accepts an absolute Node executable path. Child tools inherit a PATH containing the selected Node directory. Git is needed for backend installation; Codex CLI is optional for other clients.

On Windows, version 1.7.1 reads the current machine/user PATH from the registry and checks common Node/Git installation directories on each helper invocation. Recheck prerequisites bypasses the Git/Codex discovery cache, so newly installed tools can be detected without restarting the app. Portable tools must be placed in PATH or a standard folder. See [low-spec resource evidence](LOW-SPECS.md) for the unverified 2 GB deployment target.

Version 1.7.2 also uses WebView2's optional memory-target API: LOW while inactive and NORMAL when active. Native minimize state pauses UI polling/animations even when the WebView document still reports visible; restore resumes ordinary updates. Older runtimes without this memory API continue to work. Browser language catalogs load on demand; backend language support remains synchronous. Credential-read reuse is confined to one operation and released on completion/error. These changes do not suspend backend jobs or modify existing user concurrency settings. The Windows Job Object test constrains application processes, not the host OS; it is not a native Linux/macOS or complete 2 GB RDP certification.

Linux requires a desktop session with an unlocked Secret Service keyring and `secret-tool` (`libsecret-tools` on Debian/Ubuntu). macOS requires an unlocked login Keychain. A keyring failure is reported without creating a plaintext fallback. POSIX `.keychain` files contain only provider markers, while credential values remain in the OS store. Windows DPAPI files are unchanged. POSIX credential values travel through stdin instead of command arguments.

Caddy downloads match the running Node OS/CPU: Windows, Linux and macOS on x64 or arm64. Windows x64 retains its SHA-256 pin; additional archives use SHA-512 pins from the official 2.11.7 checksum list. Installation verifies the archive before extraction and runs the binary to confirm its version. Installation does not prove public HTTPS or model inference.

POSIX process control verifies the executable, process identity and required arguments before signaling. Router process groups must also match their recorded leader. Windows retains CIM checks. Unidentified or changed processes are refused.

## Build on the target OS

Include the source, language catalogs, icons and lockfiles in the checkout. The original setup PDF is retired and is not required or included. Build tools do not depend on an attachment or account from the original development workspace. See [BUILD.md](BUILD.md) for target-OS prerequisites and [GITHUB.md](GITHUB.md) for a clean repository and push/pull workflow.

After installing Node.js 22.12+, Git and Rust for source development:

```sh
npm ci
npm run build
npm test
npm run test:platform:smoke
npm run desktop:build
npm run release
node scripts/verify-release.mjs
```

Credential tests need a normal unlocked OS profile. The restricted Windows impersonated process in this workspace cannot encrypt DPAPI fixture keys; tests succeed with the normal Windows profile. Test data and credentials are isolated.

Debian/Ubuntu build dependencies:

```sh
sudo apt-get update
sudo apt-get install -y build-essential curl wget file libssl-dev libwebkit2gtk-4.1-dev libxdo-dev libayatana-appindicator3-dev librsvg2-dev patchelf libsecret-tools gnome-keyring dbus-x11 xdg-utils lsof
```

Build Linux packages on the oldest intended distribution that supplies WebKitGTK 4.1. Newer glibc builds do not establish compatibility with older distributions. AppImage still requires Node, a desktop keyring, browser handlers and suitable system libraries. The Debian configuration declares keyring/opening/process-inspection dependencies.

On macOS, install Xcode Command Line Tools (`xcode-select --install`) and Rust before the common commands. Exercise Apple Silicon and Intel builds separately. Signing and notarization have not been performed here; generating a DMG does not establish Gatekeeper acceptance.

Output goes to `release/windows-v1.7.4`, `release/linux-v1.7.4` or `release/macos-v1.7.4`. Release verification checks hashes and package formats, not complete GUI operation. Linux/macOS manifests explicitly retain `nativeRuntimeVerified: false`. For nondefault Cargo output, set `GEMINI_BRIDGE_BUILD_DIR` to that target's release directory before packaging.

## Required native verification on Linux and macOS

The prepared CI matrix builds on Windows, Ubuntu 24.04 and macOS. It runs backend tests, real OS credential smoke tests, official Caddy installation and browser UI fixtures. Windows additionally runs native IPC/UI tests. It uploads artifacts without publishing a release. Browser fixtures do not verify WKWebView or WebKitGTK behavior.

Before claiming full support, verify these on each target OS:

1. Fresh installation, desktop launch, Node discovery, folder picker, clipboard, browser opening and local/system fonts.
2. Right-click Refresh through the splash, saved preferences, live slider, minus/plus, automatic scale, keyboard focus, all themes and reduced motion.
3. Keyring save/read/delete across app and desktop restarts, plus locked/missing keyring handling.
4. Pinned backend installation and actual CPU execution, Google login, account readiness and real model responses.
5. Router installation, dashboard authentication, selected active key, catalog, Chat, Responses and function calling.
6. Single/Parallel pool changes, task queue, manual account endpoints, verified stop and pool restoration without affecting unrelated processes.
7. Optional Codex apply/export/terminal workflows with the selected endpoint/model/key.
8. Optional Caddy HTTPS, negative key probes, process ownership and startup after desktop login. Ports 80/443 may require administrator-approved OS configuration. Linux can use `cap_net_bind_service` for the selected Caddy binary. Linux/macOS firewall controls remain in the OS.

The 1.7.0 UI/platform tests do not claim new Google authentication or live AI inference. Existing inference reports remain historical evidence.

## Primary references

- [Tauri prerequisites](https://v2.tauri.app/start/prerequisites/) and [platform configuration](https://v2.tauri.app/reference/config/).
- [Tauri distribution](https://v2.tauri.app/distribute/) and [Linux compatibility](https://v2.tauri.app/distribute/debian/).
- [Pinned Magpie source](https://github.com/BUKOWSKIREAL/magpie-gemini-web/tree/be9ee431fa7ba6e5c8a5db3abbce10c98d8ee998), including `binaryPath` and platform release pins.
- [Official Caddy 2.11.7 checksums](https://github.com/caddyserver/caddy/releases/download/v2.11.7/caddy_2.11.7_checksums.txt).
- [GitHub runner platforms](https://docs.github.com/en/actions/reference/runners/github-hosted-runners).
