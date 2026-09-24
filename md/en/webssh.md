# Web SSH Terminal Guide

[简体中文](../webssh.md)

The R-Bot client includes a full-featured Web SSH terminal. Connect to and manage remote servers directly from your browser — no client software required.

---

## Access

After starting the client, visit:

```
https://YOUR_IP:9527
```

> Uses HTTPS + TLSv1.3 by default. On first visit, the browser will warn about the self-signed certificate — you can trust it manually or configure ACME auto-certificates.

---

## Page Layout

After login, the main interface uses a **four-view switching** layout:

```
┌─────────────────────────────────────────────────────┐
│ Top Bar: Brand | Language | Theme | Lightning |      │
│          Settings | Logout                           │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Main Content (View Switching)   Config Drawer      │
│  · Host Dashboard                (slides from right)│
│  · Terminal Workspace                               │
│  · Cloud Management                                 │
│  · Lightning Benefits                               │
│                                                     │
└─────────────────────────────────────────────────────┘
```

| Area | Description |
|------|-------------|
| **Top Bar** | Brand logo "R·Cloud·SSH", language selector, theme picker (8 themes), Lightning entry, settings menu (SSL Certificates / Config Files), light/dark mode toggle, logout |
| **Host Dashboard** | Default view after login — displays all saved host sessions as a card grid with search, cloud host sync, add session, and batch selection |
| **Terminal Workspace** | Auto-switches when connecting to a host — includes tab bar, terminal panes, and SFTP panel (drag-resizable height) |
| **Cloud Management** | Multi-cloud resource management workbench — see [Cloud Management Guide](./cloud.md) |
| **Config Drawer** | Right-sliding panel for connection details, authentication, and port forwarding configuration |

---

## Login Authentication

Two login methods are supported:

### Password Login

Log in directly using the `username` and `password` configured in `client_config`.

### Telegram Verification Code

1. Click "Send Verification Code"
2. The bot sends an 8-digit code via Telegram
3. Enter the code within 60 seconds to log in

> Anti-abuse protection: After 3 consecutive requests within 10 minutes, a CAPTCHA (arithmetic question) is triggered to prevent brute-force attacks.

![Login Page](../../screenshots/login.jpg)

---

## SSH Connections

### New Connection

1. Click "Add Session" in the host dashboard — the config drawer slides in from the right
2. Fill in connection details:
   - **Session Name** — Custom name (optional)
   - **Host** — IP address or domain name
   - **Port** — Default 22
   - **Username** — SSH login user
   - **Auth Method** — Password or private key
3. Click "Connect" to auto-switch to the terminal workspace

### Authentication Methods

| Method | Description |
|--------|-------------|
| **Password** | Enter SSH password |
| **Private Key** | Paste key content or select a saved key; supports passphrase |

### SOCKS5 Proxy

SSH connections support SOCKS5 proxy for restricted network environments. Configure the proxy address and port in the config drawer.

### Host Fingerprint Verification

On first connection, the SHA256 host fingerprint is displayed for confirmation and saved. If the fingerprint changes on subsequent connections (possibly due to server reinstallation or a security risk), a warning is shown.

For connections with a stored fingerprint, verification happens during key exchange, **before authentication**: a host key that does not match ends the connection, so the password or private key is never sent. Connections without a stored fingerprint behave as before.

### Quick Connect

In the host dashboard, click the "Quick Connect" button on a saved host card to connect directly without re-entering details. Click the "Load" button to open the config drawer for editing connection parameters.

### Auto-Reconnect

When an SSH connection is interrupted (e.g., server reboot or network fluctuation), the terminal automatically attempts to reconnect using an exponential backoff strategy — no manual action needed to restore the session.

### Multi-Tab

Multiple SSH connections can be open simultaneously, each in its own tab, freely switchable. The tab bar also integrates system resource monitoring metrics (CPU / Memory / Disk / Network).

> The terminal is built on xterm.js 6.0 with WebGL rendering (auto-fallback to DOM where unsupported) for smoother heavy output; search uses the official addon with full-match highlighting, overview-ruler markers, and a hit counter.

![Terminal Interface](../../screenshots/terminal.jpg)

---

## Terminal Toolbar

After connecting, the toolbar provides quick access to common features:

- **Favorites** — Quickly bookmark the current session
- **Search** — Search terminal content
- **Tools** — Quick access to common operations
- **Split** — Split the current terminal horizontally into two independent panes
- **Suspend** — Explicitly suspend the current session; it remains on the server after closing the tab

---

## Multi-Line Paste Protection

When you right-click-paste multi-line commands, the entire block is placed on the input line but **not executed automatically** — press Enter once more to run it (trailing newline stripped + bracketed-paste negotiation), preventing an accidentally pasted script from running immediately. Single-line paste keeps the original auto-execute behavior. A subtle hint is shown when the remote side does not enable bracketed paste.

