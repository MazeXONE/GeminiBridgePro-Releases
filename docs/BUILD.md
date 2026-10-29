# Build Gemini Bridge Pro from source

Version **1.8.1**, documentation updated 29 October 2026.

Build native installers on their target OS. Windows x64 packages have been exercised in this environment. Linux/macOS configurations and runtime adapters are included; native builds and complete operation still require validation on those systems.

The source repository is currently private. The commands below require an authorized GitHub account with repository access. Public installer users do not need source access: [download Windows packages](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/latest).

## Start from a clean checkout

Install Git, **Node.js 22.12+** with npm, Rust stable through rustup, and the OS dependencies below. The minimum crate Rust version is recorded in `src-tauri/Cargo.toml`. The canonical repository is shown below; use your fork URL when appropriate:

For the pinned OmniRoute runtime, use Node.js 22.22.2+ in the 22 series, or versions 24 through 26. Node.js 24 works with both supported routers.

```sh
git clone https://github.com/MazeXONE/GeminiBridgePro.git
cd GeminiBridgePro
```

The source needs its lockfiles, scripts, catalogs, logo/icon assets, and Tauri files. It does not need a setup PDF, production videos, developer accounts, or a previous toolchain cache. Internet is required for uncached npm/Cargo dependencies and bundler downloads.

The developer minimum follows the frontend toolchain. The installed app separately needs Node.js **22+** for its backend helper. Node.js is not bundled; backend components and 9Router are installed/configured through the app after installation.

## Windows x64

Install Microsoft C++ Build Tools with **Desktop development with C++**, the Windows SDK, Rust's MSVC toolchain, and Microsoft Edge WebView2. Open a new terminal after installation so it receives the updated PATH.

```powershell
node --version
git --version
rustc --version
cargo --version
npm ci
npm run build
npm test
npm run test:platform:smoke
npm run desktop:build
npm run release
node scripts/verify-release.mjs
```

`desktop:build` runs the UI/backend build before native compilation. The NSIS installer and uninstaller use the product's ICO. Packaging creates an installer, portable directory, ZIP, inventory, and checksums in `release/windows-v1.7.7/`. Read `manifest.json` for `portableDirectory`; the script uses a versioned directory, while the ZIP contains `GeminiBridgePro/`.

The optional `.work/toolchain` GNU/MinGW installation belongs to the original development environment. A clean clone uses your installed standard toolchain. The cache is excluded from Git and need not be copied between devices.

## Linux desktop

For Ubuntu/Debian with WebKitGTK 4.1:

```sh
sudo apt-get update
sudo apt-get install -y build-essential curl wget file libssl-dev libwebkit2gtk-4.1-dev libxdo-dev libayatana-appindicator3-dev librsvg2-dev patchelf libsecret-tools gnome-keyring dbus-x11 xdg-utils lsof
```

From the project root:

```sh
npm ci
npm run build
npm test
npm run test:platform:smoke
npm run desktop:build
npm run release
node scripts/verify-release.mjs
```

`src-tauri/tauri.linux.conf.json` selects Debian packages and AppImage. Output goes to `release/linux-v1.7.6/`. Use a desktop session with an unlocked Secret Service/keyring. The credential smoke test uses a test key; a headless shell without the service does not validate credential storage.

Build on the oldest intended distribution supplying WebKitGTK 4.1. A package built against newer glibc does not prove compatibility with older Linux systems. AppImage still requires suitable system libraries.

## macOS

Install Xcode Command Line Tools:

```sh
xcode-select --install
```

Install Node.js 22.12+, Git, and Rust stable, then run:

```sh
npm ci
npm run build
npm test
npm run test:platform:smoke
npm run desktop:build
npm run release
node scripts/verify-release.mjs
```

`src-tauri/tauri.macos.conf.json` selects an app bundle/DMG and ICNS, with macOS 12 as its configured minimum. Output goes to `release/macos-v1.7.6/`. Architecture follows the Rust target/host; test Apple Silicon and Intel separately when distributing both.

Credential storage needs an accessible Keychain. Signing/notarization are not configured. Producing a DMG does not establish Gatekeeper acceptance, working backend binaries, Google login, or inference on the recipient's machine.

## Development and browser preview

Use `npm run desktop:dev` for native development. Its startup builds the helper and starts Vite. Use `npm run dev` for browser-only hot reload. Do not run both on port 1420 together.

For a production browser preview:

```sh
npm run build
npm run preview
```

Open `http://127.0.0.1:1420`. Browser development uses separate `.dev-data`. The production desktop uses Tauri IPC and does not need the preview server.

For browser fixtures without suitable Chrome:

```sh
npx playwright-core install chromium
```

Scripts supporting `CHROME_PATH` can use that Chromium executable. Windows-specific checks still need Windows. OS credential checks need a normal unlocked user profile, not an impersonated context without its cryptographic profile.

## Optional QA inputs

A standard build and `npm test` use repository source and self-contained fixtures. They do not need Google accounts, developer app data, old router archives, or previous releases.

| Optional QA | Explicit input |
| --- | --- |
| Live execution, catalog, and UI against an existing deployment | Set `GEMINI_BRIDGE_LIVE_QA=1` and an absolute `GEMINI_BRIDGE_DATA_DIR` for the selected app data. Guards reject missing opt-in. |
| `scripts/qa-accounts-native.mjs` | Set absolute `GEMINI_BRIDGE_QA_REPO` and `GEMINI_BRIDGE_QA_STUDIO_BINARY` paths to a compatible backend checkout and Studio fixture binary. |
| Actual router, reference backend, or older-version resource comparison | Supply the pinned archive, reference checkout/binary, or baseline executable specified by the selected script. These are not normal GitHub build dependencies. |

The guarded live group includes `qa-execution-live.mjs`, `qa-execution-router-live.mjs`, `qa-live-catalog.mjs`, and `qa-live-ui.mjs`. Run only against an authorized deployment. Some tests send generation requests or temporarily change pool capacity. Flags do not provide accounts, keys, or model access.

A missing optional QA asset does not make the developer's cache a normal source dependency. Keep private deployment data out of Git. Report fixture, native, and live results separately.

## Signed update releases

For the maintainer update channel, use `npm run desktop:build:signed`, then `npm run release` and `npm run release:updater`. Signing requires the original private key supplied securely outside Git. A normal `desktop:build` still works without that key and skips updater artifacts. The updater plugin requires Rust 1.90 or newer. See [UPDATES.md](UPDATES.md).

## Before distribution

Run checks relevant to your change, verify packages with `node scripts/verify-release.mjs`, and exercise the installer on the intended OS. Verification checks format, contents, and hashes; it does not establish model entitlement, quota, or remote reachability.

Compilation, linking, caches, and bundling may need more resources than the installed app. Build on a suitable development machine before transferring an installer to a small RDP.

See [platform status](PLATFORMS.md) for verification limits and [GitHub workflow](GITHUB.md) for versioning/publication. Native prerequisites are documented by [Tauri](https://v2.tauri.app/start/prerequisites/).
