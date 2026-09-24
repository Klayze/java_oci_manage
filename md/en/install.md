# Installation & Configuration

[简体中文](../install.md)

---

## 1. One-Click Install / Update

```bash
wget -O sh_client_bot.sh https://github.com/semicons/java_oci_manage/releases/latest/download/sh_client_bot.sh && chmod +x sh_client_bot.sh && bash sh_client_bot.sh
```

> Recommended: create a directory first — `mkdir rbot && cd rbot`

The install script works under BusyBox, so it runs on OpenWrt routers as well.

ARM64 builds target ARMv8.0, so older ARM chips such as the RK3399 and Cortex-A53 / A72 run them too.

---

## 2. Activate the Client

On first startup, the system auto-generates user credentials and writes them to the `client_config` file. The client is inactive until activated — a red banner will appear at the top of the page.

### Option 1: Activate via Telegram Bot (Recommended)

1. Open the web page and copy the `/bindclient` command from the banner
2. Send it to the [Telegram Bot](https://t.me/radiance_helper_bot)
3. Refresh the page — activation complete

### Option 2: Bind an Existing Account

If you already have credentials from another client:

1. Click "Already have an account?" in the banner
2. Enter your existing username and password
3. Binding completes and you're logged in automatically

> Keep these credentials safe. If your Telegram account gets banned, you can use them to rebind.

---

## 3. Configuration Parameters

After activation, edit the `client_config` file to add cloud platform API parameters.

### Oracle Cloud (OCI) Configuration

Place API configuration between `oci=begin` and `oci=end`. Multiple profiles are supported.

```ini
oci=begin

[DEFAULT]
user=ocid1.user.oc1..aaaaaaaaxxxxgwlg3xuzwgsaazxtzbozqq
fingerprint=b8:33:6f:xxxx:45:43:33
tenancy=ocid1.tenancy.oc1..aaaaaaaaxxx7x7h4ya
region=ap-singapore-1
key_file=/root/rbot/xxx.pem

[tokyo]
user=ocid1.user.oc1..aaaaaaaaxxxxgwlg3xuzwgsaazxtzbozqq
fingerprint=b8:33:6f:xxxx:45:43:33
tenancy=ocid1.tenancy.oc1..aaaaaaaaxxx7x7h4ya
region=ap-tokyo-1
key_file=/root/rbot/xxx.pem

oci=end
```

> `key_file` is the path to your private key file on the server. The private key is generated when adding an API key in the Oracle console. See → [Oracle Cloud API Setup](./oracle.md)

### Azure Configuration

Place configuration between `azure=begin` and `azure=end`. Multiple profiles are supported.

```ini
azure=begin

[az001]
appId=551xxxx7-xxxx-xxxx-xxxx-b9xxxx60cc65
password=T618Q~.LIy_xxxxx~jm~xxxxxx
tenant=xxxx3713-xxxx-4cb5-xxxx-3001060xxxxx

azure=end
```

> You can also upload the raw JSON format via the bot's `/oci` command. See → [Azure API Setup](./azure.md)

### AWS Configuration

Place AWS credentials between `aws=begin` and `aws=end`. Multiple profiles are supported.

```ini
aws=begin

[DEFAULT]
access_key_id=AKIAxxxxxxxxxxxxxxxx
secret_access_key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
region=us-east-1

[tokyo]
access_key_id=AKIAxxxxxxxxxxxxxxxx
secret_access_key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
region=ap-northeast-1

aws=end
```

> `region` is optional, defaults to `us-east-1`. Field names support both camelCase (`accessKeyId`) and snake_case (`access_key_id`).

### GCP Configuration

Upload a GCP Service Account JSON key file via the web interface — credentials are auto-extracted, no manual editing needed. Multiple profiles are supported.

> Generate the Service Account JSON key at: GCP Console → IAM → Service Accounts → Keys.

### DigitalOcean Configuration

Place API Token between `do=begin` and `do=end`. Multiple profiles are supported.

```ini
do=begin

[DEFAULT]
token=dop_v1_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

do=end
```

> Generate the token at: DigitalOcean Console → API → Tokens. Can also be uploaded via the web interface "Settings".

### SSH Connection Configuration

Maintain SSH connection info between `ssh=begin` and `ssh=end`. Format: `ssh_IP=username:password_or_key_path`.

```ini
ssh=begin

ssh_192.168.1.10=root:MyPassword123
ssh_129.1.100.55=ubuntu:/root/.ssh/oci_key

ssh=end
```

> This configuration is for the Telegram Bot's remote command execution. Web SSH terminal connections are managed through the browser interface and don't need to be configured here.

### SolusVM Configuration

Maintain SolusVM panel configuration between `solusvm=begin` and `solusvm=end`.

```ini
solusvm=begin

[racknerd-1]
api=https://nerdvm.racknerd.com/api/client/command.php
key=your_api_key
hash=your_api_hash

solusvm=end
```

### VirtFusion Configuration

Maintain VirtFusion vendor configuration between `virtfusion=begin` and `virtfusion=end`.

```ini
virtfusion=begin

[GreenCloud DE EPYC]
host=cp.green.cloud
token=your_virtfusion_api_token
preset=greencloud

virtfusion=end
```

> `host` should be the panel domain only. Do not include `https://`, ports, or paths. Generate the `token` at `https://cp.<your-panel-host>/account/api`. `preset` is optional; common values include `greencloud`, `extravm`, and `custom`. If omitted, it falls back to `custom`.

### Cloudflare Configuration (Optional)

Two authentication methods; pick one.

**API Token (recommended)**

```ini
cf_api_token=your_API_Token
```

> Create it in the Cloudflare dashboard with Zone → DNS → Edit and Zone → Zone → Read. Its scope can be limited to specific zones.

**Global API Key**

```ini
cf_email=your_cloudflare_email
cf_account_key=your_Global_API_Key
```

> Get it at: My Profile → API Tokens → API Keys → Global API Key. This key carries full account permissions, cannot be scoped down, and there is exactly one per account. Prefer the token.

With both configured, the token wins. DNS record management, auto DNS updates on IP change, ACME certificate issuance, domain monitoring imports, and email domain verification all share these credentials.

### Network Configuration (Optional)

```ini
# Local address (leave empty for auto-detection, uses default port 9527)
local_address=https://xxx.xx:9527

# URL name (defaults to address, can be changed in the bot)
local_url_name=

# Startup mode ("local" for no-public-IP mode; leave empty for port mode)
model=
```

---

## 4. Common Commands

| Command | Description |
|---------|-------------|
| `bash sh_client_bot.sh` | Start / Restart |
| `bash sh_client_bot.sh 8888` | Start with custom port |
| `bash sh_client_bot.sh log` | View logs (Ctrl+C to exit) |
| `pgrep -f r_client \| xargs -r kill -9` | Stop |
| `bash sh_client_bot.sh uninstall` | Uninstall |

Upgrading, restarting, and reading logs can also be done from the web UI — see [Cloud Management Panel Guide — Client Maintenance](./cloud.md#client-maintenance).

---

## 5. Web SSH Terminal Access

After startup, access the Web SSH terminal via browser:

```
https://YOUR_IP:9527
```

- Log in with `username` / `password`, or use Telegram verification code
- Default port is `9527`, configurable via startup parameters
- Ensure the port is open — use [Port Test Tool](https://port.ping.pe) to check
- Local mode (`model=local`) doesn't require open ports — operate through Telegram Bot only
- You can also upload AWS / GCP / DigitalOcean / SolusVM / VirtFusion configs directly from `Settings -> Config Files`
