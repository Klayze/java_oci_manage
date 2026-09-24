# Implemented Features

[简体中文](../function.md)

---

## Telegram Bot — Oracle Cloud (OCI)

- [x] Boot instances (AMD / ARM / Intel, custom configuration)
- [x] IP management (query, change, auto DNS update)
- [x] Boot suspended instances
- [x] IPv6 management (attach, change)
- [x] Scale up / down instances (CPU, memory)
- [x] Instance management (delete, rename, force restart, reset OS, monitoring toggle)
- [x] Disk management (resize, performance tuning, detach/attach, delete)
- [x] Cloud account management (add admin, reset password, query emails, delete users)
- [x] Instance status monitoring + alert notifications
- [x] Instance status monitoring + auto-restart
- [x] One-click open all security group ports
- [x] Oracle workflow error query
- [x] Multi-profile / multi-client management
- [x] One-click account health check (batch detect all account status)
- [x] Auto IP change monitoring (with DNS binding, IP range filtering)
- [x] Quick boot (save configs, batch multi-profile boot)
- [x] Boot notification marks account type (upgraded / regular, by subscription paymentModel)
- [x] Oracle subscription info query
- [x] Last 3 months traffic query
- [x] Quota query (instances, network, storage)
- [x] Running task viewer
- [x] Client load viewer
- [x] Smart memory occupation (fill to 25%)
- [x] Last 3 months cost query
- [x] Clear all 2FA devices
- [x] One-click disable banned accounts
- [x] Delete API key
- [x] Daily cost and traffic reports
- [x] Batch email query
- [x] Cloudflare domain quick actions
- [x] Local mode without public IP

## Telegram Bot — AWS

- [x] EC2 instance management (list, start, stop, reboot, terminate)
- [x] Custom EC2 instance creation (AMI, instance type, key pair selection)
- [x] Lightsail instance management (list, start, stop, reboot, delete, traffic query)
- [x] Change IP
- [x] Cost query
- [x] Quota usage query

## Telegram Bot — GCP

- [x] Compute Engine instance management (list, start, stop, reset, delete)
- [x] Custom instance creation (zone, machine type, image, disk size, SSH key)
- [x] Change IP
- [x] Overview stats (instance counts, zone distribution, free tier count)
- [x] Last 3 months traffic query

## Telegram Bot — Azure

- [x] Custom boot
- [x] Change IP
- [x] Delete instance
- [x] Query quota usage
- [x] Delete all resources

## Telegram Bot — DigitalOcean

- [x] Droplet management (list, detail, power on, power off, reboot, destroy)
- [x] Multi-profile switching
- [x] Per-droplet monthly traffic query

## Telegram Bot — VirtFusion

- [x] Vendor-grouped instance listing
- [x] Instance detail (state, CPU, memory, disk, IPv4/IPv6, traffic, creation time)
- [x] Start / Stop / Restart / Force power off
- [x] System password reset

## Telegram Bot — SolusVM

- [x] SolusVM panel VPS management

---

## Web SSH Terminal

