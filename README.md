<img src="public/gemini-bridge-pro.svg" width="80" height="80" alt="Gemini Bridge Pro logo">

# Gemini Bridge Pro

Your Gemini connections, managed in one workspace.

Manage existing Gemini accounts, accessible models, 9Router or OmniRoute connections, client keys, API verification, parallel capacity and public HTTPS configuration from one desktop app. External coding clients handle their own files, terminal tools and agent orchestration.

Version 1.8.0 adds **Active router** selection with separate settings and encrypted credentials. Both routers support installation, start/stop, dashboard login, local provider/model setup, client-key controls and API tests inside the app. See [router setup](docs/ROUTERS.md). Reapply the Codex profile and update existing client endpoints/keys after switching routers.

## Download for Windows

Current stable version: **1.8.0**, Windows x64. No GitHub sign-in is required to download the packages.

| Download | Use |
| --- | --- |
| [Windows installer 1.8.0](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/download/windows-v1.8.0/GeminiBridgePro-v1.8.0-Windows-x64-Setup.exe) | Recommended for shortcuts and future in-app updates |
| [Portable ZIP 1.8.0](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/download/windows-v1.8.0/GeminiBridgePro-v1.8.0-Windows-x64.zip) | Extract the complete archive before opening the app |
| [SHA256SUMS.txt](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/download/windows-v1.8.0/SHA256SUMS.txt) | Compare download checksums |
| [Latest release](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/latest) / [Version history](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases) | Release notes and newer versions |

GitHub's automatic **Source code** ZIP/tar links contain this distribution repository's documentation only. Choose the Setup EXE or portable ZIP above to install the application.

## Requirements and installation

