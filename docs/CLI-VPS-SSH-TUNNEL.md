# Why `127.0.0.1:2048` (Not Your VPS IP) Works via SSH Tunnel

This guide explains how **SSH Local Port Forwarding (`ssh -L`)** connects your local browser and `aistudio2api` CLI directly to your **VPS**, how to avoid port conflicts if you also run **Gemini Bridge Pro Desktop** locally, and when to use a custom HTTPS domain instead.

---

## 1. Why Not Open `http://<YOUR_VPS_IP>:2048` Directly?

By default, Magpie (`AIStudio2API`) on your VPS binds strictly to **`127.0.0.1:2048`** (the VPS loopback interface) for security:
- Nobody on the public internet can reach your Google account admin panel or brute-force your login.
- Your admin password, pairing tokens, and session cookies never travel over unencrypted HTTP (`http://<VPS_IP>:2048`).
- Because it only listens on `127.0.0.1` inside the VPS, opening `http://<YOUR_VPS_IP>:2048` directly in your local browser will fail with *Connection refused* or timeout.

---

## 2. How the SSH Tunnel Connects `127.0.0.1` on Your PC to the VPS

When you run this command **on your local computer**:

```bash
ssh -L 2048:127.0.0.1:2048 <vps-user>@<YOUR_VPS_IP>
```

SSH creates an encrypted tunnel between your local machine and the VPS:

```text
Your Local PC                                              Your Linux VPS
┌────────────────────────────────────┐                    ┌────────────────────────────────┐
│ Browser / aistudio2api.exe         │                    │ AIStudio2API (Magpie)          │
│ -> http://127.0.0.1:2048           │ ═══ SSH Tunnel ══▶ │ listening on 127.0.0.1:2048    │
│    (local port 2048)               │     (encrypted)    │ (remote loopback port 2048)    │
└────────────────────────────────────┘                    └────────────────────────────────┘
```

While that SSH terminal window stays open:
1. Opening **`http://127.0.0.1:2048`** in your local browser actually loads the **AIStudio2API admin panel running inside your VPS** — not your local PC.
2. Running **`aistudio2api.exe setup --remote http://127.0.0.1:2048 --token <TOKEN>`** on your local PC sends the authenticated Google session **through the SSH tunnel straight into your VPS disk** (`~/.local/share/magpie-ai-studio/auth/`).
3. As soon as `gemini-bridge account list` on the VPS shows **`ready`**, you can close the SSH tunnel and shut down your local PC. The accounts remain stored on the VPS and continue serving 24/7.

---

## 3. Running Gemini Bridge Pro Desktop on the Same PC? Use Port `2049`!

If you **also** run **Gemini Bridge Pro Desktop** (or a local `AIStudio2API` instance) on your Windows/macOS PC, your local port `2048` is already occupied by your local instance.

To guarantee you are talking to your **VPS** and never colliding with your local Desktop app, map local port **`2049`** to remote port **`2048`**:

```bash
ssh -L 2049:127.0.0.1:2048 <vps-user>@<YOUR_VPS_IP>
```

Then:
1. Open **`http://127.0.0.1:2049`** in your local browser.
2. Sign in to your VPS AIStudio2API panel.
3. Click **Add account → Add remotely**.
4. Change **Instance address** to **`http://127.0.0.1:2049`**.
5. Copy and run the generated command on your local PC:
   ```powershell
   aistudio2api.exe setup --remote http://127.0.0.1:2049 --token <PAIRING_TOKEN>
   ```

| Port | Where It Points |
| --- | --- |
| `http://127.0.0.1:2048` | Your local Windows/macOS Desktop AIStudio2API (if running) |
| `http://127.0.0.1:2049` | Your remote Linux VPS AIStudio2API (via SSH tunnel) |

---

## 4. Alternative: Access Admin Panel via Your Own HTTPS Domain

If you prefer accessing the VPS AIStudio2API admin panel without opening an SSH tunnel each time, place it behind **HTTPS** on a separate subdomain (for example, `https://studio-admin.example.com`) proxied to `127.0.0.1:2048` (with strong login credentials and optional IP allowlist / firewall rules):

- Keep your public API on `https://api.example.com/v1` (managed by `gemini-bridge proxy setup`).
- In **Add account → Add remotely**, set **Instance address** to `https://studio-admin.example.com`.
- Run `aistudio2api.exe setup --remote https://studio-admin.example.com --token <PAIRING_TOKEN>` from your local PC.

> **Do not** expose `http://<YOUR_VPS_IP>:2048` directly over plain HTTP on the public internet.

---

Back to the main guide: [Gemini Bridge Pro CLI — Headless Linux VPS Guide](CLI-VPS.md)
