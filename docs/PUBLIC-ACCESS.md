# Public access with Caddy

Version 1.4.1 gives every card and disclosure a consistent 24px gap, including stacked layouts. Address/HTTPS and Caddy installation use equal columns on wide screens and separate full-width cards on smaller screens. Installation status, folder actions and sign-in startup have distinct groups; client values have their own separated rows. The existing actions and workflow are preserved.

In Gemini Bridge Pro 1.3.0, open **9Router → Public access**. Connection setup stays in the adjacent **Connection** tab; the sidebar is unchanged.

1. Connect and verify 9Router with an active client API key.
2. Install Caddy into an empty folder, find an existing installation, or import a folder containing `caddy.exe`. The managed download is the official Windows x64 Caddy 2.11.7 archive, checked against its pinned SHA-256. Imported binaries need Caddy 2.11 or newer.
3. Enter the public IP or domain of the machine running the app. The upstream follows the loopback 9Router address in Settings.
4. Save and validate the configuration, then start the proxy. IP addresses use ACME `profile shortlived` and `default_sni`; domains use automatic HTTPS. Caddy maintains certificate renewal while running.
5. Allow TCP ports 80 and 443 in Windows and the RDP provider’s firewall. The Windows firewall control asks for Windows administrator approval and manages only this application’s named, program-scoped rules. Provider firewall and DNS changes are made in the provider’s control panel.
6. Run **Test public connection**. It checks certificate-verified HTTPS, dashboard isolation, rejection of missing/invalid API keys, and a model catalog with the selected client key. The optional Responses test sends an actual request and uses the backend’s quota.
7. Copy the HTTPS Base URL, the full model ID including `studio/` or `web/`, and an active 9Router client key into the other application.

The proxy publishes `/v1/*`. Dashboard and administrative paths return 404. Before starting or reloading, Gemini Bridge Pro ensures that 9Router requires an API key and rejects an invalid key. Router settings and Google credentials are never copied into the Caddyfile. Authentication remains in 9Router, so active client keys are managed through the Connection tab.

Import reads a recognizable host and certificate email from an existing `Caddyfile`. The original is preserved; GUI changes use `Caddyfile.geminibridge`, with backups before replacement. The GUI intentionally manages one API host. Arbitrary multi-site/plugin configuration in an imported Caddyfile is not executed or rewritten by this workflow. A Caddy service running outside the app is not stopped or adopted. Stop it separately if it occupies ports 80/443, then start the managed instance.

Relocation copies and checks the executable and managed configuration into an empty destination, then updates the active folder. Previous files are retained. Certificate storage and bounded log reads use the GeminiBridge data directory, so moving the executable does not discard certificate state. After relocation, use Allow Caddy through firewall again if needed: Windows rules are scoped to the executable’s path. Caddy keeps running after the GUI closes; Start/Stop/Reload control only a process whose Windows executable, command line, PID and creation time match the app’s record. Optional sign-in startup is scoped to the current Windows user and retries while 9Router becomes ready. It does not install a machine-wide Windows service. 9Router and its backends must also be running.

A successful server-side test does not prove reachability from every external network. Confirm the endpoint from another device/network after configuring firewall, DNS and any NAT forwarding. A running Caddy process is shown separately from successful HTTPS or model generation. No Google login, inference or public certificate is fabricated.

Test results show their timestamp and expire after 24 hours. Run the test again after changing API keys, model settings or networking.

Sources: [Caddy releases](https://github.com/caddyserver/caddy/releases/tag/v2.11.7), [automatic HTTPS](https://caddyserver.com/docs/automatic-https), [TLS issuer configuration](https://caddyserver.com/docs/caddyfile/directives/tls), [default SNI](https://caddyserver.com/docs/caddyfile/options#default-sni), [commands and graceful reload](https://caddyserver.com/docs/command-line), [reverse proxy](https://caddyserver.com/docs/caddyfile/directives/reverse_proxy).