---

## Terminal Image Paste

Paste images into the terminal. The image is uploaded to a temporary directory on the remote host and what lands in the terminal is its absolute path there — which is all Claude Code, Codex, and similar tools need to attach it.

In a local terminal those tools read your system clipboard. In a web terminal they run on the remote side and see an empty clipboard there, and the terminal channel itself carries only text, hence this route.

| Entry point | Key / action |
|------|-----------|
| Shortcut | `Cmd+V` on Mac, `Ctrl+Shift+V` on Windows and Linux |
| Right-click | Right-click paste |
| Drag and drop | Drop an image file onto the terminal |

PNG / JPG / GIF / WebP, up to 20 MB each. Multiple images paste in order, separated by spaces.

Whenever the clipboard holds text it is pasted as text, exactly as before. Remote temporary files older than 24 hours are cleaned up automatically.

---

## Terminal Split-Screen

Click the "Split" button in the toolbar to split the terminal into two independent panes, each with its own SSH connection — ideal for monitoring logs on one side and running commands on the other.

- **One-click clone** — Connect the second pane to the current host automatically
- **Independent connections** — Each pane has its own WebSocket
- **Narrow-screen collapse** — Panes auto-collapse on narrow viewports and restore when widened
- **Refresh persistence** — Split layout is restored after page refresh

---

## Session Suspension

Similar to Unix `screen`, you can explicitly suspend the current SSH session without disconnecting. The session stays alive on the server after closing the tab and can be resumed from the suspended list with command history intact.

| Action | Description |
|--------|-------------|
| Suspend Session | Click the "Suspend" button in the toolbar |
| Reattach | Resume from the "Suspended" entry on the host dashboard |
| Group Suspend | In split mode, supports suspending and reattaching as a group |
| TTL Limit | Up to 20 suspended sessions per user; cleared on client restart |

---

## Persistent Shell & Alerts

R-Bot provides an SSH retention suite for long-running operations:

- **Persistent Shell (reattach)** — Shell process keeps running on the server after browser close or network drop, auto-reattaches on next open with command history preserved
- **Resource Alerts** — Configure CPU / memory / disk thresholds; alerts pushed to Telegram on breach
- **Multi-Cloud Health Check** — One panel summarizing instance status and DNS info across all cloud platforms

---

## SFTP File Manager

After connecting via SSH, click the "SFTP" button in the tab bar to open the file management panel. The SFTP panel appears below the terminal and supports **drag-resizing** the divider to adjust the height ratio between terminal and SFTP.

### Features

| Action | Description |
|--------|-------------|
| **Browse Directories** | Click folders to enter; breadcrumb navigation for quick jumping |
| **Upload Files** | Chunked upload with progress display |
| **Download Files** | Streaming transfer with 512KB chunks + sliding window acknowledgment |
| **Online Edit** | syntax highlighting — edit server files directly in the browser |
| **Delete Files/Folders** | Inline confirmation, supports recursive directory deletion |
| **Create Directory** | Create new folders in the current path |

### Transfer Manager

Uploads and downloads are consolidated into a dedicated **Transfer Manager panel**:

- Per-task progress bars for concurrent transfers, with live speed and percentage
- Collapsible to a pill, keeps a recently-finished history, cancel a single transfer or all at once
- In split mode, each pane's transfer state is isolated — no cross-talk

### Fallback for Hosts Without SFTP

When connecting to hosts without an SFTP subsystem (OpenWrt / dropbear / busybox, etc.), the file panel automatically falls back to the exec channel — browse, upload, download, create directory, delete, and online edit all keep working transparently; a compatibility banner at the top of the panel indicates the degraded transfer mode.

<!-- Screenshot placeholder: SFTP file management panel -->
<!-- ![SFTP Panel](../../screenshots/sftp.png) -->

---

## Port Forwarding

Configure SSH port forwarding in the config drawer (while connected).

### Local Forward

Map a remote service to a local port (e.g., access a remote database):

| Parameter | Example |
|-----------|---------|
| Bind Address | `127.0.0.1` |
| Bind Port | `3306` |
| Target Address | `127.0.0.1` |
| Target Port | `3306` |

### Remote Forward

Expose a local service to a port on the remote server.

<!-- Screenshot placeholder: Port forwarding configuration -->
<!-- ![Port Forwarding](../../screenshots/port-forward.png) -->

---

## Batch Commands

Send the same command to multiple hosts simultaneously — ideal for batch operations.

