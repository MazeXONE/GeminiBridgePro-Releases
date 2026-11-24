# Gemini Bridge Pro CLI — Headless Linux VPS Guide

Complete operational guide for installing and running **Gemini Bridge Pro CLI** on a headless **Linux VPS** (Ubuntu / Debian) 24/7 with **Magpie (`AIStudio2API` + `gemini-web2api-go`)**, **9Router or OmniRoute**, and **Caddy HTTPS** — no desktop environment, no RDP, and no WebView required.

---

## Architecture & Key Capabilities

- **Zero GUI overhead**: Runs purely on Node.js 22.12+ and Go binaries (`AIStudio2API`, `gemini-web2api-go`, `Caddy`). Suitable for 1–2 GB+ Linux VPS instances (2 GB RAM + 2 GB swap recommended for multi-account parallel workloads).
- **24/7 Autonomous VPS Accounts**: Sign in to your Google accounts from your local computer (Windows / macOS / Linux) using Magpie's built-in **Add remotely** workflow or **Paste cookies**. Once an account reaches `Ready`, its session is saved on the VPS disk (`~/.local/share/magpie-ai-studio/auth/`) and continues serving 24/7 even after your local computer is powered off.
- **One-Command Router + Responses Setup**: `gemini-bridge router setup` starts the active router, signs in to its dashboard, connects and verifies the primary Chat route (`studio/<model>`), connects and verifies the dedicated Responses route (`studio-responses/<model>` for Codex & T3 Code), and creates/selects an active client key (`sk-...`).
- **Headless Encrypted Credential Vault**: On headless Linux (without Desktop Secret Service / `secret-tool`), credentials are automatically stored using an **AES-256-GCM encrypted file vault** (`GEMINI_BRIDGE_VAULT=file`, file mode `0600`).
- **Isolated Public HTTPS**: Caddy publishes only `/v1/*` over automatic HTTPS while blocking all router dashboard/admin paths with `404`.

---

## 1. VPS Prerequisites

- **OS**: Ubuntu 22.04 / 24.04 LTS or Debian 12+ (`x86_64` or `arm64`).
- **Node.js**: **22.12+** (Node.js **24 LTS** recommended so both 9Router and OmniRoute are supported).
- **Git**: Required for installing the pinned Magpie backend revision.
- **Firewall**: Open TCP ports `80` and `443` for Caddy HTTPS. Keep ports `2048` (AI Studio) and `20128`/`20129` (9Router/OmniRoute) bound to `127.0.0.1` and closed to the public internet.

### Install Node.js 24 LTS and Git (Ubuntu / Debian)

```bash
sudo apt-get update
sudo apt-get install -y curl git ca-certificates
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt-get install -y nodejs
node -v && git --version
```

### Optional: Allow HTTPS Ports in UFW

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

---

## 2. Download, Verify & Install the CLI