- [x] In-browser SSH connections (password / private key auth, SOCKS5 proxy support)
- [x] Multi-tab parallel terminals
- [x] SFTP file management (browse, upload, download, delete files/folders, create directories)
- [x] SFTP online text editor (syntax highlighting, edit server files directly in browser)
- [x] SFTP transfer manager panel (concurrent multi-task progress, speed, cancel, recently-finished history)
- [x] Exec-channel file transfer fallback for hosts without an SFTP subsystem (OpenWrt/dropbear/busybox still transfer files)
- [x] SSH port forwarding (local / remote)
- [x] SSH auto-reconnect on disconnect (exponential backoff, supports reboot / network interruption scenarios)
- [x] Batch commands (send to multiple hosts simultaneously, result workbench with continuous execution)
- [x] Terminal toolbar (favorites, search, quick tool access)
- [x] Terminal WebGL rendering (xterm.js 6.0, auto-fallback to DOM) + official search addon (highlight / overview ruler / count)
- [x] Multi-line paste protection (whole block stops on the input line, press Enter to run, prevents accidental script execution)
- [x] Terminal image paste (shortcut / right-click / drag-and-drop; the image is uploaded to the remote host and its path pasted, so Claude Code and Codex can attach it)
- [x] Host tags & group filtering (inline tagging on cards, AND-intersection filter, hit count, search matches tags)
- [x] Terminal split-screen (horizontal split into two independent panes, one-click clone of current session, refresh persistence)
- [x] Session suspension (screen-style explicit suspend + reattach from list, up to 20 per user)
- [x] Persistent shell (shell process keeps running on server after browser close, auto-reattach on next open)
- [x] Resource alerts (CPU / memory / disk threshold alerts pushed via Telegram)
- [x] Multi-cloud health check panel (one-screen overview of instance status across all cloud platforms)
- [x] Session profile save & management
- [x] Centralized SSH key management (encrypted storage, concurrent smart matching)
- [x] Host fingerprint verification (SHA256; stored fingerprints are checked during key exchange, before authentication, so passwords never reach an impersonating host)
- [x] Auto host specs detection (OS, CPU, memory, disk)
- [x] OCI Object Storage management (bucket browsing, file CRUD)
- [x] Resource monitor panel (top bar displaying real-time CPU / memory / disk / network metrics)
- [x] ACME auto SSL certificates (Let's Encrypt)
- [x] Cloud host sync (one-click discover hosts from OCI/AWS/GCP/Azure/DO/SolusVM/VirtFusion and import to session list, real-time SSE progress)
- [x] Cloud platform config online upload, editing & management (OCI/AWS/GCP/Azure/DO/SolusVM/VirtFusion, merge mode + inline edit/delete single Profile + secret masking + hot-reload on save)
- [x] MCP access (AI agents operate authorized hosts through the panel; read-only and executable token scopes, host allowlist, expiry, instant revocation, call auditing, and SSH credentials never leave the panel)
- [x] Web client upgrade (version check, upgrade, force upgrade, restart service, with a trigger cooldown against duplicate launches)
- [x] Web client run log (line count selection, keyword filtering, auto-refresh, copy-all)
- [x] Telegram verification code login + anti-brute-force
- [x] Chinese/English interface switching
- [x] Page state memory (auto-restore position after refresh, cloud management sub-page state sync)
- [x] Online support IM (built-in chat window with image message support)
- [x] Responsive layout (mobile-friendly)
- [x] HTTPS (TLSv1.3) + HTTP/2

---

## Web Cloud Management Panel

### Oracle Cloud

- [x] Instance management (create, quick boot, Force ARM boot, start, stop, reboot, terminate, reset OS, scale, rename, repair)
- [x] Quick config launch (AMD Micro 1C/1G and ARM A1 2C/12G presets filling shape, image, key, and retry delay)
- [x] Create from an existing boot volume (single instance, automatically constrained to the volume's availability domain)
- [x] Instance list with boot volume info merged inline
- [x] Force ARM boot (improve ARM creation success rate for trial accounts, supports Web + Telegram)
- [x] Network management (change IP, attach IPv4/IPv6, reserved IP, delete IP)
- [x] Volume management (resize, VPU performance, detach, attach, delete, batch VPU, with lifecycle and attachment badges shown for every volume)
- [x] Boot volume reattachment (attach a detached boot volume back onto a stopped instance; instances with no attached volume jump straight to the unattached-volume panel)
- [x] Instance creation result polling (the web UI checks task status after submitting and shows the final result)
- [x] A1 config audit / downscale (parallel scan of each account's A1.Flex usage vs the always-free cap; account-level / batch / per-instance preemptive downscale, downscale-only, never auto-deletes instances)
- [x] User management (create, delete, reset password, update email, clear MFA, rename tenant, view identity domain password policy)
- [x] Statistics overview (cost, traffic, subscription info, quota)
- [x] Profile management (list, switch, delete)
- [x] One-click Profile copy to a new region (OCI / AWS; credentials copied server-side, only the region is replaced)
- [x] Per-Profile API outbound proxy (web panel + Telegram, with proxied accounts flagged on the overview page)
- [x] Object Storage management (bucket browsing, file upload/download/delete)
- [x] Instance monitoring alerts / auto-start / daily report / health check
- [x] Serial Console (OCI instance serial console connection, WebSocket real-time terminal, Netboot.xyz rescue boot automation)
- [x] Email Delivery (one-click email domain setup, DKIM/DNS auto-config, DKIM repair, add sender, SMTP credential management, test send)

### AWS

- [x] EC2 instance management (list, start, stop, reboot, terminate)
- [x] Create EC2 instances (AMI selection, instance type, key management, async creation)
- [x] Lightsail instance management (list, start, stop, reboot, delete, current-month traffic monitoring)
- [x] Lightsail instance creation (region, availability zone, blueprint, bundle, key pair, name, count; bundles filtered by blueprint platform, local public keys importable)
- [x] EC2 firewall / security group management (attach, detach, create, delete groups; add and remove inbound and outbound rules; one-click common presets)
- [x] Lightsail network / IP management (static IP allocate/attach/detach/release, change static IP, reboot to change dynamic IP, firewall ports)
- [x] Network management (VPC, security groups)
- [x] Cost statistics (Cost Explorer integration)
- [x] Quota usage query

### GCP

- [x] Compute Engine instance management (list, create, start, stop, reset, delete, change IP)
- [x] Overview stats (instance counts, zone distribution, free tier stats)
- [x] Traffic query (last 3 months sent/received traffic breakdown)

### Cloudflare DNS

- [x] Zone listing, DNS record CRUD operations
- [x] API Token authentication (minimal scope, coexists with Global API Key, token takes precedence)

### DigitalOcean

- [x] Droplet management (list, create, power on, power off, reboot)
- [x] Reserved IP management (allocate, assign, unassign, release)
- [x] Bandwidth monitoring (monthly usage and quota)
- [x] Billing overview

### Azure

- [x] VM management (list, create, delete, restart, change IP)
- [x] Resource usage query

### SolusVM

- [x] VPS management (node list, dashboard, boot/shutdown/reboot)

### VirtFusion

- [x] Instance card management (vendor grouping, state/spec/IP/created-time display)
- [x] Traffic progress and current-period usage
- [x] One-click SSH connection
- [x] Start / Stop / Restart / Force power off
- [x] Instance rename
- [x] System password reset

### Cloud & Domain Monitoring

- [x] Traffic Guard (per-account monthly traffic threshold; notify only, or automatically stop every instance on that account across all regions)
- [x] Uptime Guard (unexpected-stop notifications and auto-restart; accounts stopped by Traffic Guard stay down for the rest of the month)
- [x] Domain and SSL certificate expiry monitoring (daily scheduled checks, tiered Telegram alerts at 30/14/7/1 days and expired)
- [x] One-click import of Cloudflare-hosted domains, with punycode conversion for internationalized domain search

### General

- [x] Cloud instance quick SSH — all cloud platform instance cards support direct SSH terminal connection

---

## Cloud Host Sync

- [x] Multi-cloud host discovery (OCI / AWS EC2 / AWS Lightsail / GCP / Azure / DO / SolusVM / VirtFusion parallel queries)
- [x] Auto-import to SSH session list (IP deduplication, IPv6 support)
- [x] Real-time SSE progress feedback (per-platform query status)

---

## In Development

- [ ] More cloud platform management features in progress
