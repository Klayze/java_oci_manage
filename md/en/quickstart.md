# Getting Started

[简体中文](../quickstart.md)

One full pass: install the client, activate it, upload your Oracle API config, launch an instance, and connect to it.

Ten minutes or so, not counting the time Oracle makes you wait for capacity.

---

## 1. Install the client

Pick a machine you control that can expose a port (a VPS, a home server, a router — anything):

```bash
mkdir rbot && cd rbot
wget -O sh_client_bot.sh https://github.com/semicons/java_oci_manage/releases/latest/download/sh_client_bot.sh && chmod +x sh_client_bot.sh && bash sh_client_bot.sh
```

The script downloads the right version for your machine and keeps it running in the background on port 9527.

Your API private keys live on whichever machine you install this on. Choose accordingly.

No public IP? Edit `client_config` and set `model=local`. Everything then runs through the Telegram bot with no web endpoint exposed.

## 2. Activate

Open `https://your-ip:9527`. The certificate is self-signed, so your browser will complain — proceed anyway.

A red banner at the top of the page contains a `/bindclient username password` command. Copy it, send it to [@radiance_helper_bot](https://t.me/radiance_helper_bot), then refresh the page.

Reinstalling on a new server and want it under your existing account? Click "Already have an account?" in that red banner and enter your old username and password.

> Those credentials are stored in `client_config`. Keep a copy. If your Telegram account is ever banned, they are how you rebind.

## 3. Upload your Oracle API config

Get the API parameters from the Oracle console first: avatar menu → User Settings → Resources → API Keys → Add API Key → download the private key (.pem) → copy the whole configuration preview that appears afterwards.

The preview looks like this:

```ini
[DEFAULT]
user=ocid1.user.oc1..aaaaxxxx
fingerprint=b8:33:6f:xxxx:45:43:33
tenancy=ocid1.tenancy.oc1..aaaaxxxx
region=ap-singapore-1
key_file=<path to your private keyfile>
```

Other entry points and details: [Oracle Cloud API Configuration](./oracle.md).

Then upload it one of two ways:

**Web (recommended)** — Settings → Config File Settings → OCI. Paste that block, upload the `.pem` alongside it, and `key_file` gets filled in for you. It takes effect on save; no restart.

**Bot** — scp the private key to the client server yourself, note its path, set `key_file=` to that path, and send the whole block after `/oci`. The bot accepts the path, never the key file itself.

Multiple Oracle accounts means multiple blocks — `[DEFAULT]`, `[tokyo]`, `[osaka]`. The name in brackets is the profile name.

## 4. Launch your first instance

**Web** — Cloud Management → Instances → Create Instance. Click `ARM A1 2C/12G` or `AMD Micro 1C/1G` under Quick Config, pick an OS and an SSH key, submit.

**Bot** — send `/oracle`, tap "1. Launch (ARM)", answer each prompt, confirm twice.

Every parameter, how the retry loop works, and what to do when it fails: [Oracle Instance Launch Guide](./boot-oracle.md).

Launching runs as a background task. Out-of-capacity errors are retried automatically and the result arrives over Telegram — no need to watch the page.

## 5. Connect

The success notification carries the IP and the root password. That password is shown once and never stored.

In the web UI, click "SSH" on the instance card to jump straight into a terminal with the connection prefilled.

You can also add a session by hand from the host panel: IP, username, and either a password or a private key. Store keys once under Key Management and every session can reuse them.

Already have instances running? Cloud Host Sync pulls them in from OCI, AWS, GCP, Azure, DO, SolusVM, and VirtFusion in one shot.

---

## Where to go next

| Goal | Document |
|------|------|
| Understand every launch parameter | [Oracle Instance Launch Guide](./boot-oracle.md) |
| Rotate IPs, auto-update DNS, downscale A1, monitor domain expiry | [How-To Guide](./howto.md) |
| Bot commands and menus | [Bot Commands](./BOT-README.md) |
| What else the web terminal does | [Web SSH Terminal Guide](./webssh.md) |
| Full cloud panel capabilities | [Cloud Management Panel Guide](./cloud.md) |
| What each config field means | [Installation & Configuration](./install.md) |