Download the latest CLI archive and checksum file from [GitHub Releases](https://github.com/MazeXONE/GeminiBridgePro-Releases/releases):

```bash
curl -LO https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/download/cli-v1.8.3/GeminiBridgePro-CLI-v1.8.3-Linux.tar.gz
curl -LO https://github.com/MazeXONE/GeminiBridgePro-Releases/releases/download/cli-v1.8.3/SHA256SUMS.txt

# Verify SHA-256 checksum
sha256sum -c SHA256SUMS.txt --ignore-missing

# Extract and install system-wide (/usr/local/bin/gemini-bridge)
tar -xzf GeminiBridgePro-CLI-v1.8.3-Linux.tar.gz
cd GeminiBridgePro-CLI-v1.8.3
sudo ./install.sh
```

> **Non-root user install**: Run `./install.sh --user` to install to `~/.local/bin/gemini-bridge` and `~/.local/share/gemini-bridge-cli`.

Confirm installation:

```bash
gemini-bridge --version
gemini-bridge --help
```

To uninstall later (your accounts and data in `~/.local/share/app.geminibridge.desktop` are preserved):

```bash
sudo ./uninstall.sh
```

---

## 3. Install & Start Backends (Magpie, Router & Caddy)

Install the pinned Magpie backends (`AIStudio2API` + `gemini-web2api-go`), your chosen router (`9router` by default, or `omniroute`), and official Caddy `2.11.7`:

```bash
# Default: installs Magpie + 9Router + Caddy
gemini-bridge install

# Or select OmniRoute directly:
gemini-bridge install --router omniroute
```

Start AI Studio and the active router, then inspect live status:

```bash
gemini-bridge start
gemini-bridge status
```

---

## 4. Sign In Google Accounts from Your Local PC (24/7 on VPS)

You do **not** need a browser or GUI on the VPS. Choose either method below:

### Method A: `Add remotely` via Encrypted SSH Tunnel (Recommended)

1. On the VPS, display the exact tunnel command and AI Studio status:
   ```bash
   gemini-bridge account remote-info --host <YOUR_VPS_IP>
   ```
2. On your **local computer** (Windows PowerShell, macOS Terminal, or Linux), open an SSH tunnel:
   ```bash
   ssh -L 2048:127.0.0.1:2048 <vps-user>@<YOUR_VPS_IP>
   ```
3. Open `http://127.0.0.1:2048` in your local browser and sign in to the **AIStudio2API** admin panel.
4. Click **Add account → Add remotely**:
   - Keep **Instance address** set to `http://127.0.0.1:2048` (forwarded securely through your SSH tunnel to the VPS).
   - Select **Sign in in a new window** (recommended) or **That computer's Chrome accounts**.
   - Copy and run the displayed `aistudio2api` setup command in a terminal on your local computer.
   - A single pairing token can import multiple accounts while active.
5. Verify on the VPS:
   ```bash
   gemini-bridge account list
   ```
   Once accounts show **`ready`**, you can close the SSH tunnel and turn off your local computer. All sessions remain stored on the VPS and serve requests 24/7.

> **Why `127.0.0.1:2048` instead of `<YOUR_VPS_IP>:2048`? Running Gemini Bridge Desktop locally too?**
> Read the detailed guide: [How SSH Tunneling & Port `2049` Avoid Local Conflicts](CLI-VPS-SSH-TUNNEL.md).

> **Session longevity tip**: If you used *That computer's Chrome accounts*, do **not** click "Sign out" on those Google accounts in your local Chrome browser, as signing out revokes the shared session. Using **Sign in in a new window** avoids this.

### Method B: Direct Cookie / Storage-State Import via CLI

If you exported cookies or a Playwright `storage-state.json` from your local browser, copy the file to the VPS (`scp`) and run:

```bash
gemini-bridge account import-cookie \
  --route studio \
  --file ./cookies.json \
  --label you@gmail.com
```

For Gemini Web accounts, pass `--route web`.

---

## 5. Connect 9Router / OmniRoute + `studio-responses` in One Command

Once at least one account is `ready`, run a single command to start the router, sign in, sync the real model catalog, register and verify the Chat route (`studio/<model>`), register and verify the dedicated Responses route (`studio-responses/<model>`), and create/select an active client API key (`sk-...`):

```bash
export GEMINI_BRIDGE_ROUTER_PASSWORD="your-router-dashboard-password"
gemini-bridge router setup \
  --model gemini-4-argon \
  --responses-prefix studio-responses
```

Useful options:
- `--show-key` — print the full client API key in the summary output.
- `--no-responses` — skip the dedicated Responses route and connect only the primary Chat route.
- `--router omniroute` — switch to and configure OmniRoute instead of 9Router.

Print the active full client key anytime:

```bash
gemini-bridge router key show
```

---

## 6. Enable Public HTTPS Access with Caddy

### Non-Root Linux Capability for Ports 80 & 443

If you run `gemini-bridge` as a non-root Linux user, grant the managed Caddy binary permission to bind privileged ports `80` and `443`:

```bash
CADDY_BIN=$(gemini-bridge proxy status --json | node -e "const d=JSON.parse(require('fs').readFileSync(0,'utf8'));console.log(d.installation?.binary||'')")
sudo setcap cap_net_bind_service=+ep "$CADDY_BIN"
```

### Configure & Start Caddy

Use either a **domain name** (automatic Let's Encrypt HTTPS) or a **public VPS IP address** (ACME `shortlived` TLS certificate):

```bash
# Option 1: Domain name (point DNS A record to your VPS IP first)
gemini-bridge proxy setup --host api.example.com --email admin@example.com --start

# Option 2: Direct public VPS IP
gemini-bridge proxy setup --host 203.0.113.10 --start
```

Verify certificate-checked HTTPS, dashboard isolation (`404`), API key enforcement, and model catalog access:

```bash
gemini-bridge proxy test
```

---

## 7. Connect Remote Clients (Codex, T3 Code, Chatbox, Zed)

Use the values printed by `gemini-bridge router setup` and `gemini-bridge proxy status`:

| Client | API Type | Base URL | Example Model ID | API Key |
| --- | --- | --- | --- | --- |
| **Chatbox / Chat clients** | Chat Completions (`/v1/chat/completions`) | `https://api.example.com/v1` | `studio/gemini-4-argon` | `gemini-bridge router key show` |
| **Codex CLI / T3 Code** | Responses (`/v1/responses`) | `https://api.example.com/v1` | `studio-responses/gemini-4-argon` | `gemini-bridge router key show` |
| **Zed** | OpenAI-compatible Chat | `https://api.example.com/v1` | `studio/gemini-4-argon` | `gemini-bridge router key show` |

---

## 8. 24/7 Autostart on Reboot (`systemd`)

Generate and install a `systemd` service so Magpie, 9Router/OmniRoute, and Caddy automatically start after every VPS reboot:

```bash
sudo gemini-bridge systemd --user "$USER" --install
sudo systemctl daemon-reload
sudo systemctl enable --now gemini-bridge
sudo systemctl status gemini-bridge
```

---

## 9. Complete Command Reference

| Category | Command | Description |
| --- | --- | --- |
| **Lifecycle** | `gemini-bridge install [--router 9router\|omniroute]` | Install Magpie backends, router, and Caddy |
| | `gemini-bridge start [--studio\|--web\|--router\|--proxy\|--all]` | Start services |
| | `gemini-bridge stop [--studio\|--web\|--router\|--proxy\|--all]` | Stop managed services |
| | `gemini-bridge restart [--studio\|--router\|--proxy]` | Restart selected services |
| | `gemini-bridge status [--json]` | Full status of services, accounts, router, pool, and proxy |
| **Accounts** | `gemini-bridge account list [--json]` | List accounts, state, model count, and active cooldowns |
| | `gemini-bridge account remote-info [--host <ip>]` | Print SSH tunnel & Add Remotely instructions |
| | `gemini-bridge account import-cookie --file <path>` | Import cookies or `storage-state.json` |
| | `gemini-bridge account verify\|pause\|resume <id>` | Refresh, pause, or resume an account |
| | `gemini-bridge account delete <id> --yes` | Remove an account |
| | `gemini-bridge account test <id> [--kind chat\|responses\|tools]` | Run an isolated live test on one account |
| | `gemini-bridge account quota <id>` | Check cooldown/quota records for one account |
| **Router** | `gemini-bridge router use <9router\|omniroute>` | Switch active router |
| | `gemini-bridge router login [--password <pass>]` | Authenticate CLI to router dashboard |
| | `gemini-bridge router setup [options]` | One-shot setup for Chat + Responses routes & key |
| | `gemini-bridge router keys [--show]` | List client API keys |
| | `gemini-bridge router key create\|use\|delete\|show` | Manage client API keys |
| **Models & Tests** | `gemini-bridge models [--route studio\|web\|router]` | List real available models |
| | `gemini-bridge test [--route router\|studio\|web] [--kind ...]` | Run live inference test |
| **Pool** | `gemini-bridge pool status` | Inspect Single/Parallel capacity |
| | `gemini-bridge pool mode <single\|parallel> [--concurrency N]` | Set worker concurrency |
| | `gemini-bridge pool restore` | Stop manual account endpoints and restore shared pool |
| **HTTPS Proxy** | `gemini-bridge proxy status\|install\|setup\|start\|stop\|reload\|test\|logs` | Full Caddy HTTPS lifecycle |
| **Config & Ops** | `gemini-bridge systemd [--user <name>] [--install]` | Generate or install `systemd` unit |
| | `gemini-bridge config get\|set <key> <value>` | View or update settings |
| | `gemini-bridge diagnostics` | Print redacted diagnostics JSON |

---

## Security Notes

- **Headless Vault**: On Linux without a desktop keyring (`DBUS_SESSION_BUS_ADDRESS` unset), the CLI automatically sets `GEMINI_BRIDGE_VAULT=file`, encrypting stored router sessions and API keys with **AES-256-GCM** (`0600`). You may optionally supply your own master passphrase via `GEMINI_BRIDGE_VAULT_KEY` (16+ characters).
- **Never expose raw admin ports**: Keep port `2048` (AI Studio) and `20128`/`20129` (9Router/OmniRoute) on `127.0.0.1` and use SSH tunneling when administering accounts.
- **Never share secrets**: Never post client API keys (`sk-...`), cookie exports, or `~/.local/share/app.geminibridge.desktop/.vault.key`.
