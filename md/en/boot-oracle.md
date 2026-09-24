# Oracle Instance Launch Guide

[简体中文](../boot-oracle.md)

Both the Telegram bot and the web panel can launch instances. Same backend queue, same config — use whichever is at hand.

- **Web** — every parameter on one screen, shapes and images are searchable. Best for your first launch.
- **Bot** — works from your phone. Best for topping up later, batch launches, and driving several clients at once.

Either way the task runs in the background on your client server and the result arrives over Telegram.

---

## Before you start

Three prerequisites:

1. An activated client — see [Getting Started](./quickstart.md)
2. Oracle API config uploaded — see [Oracle Cloud API Configuration](./oracle.md)
3. An SSH public key or a root password. At least one. Supplying neither is rejected outright

The key does not need to exist beforehand — you can upload one from the launch form.

---

## Launching from the web

Cloud Management → Instances → Create Instance. The form has four sections: basic, system, network & security, advanced.

### The fast path

Click one of the two Quick Config buttons and most fields fill themselves in:

| Button | What it prefills |
|------|---------|
| **AMD Micro 1C/1G** | 1 OCPU / 1 GB, a non-Minimal Ubuntu image, default disk, count 1, 1–2s delay, first available public key selected |
| **ARM A1 2C/12G** | 2 OCPU / 12 GB, otherwise identical |

If your tenancy has no such shape, the button says so rather than failing silently.

Check the count and the key, hit "Start Creating", review shape / OS / script in the confirmation dialog, then "Confirm".

After submitting, the outcome is shown on the page and also sent over Telegram.

### Field by field

| Field | Notes |
|------|------|
| **Shape** | Oracle's shape name, searchable. `A1` means ARM; `E2`/`E3`/`E4` are AMD |
| **OCPU / Memory** | Adjustable on flexible shapes only. Out-of-range values are rejected immediately |
| **Count** | How many to launch. Forced to 1 when creating from a boot volume |
| **Image / Boot Volume** | Two tabs. "Image" installs a fresh OS; "Boot Volume" boots an existing detached volume with its system and data intact |
| **Operating System** | Searchable. Anything with "Minimal" in the name ships without most common utilities |
| **Disk Size (GB)** | Leave empty for the default. The free tier gives you 200 GB total to divide across instances |
| **SSH Public Key** | Pick a stored key, or click "Upload Key" and paste one (must start with `ssh-`) |
| **Set Root Password** | Generates a root password, shown once in the success notification and never stored |
| **Public IP** | Turn it off for a private-only instance |
| **Transit Encryption** | Leave at the default |
| **Delay Range (sec)** | Retry interval for the capacity loop. Give a min and a max; each wait is randomized within the range |
| **Startup Script** | Shell script executed after the instance comes up. Empty means none |

### Booting from an existing volume

For instances that were suspended, or terminated with the boot volume preserved: switch to the "Boot Volume" tab and pick a detached volume.

Two constraints. One instance at a time, and it can only launch in the availability domain that volume lives in — crossing ADs is rejected by Oracle, and the task terminates immediately with a notification.

If the instance still exists and only lost its volume, there is no need to launch anything — attach the volume back from Volume Management → Unattached Boot Volumes, provided the target instance is stopped.

### Saved launch configs

The dropdown lists launch configs saved from the bot; picking one fills the entire form. It shows up empty if you have never saved any — these can only be created bot-side.

---

## Launching from the bot

### 1. Open the keyboard

Send `/oracle`. If you run multiple accounts, switch profile under "13. Profile Management" first; for multiple clients, "14. Client Management".

### 2. Tap "1. Launch (ARM)"

Answer each prompt in turn. The fields map one-to-one to the web form: CPU type → OCPU count → memory → root password → public key → disk → count → region → script → public IP → transit encryption.

CPU type is Oracle's internal shape name; the keyboard links to a reference. Anything starting with `aarch` is ARM.

### 3. Confirm twice

After the last parameter the bot echoes a summary. **Nothing has started yet.** Check it, then press the confirm button on the keyboard to actually queue the task.

### 4. Batch launches

"17. Quick Launch" saves a parameter set for reuse:

- Several configs + several profiles → parallel launches on one client
- Several configs + several clients → parallel launches across clients

Select both and only the multi-client mode takes effect.

---

## How the capacity retry works

Oracle's free ARM is almost always sold out, so launching usually fails with `Out of host capacity` (no stock). After you submit, the client keeps trying for you until it gets one or you cancel:

- It waits within your delay range between attempts
- Each instance it gets triggers a Telegram message with the IP, root password, number of attempts, and time taken
- If it keeps failing because of quota rather than stock, you are told — retrying will not help in that case
- Running tasks are under "21. Running Tasks" in the bot and in the web task list, and can be cancelled any time

It can keep trying for a long time. That is normal.

### Force ARM

For trial accounts only — the "Force ARM (Trial)" toggle on the launch form. It measurably improves ARM creation success, at the cost of briefly using a paid feature on the account.

The UI labels it high-risk. That is not boilerplate. Do not enable it on non-trial accounts.

---

## When it will not launch

| What the notification says | Cause and fix |
|------|------|
| `Out of host capacity` | No stock. Nothing to do but let it keep trying. Trial accounts can consider Force ARM |
| Quota exceeded | You are out of quota. Send `/oracle` → "20. Check Quota" to see what is left on ARM cores, memory, and disk |
| Disk quota exceeded | Past the 200 GB total. Delete unused boot volumes or shrink the new instance's disk |
| All availability domains failed due to limits | Same as above — a quota problem, not a stock problem. Retrying forever will not help |
| Root password or public key required | You supplied neither. Go back and add one |
| Selected image not found | The image was delisted in this region. Pick another |
| Cross-AD 400, task terminated | Wrong availability domain when booting from a volume — see above |

If an account is already over the A1 free allowance (which Oracle may reclaim), the web panel's A1 Audit downscales it in bulk — see [How-To Guide](./howto.md#a1-free-tier-audit).

---

## After it launches

- The root password appears exactly once in the success notification. Save it
- Click "SSH" on the instance card to connect without retyping the IP
- Rotating IPs and binding domains: [How-To Guide](./howto.md#rotate-ip-and-auto-update-dns)
- Auto-restart on unexpected shutdown: bot menu "10. Auto-start on Failure", or Monitoring in the web settings
