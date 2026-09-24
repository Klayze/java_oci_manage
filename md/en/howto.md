# How-To Guide

[简体中文](../howto.md)

Step-by-step for common tasks. The full feature list is in [Implemented Features](./function.md).

- [Rotate IP and auto-update DNS](#rotate-ip-and-auto-update-dns)
- [Rotate automatically when an IP goes dark](#rotate-automatically-when-an-ip-goes-dark)
- [Auto-shutdown on traffic overage](#auto-shutdown-on-traffic-overage)
- [Bring stopped instances back up](#bring-stopped-instances-back-up)
- [A1 free tier audit](#a1-free-tier-audit)
- [Domain and certificate expiry monitoring](#domain-and-certificate-expiry-monitoring)
- [Give each account its own outbound IP](#give-each-account-its-own-outbound-ip)
- [Lost boot volume or terminated instance](#lost-boot-volume-or-terminated-instance)
- [Rescue an instance you cannot SSH into](#rescue-an-instance-you-cannot-ssh-into)
- [Run one command across many hosts](#run-one-command-across-many-hosts)
- [Import existing cloud instances as sessions](#import-existing-cloud-instances-as-sessions)
- [Upgrade the client and read its logs](#upgrade-the-client-and-read-its-logs)

---

## Rotate IP and auto-update DNS

The usual move once an IP is blocked: change the IP and repoint the domain at the same time, which works like DDNS.

**First**, add Cloudflare credentials under Settings → Config File Settings. Either:

| Method | Fields | Where to get it |
|------|--------|---------|
| API Token (recommended) | `cf_api_token` | Create a token in the Cloudflare dashboard with Zone→DNS→Edit and Zone→Zone→Read |
| Global API Key | `cf_email` + `cf_account_key` | My Profile → API Tokens → API Keys → Global API Key |

Prefer the token: it can only edit DNS, while the Global API Key can do anything on your Cloudflare account.

**Bot** — `/oracle` → "2. IP Management":

- Change the IP
- Point the current IP at a domain on Cloudflare
- Change the IP and update the domains pointing at it in one step
- Unbind — detach every domain from the current IP
- Delete the current IP

**Web** — Cloud → instance card → Network / IP: change IPv4, change IPv6, add IPv4, add IPv6, attach a reserved IP. Domain records are edited under the Cloudflare tab.

---

## Rotate automatically when an IP goes dark

`/oracle` → "16. Auto IP Rotation Monitor". Set it and stop watching.

Settings:

- The IP to watch
- Whether to check through a proxy
- Netflix non-original content check
- IP range — keep changing until the new IP falls inside it

When the IP stops responding it is changed automatically and your domains are updated to match.

---

## Auto-shutdown on traffic overage

Oracle's free traffic is 10240 GB (10 TB) a month; beyond that you pay by usage. Someone hammering your bandwidth overnight can cost you tens of dollars.

Cloud → "Cloud Monitoring" → "+ Add Traffic Guard":

1. Pick the account
2. Set the limit. **Use 9000, not 10240** — Oracle's traffic figures lag by hours, so a limit at the ceiling triggers too late
3. Choose "Notify only" or "Auto shutdown"

With auto shutdown:

- It shuts down the moment the limit is hit, without asking, and it stops that account's machines in **every region**
- Start them again while still over the limit and they are stopped again within the hour
- Leave them off too long and Oracle may take back hard-to-get A1 capacity, so you would have to compete for it again

---

## Bring stopped instances back up

Cloud → "Cloud Monitoring" → Uptime Guard, two toggles:

| Toggle | What it does |
|------|------|
| Stop notifications | Telegram message when a machine stops unexpectedly |
| Auto-start | Start it again |

Accounts shut down by traffic guard stay off for the rest of the month, so they do not come straight back and keep burning bandwidth.

The bot's "9. Instance Status Monitoring" and "10. Auto-start on Failure" are the same settings.

---

## A1 free tier audit

Oracle's ARM free allowance is per account, and accounts over it risk being reclaimed. Cloud → "A1 Audit" checks and downscales in bulk.

1. **Audit** — shows ARM usage per account and flags each as over-allowance, compliant, or query failed
2. **Downscale** — per account (split evenly across that account's machines), batch (tick several accounts and submit together), or per machine
3. The default target is 2 OCPU / 12 GB and can be changed

Keep in mind:

- It only ever scales down and **never deletes machines on its own**
- Out-of-capacity errors are retried until it succeeds; the result arrives over Telegram and it can be cancelled from the task list
- Running machines reboot during the change
- The target OCPU count cannot be lower than the number of machines, or it will not split evenly — downscale one by one or delete a few first
- Machines can only be deleted one at a time with confirmation

Free users can audit the current account only; Lightning users can handle every account at once.

---

## Domain and certificate expiry monitoring

Cloud → "Domain Monitoring". Checks domain registration and SSL certificate expiry every day and warns you over Telegram before they run out.

Add domains by hand, or click "Import from Cloudflare" to pull in all your domains at once.

You get one reminder each at 30, 14, 7, and 1 day before expiry, and one on the day it expires.

Keep in mind:

- Enter the **main domain** (`example.com`), not a subdomain. Subdomains have no registration date, so domain expiry shows "unknown" — certificate expiry still works
- Internationalized domains can be searched directly
- Adding, importing, and turning on reminders need Lightning; disabling, deleting, viewing, and "Check now" do not

---

## Give each account its own outbound IP

When several Oracle accounts sit on the same client, they all reach Oracle from the same IP. To send some accounts through their own proxy, set it per account.

**Web** — Settings → Config File Settings → open the account → "API Outbound Proxy" at the bottom. Enter the proxy address, plus username and password if it needs them, and click "Save Proxy".

To turn it off, click "Remove Proxy". Clearing the fields and saving does not work.

The account list on the overview page has an "Outbound Proxy" column so you can see which accounts have one.

**Bot** — `/oproxy`.

---

## Lost boot volume or terminated instance

Boot volumes and instances are separate. If the volume is still there, so is your data.

Cloud → Volumes. Detached boot volumes are in the "Unattached Boot Volumes" panel; if an instance has no boot volume, clicking "Boot Volume" takes you there.

| Situation | What to do |
|------|------|
| The instance exists, its volume was detached | Click "Attach" in the unattached panel and pick the instance. Stop the instance first |
| The instance was deleted, the volume kept | Launch a new one from it — see [Oracle Instance Launch Guide](./boot-oracle.md#booting-from-an-existing-volume) |

Detaching a boot volume also requires the instance to be stopped.

---

## Rescue an instance you cannot SSH into

SSH refuses, you firewalled yourself out, or the system will not boot — use the serial console.

Cloud → instance → Serial Console. A terminal opens in the browser, as if you had plugged in a screen and keyboard.

If the system is too broken to start, the built-in Netboot.xyz gets you into a rescue system, pausing for you before anything risky.

---

## Run one command across many hosts

Web SSH terminal → Batch Commands. Tick the hosts, type the command, send it once.

Results come back per host, and you can run the next command on the same hosts without reselecting them.

---

## Import existing cloud instances as sessions

No need to type IPs one by one. Host panel → Cloud Host Sync, covering Oracle, AWS, GCP, Azure, DigitalOcean, SolusVM, and VirtFusion.

Only the IP and name come across; set the username and key once yourself. Store the key under SSH Key Management and every connection can use it.

For a one-off connection, the "SSH" button on any instance card connects directly.

---

## Upgrade the client and read its logs

"Client Upgrade" and "Client Logs" in the settings menu — no need to log into the server, and no Lightning needed. The bot's "32. Upgrade Client" and "34. Latest Logs" do the same.

**Upgrade**

The page shows the current and latest version.

- **Upgrade Now** — download the new version and restart
- **Force Upgrade** — reinstall regardless of version. Use it when the version check fails
- **Restart Service** — restart without upgrading

Once per 5 minutes. Terminals disconnect during an upgrade and are usually back within 1-3 minutes.

**Logs**

Show 100 / 300 / 1000 lines, filter by keyword, turn on auto-refresh, copy it all. When something breaks, copy a chunk and send it to support.