Use Windows x64 with WebView2, [Node.js](https://nodejs.org/en/download) and [Git](https://git-scm.com/downloads/win). Node.js 24 supports both pinned routers. OmniRoute requires Node.js 22.22.2+ in the 22 series, or versions 24 through 26. Codex CLI is optional except for Codex/T3 Codex workflows. Backend components and browser downloads need internet access and additional disk space. Full-stack operation on a 2 GB / 2 vCPU Windows RDP has not been certified; see [measured resource limits](docs/LOW-SPECS.md).

1. Download the installer and checksum file above.
2. In PowerShell, compare the printed hash with the matching line in SHA256SUMS.txt:

   ```powershell
   Get-FileHash .\GeminiBridgePro-v1.8.0-Windows-x64-Setup.exe -Algorithm SHA256
   ```

3. Run the installer, then open **Gemini Bridge Pro** from its shortcut.
4. Open **Guided setup** and follow the first-connection steps below.

The installer/uninstaller use the product logo. Windows publisher/Authenticode signing is not configured; updater signatures separately verify official update packages.

For portable use, extract the entire ZIP and run `GeminiBridgePro/GeminiBridgePro.exe`. Keep its backend folder and DLL beside the executable. Portable files do not include your accounts/settings and do not use in-app installer replacement.

## First connection

1. In the router workspace choose **Active router**, then use **Guided setup** to check Node.js and Git and prepare the selected backends/router. **Recheck prerequisites** reloads the Windows PATH after tool installation.
2. In **Gemini accounts**, start AI Studio, add your existing Google account and complete browser sign-in/consent. Repeat for additional accounts if needed.
3. In **Models & tests**, load the catalog, select an accessible model and verify a real generation. Session readiness and model listings alone do not prove inference.
4. In **9Router** or **OmniRoute**, start the selected router and select **Connect automatically**. Review the endpoint, full model ID and client-key controls.
5. For Codex/T3, use the selected router's **Provider settings → Responses route for Codex & T3** and wait for **Responses verified** before copying the new model ID. The existing Chat route remains available.
6. In **Codex** or **Other apps**, follow your client's guide. Match the Base URL, API type, full model ID and client key, then verify a reply in that client.

Chatbox uses Chat Completions; Codex and T3's Codex provider use Responses. Test the actual API and tool behavior your client needs. A local endpoint such as `http://127.0.0.1:20128/v1` refers to that client computer. A client on another machine needs the configured public HTTPS endpoint.

## Accounts, parallel work and RDP

**Tasks & parallel** controls Single/Parallel capacity. Independent requests can run concurrently on eligible accounts, with one request per account. Parallel mode does not increase one request's context window or account quota. Manual account endpoints pause shared-pool generation until stopped.

On your RDP, use **9Router → Public access** for managed HTTPS and active client-key protection. Configure the remote client with the resulting endpoint and test from that client machine. DNS/firewall/NAT may need separate configuration. The app routes access your existing accounts actually have; it does not create accounts or unlock another product's native model picker.

## Check the version and update

The sidebar and **Settings → Updates** show the installed version. From 1.8.0, checks run at startup and daily; **Check for updates** checks immediately. Select **Download update**, save your work, then confirm **Install & restart**. Active application jobs block installation. Existing account/settings storage is kept.

**Upgrading from 1.7.4 or 1.7.5:** download and run the 1.8.0 installer once over the existing installation using the same Windows account and location. Earlier builds either lack the updater or point at the private feed. You do not need to uninstall first. Future releases use this public channel. Portable users download/extract a new ZIP instead.

The public updater metadata is [latest.json](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/latest/download/latest.json). No GitHub token is embedded in the application.

## Clone and build yourself (authorized source users)

The application source is currently private in [MazeXONE/GeminiBridgePro](https://github.com/MazeXONE/GeminiBridgePro). These commands require a GitHub account granted source access. Cloning this public distribution repository gives documentation only. Public installer users do not need source access.

Install Git, Node.js **22.12+**, npm, Rust stable **1.90+**, and the [OS-specific native prerequisites](docs/BUILD.md). On Windows, install Microsoft C++ Build Tools with Desktop development with C++, Windows SDK and WebView2. On macOS, install Xcode Command Line Tools. Linux requires WebKitGTK 4.1 and the desktop/keyring packages in the build guide.

```sh
git clone https://github.com/MazeXONE/GeminiBridgePro.git
cd GeminiBridgePro
npm ci
npm run build
npm test
npm run desktop:build
npm run release
node scripts/verify-release.mjs
```

Windows output: `release/windows-v1.8.0/`. Native development: `npm run desktop:dev`. On another device, clone once, then use `git pull --ff-only` and `npm ci`. Build native installers on their target OS. Linux/macOS configurations are included; their native operation is not claimed as verified here. Normal personal builds do not require the maintainer's updater signing key.

## User documentation

| Guide | Contents |
| --- | --- |
| [User guide](docs/USER-GUIDE.md) | Full setup, account controls, testing and everyday use |
| [9Router and OmniRoute](docs/ROUTERS.md) | Router selection, separate settings, key controls and runtime requirements |
| [Build guide](docs/BUILD.md) | Windows/Linux/macOS tools and build commands |
| [T3 Code](docs/T3-CODE.md) | Provider configuration and first reply |
| [Context limits](docs/T3-CONTEXT-LIMIT.md) | Long conversations, images and route recovery |
| [Public access](docs/PUBLIC-ACCESS.md) | HTTPS, keys and remote verification |
| [Execution modes](docs/EXECUTION.md) | Pool, parallel capacity and manual endpoints |
| [Platform status](docs/PLATFORMS.md) | Verification limits per operating system |
| [Sources and licenses](docs/SOURCES.md) | Third-party attribution |

The offline **Guide** is also inside the application. The interface starts in English and offers eight languages.

## Support and license

Contact the [developer](https://t.me/mazda_dev) with the app version, OS, failing step and redacted error. Never post client keys, cookies or personal account information.

Application licensing: [MIT](LICENSE). Third-party notices are included in the downloaded packages. Gemini Bridge Pro is independent; provider eligibility and terms still apply.

Created by [DesignMD Generator](https://designmdgenerator.dev)