1. In the host dashboard, check the target host cards (multi-select supported)
2. A batch toolbar automatically appears at the bottom showing the selected count
3. Enter the command in the input field and click execute
4. Enter the result workbench — results are displayed in real-time as grid cards showing each host's execution status and output
5. Continue executing new commands directly in the result workbench without re-selecting hosts

---

## Session Management

### Host Dashboard

The default view after login displays all saved sessions as a **responsive card grid**. Each card shows:

- Session name and description
- Host specs (OS, CPU, memory, disk — auto-detected after connection)
- IP address(es) with one-click copy
- Last modified timestamp
- Action buttons: Quick Connect, Load Config, Delete

The dashboard header provides a search box (filter by name / IP) and an "Add Session" button.

### Host Tags & Filtering

Tag host cards to quickly group and filter large session lists:

- **Add / Remove Tags** — Type a tag name inline on a card and press Enter to add; click the × on a tag to remove it (up to 12 tags per host, 24 chars each)
- **Tag Filter Bar** — A tag filter row at the top of the dashboard; selecting multiple tags filters by intersection (AND) and shows a hit count
- **Expand / Collapse / Clear** — Collapse when there are many tags; clear all filters in one click
- **Search Matches Tags** — The search box matches session name, IP, and tags

### Save Sessions

After a successful connection, save the current configuration as a session from the config drawer for one-click reconnection from the host dashboard. Saved information includes:

- Session name, description
- Host, port, username
- Auth method and credentials (encrypted storage)

### SSH Key Management

Centralized management of all SSH private keys:

- Add / delete keys
- Concurrent smart matching of saved keys when connecting
- Keys stored with AES encryption

<!-- Screenshot placeholder: Session list -->
<!-- ![Session Management](../../screenshots/sessions.png) -->

---

## Cloud Host Sync

One-click discover hosts from multiple cloud platforms and auto-import them into the SSH session list — no manual entry needed.

### Supported Platforms

| Platform | Synced Content |
|----------|----------------|
| **Oracle Cloud (OCI)** | All instance public/private IPs, names, shapes, regions |
| **AWS** | EC2 and Lightsail instance public/private IPs, names, instance types / bundles, regions |
| **GCP** | Compute Engine instance public/private IPs, names, machine types, zones |
| **DigitalOcean** | Droplet public/private IPs, names, regions, sizes |
| **Azure** | VM IP addresses and instance info |
| **SolusVM** | VPS node IP addresses |
| **VirtFusion** | First public IPv4 / IPv6, instance name, and vendor alias |

### How It Works

1. Click the "Cloud Host Sync" button at the top of the host dashboard
2. The system queries all configured cloud platform Profiles in parallel
3. Real-time progress is displayed via SSE (Server-Sent Events) for each platform
4. Newly discovered hosts are automatically added to the host dashboard (existing IPs are skipped)
5. After sync completes, a summary shows added count, skipped count, and any errors

> Synced hosts default to SSH port 22, with automatic IPv6 address detection.

---

## OCI Object Storage Management

Manage OCI Object Storage directly from the web interface:

| Action | Description |
|--------|-------------|
| **Browse Buckets** | List all storage buckets |
| **Browse Objects** | Prefix filtering and pagination |
| **Upload Files** | Streaming upload with path traversal prevention |
| **Download Files** | Streaming download |
| **Delete Objects** | Delete individual files |
| **Create Folders** | Create directory structures |

<!-- Screenshot placeholder: OCI Object Storage interface -->
<!-- ![OCI Object Storage](../../screenshots/oss.png) -->

---

## SSL Certificate Configuration

### Built-in Self-Signed Certificate

The client ships with a self-signed PKCS12 certificate — works out of the box.

### ACME Auto-Certificate (Let's Encrypt)

Supports automatic issuance and renewal of Let's Encrypt certificates. Requirements:

1. A domain name pointing to your server
2. Port 80 accessible (for HTTP-01 verification)

Configure in the web interface under "Settings":

| Parameter | Description |
|-----------|-------------|
| **Domain/IP** | Domain to bind the certificate to |
| **Email** | Let's Encrypt notification email |
| **Challenge Port** | HTTP-01 verification port, default 80 |

Once configured, certificates are issued automatically and renewed 2 days before expiry.

---

## Multi-Language Support

The web interface supports Chinese/English switching via the language selector in the top bar.

- 简体中文 (zh-CN)
- English (en)

---

## Technical Specifications

| Item | Specification |
|------|---------------|
| Protocol | HTTPS (TLSv1.3) + WebSocket |
| Terminal | xterm.js 6.0 (WebGL rendering with DOM fallback) |
| Max Connections | 200 concurrent |
| Max Message Size | 8 MB |
| Stale Connection Cleanup | 30-minute timeout with auto-reclaim |
| Download Transfer | 512 KB chunks + sliding window (4 windows) |
