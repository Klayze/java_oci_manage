# MCP Access

[简体中文](../mcp.md)

Let AI agents like Claude Code, Codex, and Cursor work on your hosts through the panel.

The point is that credentials never reach the agent: SSH passwords and private keys stay in the panel, and the agent only gets a token. Which hosts it can touch and whether it can run commands are fixed when you issue that token, and you can revoke it at any time.

Settings → "MCP Access" in the top bar. Requires Lightning.

---

## First: the certificate

**Claude Code and Codex reject self-signed certificates.** The panel ships with a self-signed one, so the agent will not connect, and the error it reports usually does not look like a certificate problem.

Two ways around it:

- Enable ACME under Settings → SSL Certificate Settings and get a Let's Encrypt certificate (you need a domain — see [Web SSH Terminal Guide — SSL Certificates](./webssh.md#ssl-certificate-configuration))
- Or put a reverse proxy in front with a certificate you already trust

The MCP page warns you when ACME is off. Do not skim past that warning.

---

## Issuing a token

Click "New Token". Four fields:

| Field | Notes |
|------|------|
| **Name** | Whatever you will recognize later, e.g. `claude-code-laptop`. This is how you tell tokens apart when revoking |
| **Scope** | Read-only or executable, see below |
| **Expiry** | Expires on its own |
| **Authorized Hosts** | Picked from your saved sessions. Hosts you do not tick are unreachable with this token. Save some hosts first if the list is empty |

Two scopes:

| Scope | What it allows |
|------|---------|
| **Read-only** | List hosts and read files. No command execution |
| **Executable** | The above plus running commands and writing files — equivalent to that SSH account's shell |

The token is **shown once**. The same screen hands you three ready-made config snippets: Claude Code, Codex CLI (`~/.codex/config.toml`), and generic JSON for Cursor and others. Copy the one you need into your client config; there is no endpoint URL to assemble by hand.

---

## What the agent can do

Phase one covers SSH, with six tools:

| Tool | Scope | Purpose |
|------|------|------|
| `ssh_list_hosts` | Read-only | List the hosts this token is authorized for |
| `ssh_read_file` | Read-only | Read a remote file |
| `ssh_exec` | Executable | Run a command. Returns the result directly if it finishes within 55 seconds, otherwise hands back a job id |
| `ssh_job_read` | Executable | Read a background job's output by id |
| `ssh_job_cancel` | Executable | Cancel a background job |
| `ssh_write_file` | Executable | Write a remote file, preserving the original permissions when overwriting |

Long-running commands do not block the agent — once a command becomes a background job, the agent can go do something else and collect the output later.

Every call is checked item by item: the token is valid and not revoked, the scope covers the tool, and the target host is on that token's list. Host fingerprints are verified against what the panel has on record, and a host configured to use a proxy is refused outright if that proxy is unavailable rather than falling back to a direct connection.

---

## Two things to get right

They are on the page too. Repeating them here because this is where it goes wrong.

**1. Give the agent its own account, without sudo.**

An agent that can run commands can leave itself a backdoor. Do not hand it root or the account you use yourself.

```bash
# a dedicated account on the server, no sudo
useradd -m -s /bin/bash aiagent
```

Then keep that account's `authorized_keys` in a root-owned directory so the agent cannot edit its own access:

```
# /etc/ssh/sshd_config
AuthorizedKeysFile /etc/ssh/keys/%u
```

Save the host in the panel under that account, and authorize only that host on the token.

**2. Command output goes to the AI provider as context.**

Whatever the agent reads, the provider sees. Keep it away from private keys, config files holding passwords, and database dumps. A read-only token does not help here — read-only restricts writing, not looking.

---

## Revoking and auditing

Each token in the list shows how many hosts it covers, when it expires, and when it was last used.

"Revoke" takes effect immediately: the agent loses access on the spot, and **any background jobs it has running are cancelled with it**.

Below that, "Call Log" is the full audit trail — issuance, revocation, and every tool call. It can be refreshed and cleared.
