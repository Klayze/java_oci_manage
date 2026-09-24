# MCP Access

[简体中文](../mcp.md)

Let AI assistants such as Claude Code, Codex, and Cursor work on your servers through the panel.

Server passwords and private keys stay in the panel; the AI only gets a token. Which machines it can touch and whether it can run commands are decided when you issue that token, and you can take it back at any time.

Where: Settings → "MCP Access" in the top bar. Requires Lightning.

---

## Step 1: get a real certificate

**Claude Code and Codex cannot connect to a panel with a self-signed certificate.** That is what the panel uses by default, so without changing it the AI will not connect — and the error it shows usually does not say the certificate is why.

Enable ACME under Settings → SSL Certificate Settings, using either the server IP or a domain — see [SSL Certificates](./webssh.md#ssl-certificate-configuration). Skip this if you already serve the panel through a reverse proxy with a trusted certificate.

The MCP page shows a warning at the top until this is done.

---

## Step 2: issue a token

Click "New Token" and fill in four things:

| Field | Notes |
|------|------|
| **Name** | Anything you will recognize, e.g. `claude-code-laptop` |
| **Scope** | Read-only or executable, see below |
| **Expiry** | Stops working on its own after this |
| **Authorized hosts** | Tick the machines this token may use; anything unticked is off limits. If the list is empty, save some hosts on the host panel first |

| Scope | What it allows |
|------|---------|
| **Read-only** | See which machines there are and read files. No commands |
| **Executable** | Run commands and read / write files — effectively handing it that SSH account |

---

## Step 3: configure the AI client

The token is **shown only once**, and the same page gives you three ready-to-paste configs:

- Claude Code
- Codex CLI (goes in `~/.codex/config.toml`)
- JSON for Cursor and other clients

Copy the one you need into your AI client's config. The address and token are already filled in.

Once it is set up, just ask the AI things like "check disk usage on the web server". Long-running commands go to the background and it comes back for the result later.

---

## Staying safe

**1. Give the AI its own account without sudo.**

Anything that can run commands can leave itself a backdoor. Do not give it root or the account you use yourself.

Create a dedicated account on the server:

```bash
useradd -m -s /bin/bash aiagent
```

Then keep that account's `authorized_keys` somewhere it cannot edit, in `/etc/ssh/sshd_config`:

```
AuthorizedKeysFile /etc/ssh/keys/%u
```

Save the host in the panel under this account, and tick only that host when issuing the token.

**2. What the AI sees goes to the AI provider.**

Every file it reads and every command output is sent to the AI provider. Keep it away from private keys, config files containing passwords, and database backups. Read-only does not help here — it stops changes, not reading.

---

## Revoking and the call log

The token list shows how many machines each token covers, when it expires, and when it was last used.

"Revoke" takes effect immediately: the AI loses access at once, and **anything it has running is stopped too**.

The "Call Log" below shows everything the AI has done. It can be cleared.
