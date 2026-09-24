# 安装与配置

[English](./en/install.md)

---

## 1. 一键安装 / 更新

```bash
wget -O sh_client_bot.sh https://github.com/semicons/java_oci_manage/releases/latest/download/sh_client_bot.sh && chmod +x sh_client_bot.sh && bash sh_client_bot.sh
```

> 建议先创建目录：`mkdir rbot && cd rbot`

---

## 2. 激活客户端

首次启动时，系统会自动生成用户凭据并写入 `client_config` 文件。此时客户端处于未激活状态，页面顶部会显示红色提示栏。

### 方式一：通过 Telegram 机器人激活

1. 打开 Web 页面，复制提示栏中的 `/bindclient` 命令
2. 发送给 [Telegram 机器人](https://t.me/radiance_helper_bot)
3. 刷新页面，激活完成

### 方式二：绑定已有账户

如果你已有其他客户端的账户凭据：

可以任选一种方式：

- **在 Web 页面操作**：
  1. 打开客户端 Web 页面，看页面最上方的红色未激活提示栏
  2. 在这条红色提示栏里点击「已有账户？」按钮（不是在 Telegram 里点击）
  3. 在弹出的输入框里填写已有的用户名和密码
  4. 绑定成功后自动登录

- **直接修改配置文件**：
  1. 打开客户端目录下的 `client_config`
  2. 找到 `username=` 和 `password=`
  3. 把等号后面改成已有账户的用户名和密码，例如：

     ```ini
     username=你的已有用户名
     password=你的已有密码
     ```

  4. 保存后重启客户端

> 请妥善保存凭据。如果 Telegram 账号被封，可以凭此信息重新绑定。

---

## 3. 配置参数

激活后，编辑 `client_config` 配置文件添加云平台 API 参数。

### Oracle Cloud (OCI) 配置

在 `oci=begin` 和 `oci=end` 之间放入 API 配置信息，支持多个 Profile。

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

> `key_file` 是服务器上的私钥文件路径。私钥在甲骨文控制台添加 API 密钥时生成下载。详见 → [甲骨文云 API 配置](./oracle.md)

### Azure 配置

在 `azure=begin` 和 `azure=end` 之间放入 API 配置信息，支持多个 Profile。

```ini
azure=begin

[az001]
appId=551xxxx7-xxxx-xxxx-xxxx-b9xxxx60cc65
password=T618Q~.LIy_xxxxx~jm~xxxxxx
tenant=xxxx3713-xxxx-4cb5-xxxx-3001060xxxxx

azure=end
```

> 也可通过机器人 `/oci` 命令上传原始 JSON 格式。详见 → [Azure API 配置](./azure.md)

### AWS 配置

在 `aws=begin` 和 `aws=end` 之间放入 AWS 凭据配置，支持多个 Profile。EC2 和 Lightsail 轻量实例共用这套 AWS Profile。

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

> `region` 为可选参数，默认为 `us-east-1`。字段名支持驼峰格式（`accessKeyId`）和下划线格式（`access_key_id`）。如需管理 Lightsail，请确保该 AWS 凭据拥有 Lightsail 实例、静态 IP、端口、防火墙和监控指标相关权限。

### GCP 配置

通过 Web 界面上传 GCP Service Account JSON 密钥文件即可自动配置，无需手动编辑。

上传后系统会自动提取 `project_id`、`client_email`、`private_key` 并写入配置文件，支持多个 Profile。

> Service Account JSON 密钥在 GCP 控制台 → IAM → 服务账号 → 密钥 中生成下载。

### DigitalOcean 配置

在 `do=begin` 和 `do=end` 之间放入 API Token 配置，支持多个 Profile。

```ini
do=begin

[DEFAULT]
token=dop_v1_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

do=end
```

> Token 在 DigitalOcean 控制台 → API → Tokens 中生成。也可通过 Web 界面「设置」上传。

### SSH 连接配置

在 `ssh=begin` 和 `ssh=end` 之间维护 SSH 连接信息。格式：`ssh_IP=用户名:密码或密钥路径`

```ini
ssh=begin

ssh_192.168.1.10=root:MyPassword123
ssh_129.1.100.55=ubuntu:/root/.ssh/oci_key

ssh=end
```

> 此配置用于 Telegram 机器人的远程命令执行。Web SSH 终端的连接在浏览器界面中管理，无需在此配置。

### SolusVM 配置

在 `solusvm=begin` 和 `solusvm=end` 之间维护 SolusVM 面板配置。

```ini
solusvm=begin

[racknerd-1]
api=https://nerdvm.racknerd.com/api/client/command.php
key=你的API_Key
hash=你的API_Hash

solusvm=end
```

### VirtFusion 配置

在 `virtfusion=begin` 和 `virtfusion=end` 之间维护 VirtFusion 厂商配置。

```ini
virtfusion=begin

[GreenCloud DE EPYC]
host=cp.green.cloud
token=你的VirtFusion_API_Token
preset=greencloud

virtfusion=end
```

> `host` 只填写控制面板域名，不要带 `https://`、端口或路径。`token` 可在 `https://cp.<你的面板域名>/account/api` 生成。`preset` 可选，常见值有 `greencloud`、`extravm`、`custom`；不填时默认按 `custom` 处理。

### Cloudflare 配置（可选）

两种认证方式，任选其一。

**API Token（推荐）**

```ini
cf_api_token=你的API_Token
```

> 在 Cloudflare 后台创建，权限勾选 Zone → DNS → Edit 和 Zone → Zone → Read 即可。作用范围可以限定到指定域名。

**Global API Key**

```ini
cf_email=你的Cloudflare邮箱
cf_account_key=你的Global_API_Key
```

> 获取路径：我的个人资料 → API 令牌 → API 密钥 → Global API Key。这把钥匙拥有账户全部权限且不可收敛，每个账户只有一把，能用 Token 就别用它。

两者都填时以 Token 为准。DNS 记录管理、换 IP 自动更新 DNS、ACME 证书签发、域名监控导入、邮件域校验共用这套凭据。

### 网络配置（可选）

```ini
# 本机地址（留空则自动获取，使用默认端口 9527）
local_address=https://xxx.xx:9527

# URL 名称（默认为 address，可在 bot 上修改）
local_url_name=

# 启动模式（填 local 为无公网 IP 模式；留空或填其他为端口模式）
model=
```

---

## 4. 常用命令

| 命令 | 说明 |
|------|------|
| `bash sh_client_bot.sh` | 启动 / 重启（守护进程） |
| `bash sh_client_bot.sh 8888` | 指定端口启动 |
| `bash sh_client_bot.sh status` | 查看运行状态 |
| `bash sh_client_bot.sh log` | 查看日志（Ctrl+C 退出） |
| `bash sh_client_bot.sh stop` | 停止客户端 |
| `bash sh_client_bot.sh restart` | 重启客户端 |
| `bash sh_client_bot.sh upgrade` | 升级到最新版本 |
| `bash sh_client_bot.sh uninstall` | 卸载 |

升级、重启和查看日志也可以在 Web 界面完成，见 [Web 云管理面板指南 — 客户端维护](./cloud.md#客户端维护)。

---

## 5. 访问 Web 界面

启动后通过浏览器访问：

```
https://你的IP:9527
```

- 使用 `username` / `password` 登录，或使用 Telegram 验证码登录
- 默认端口 `9527`，可通过启动参数修改
- 确保端口已开放 — 使用 [端口测试工具](https://port.ping.pe) 检查
- 本地模式（`model=local`）无需开端口，仅通过 Telegram 机器人操作
- 在顶部「设置 → 配置文件」中也可直接上传 AWS / GCP / DigitalOcean / SolusVM / VirtFusion 等配置

---

## 6. 支持的架构

| 架构 | 下载包 |
|------|--------|
| Linux x86_64（AVX2） | `gz_client_bot_x86.tar.gz` |
| Linux x86_64（兼容） | `gz_client_bot_x86_compatible.tar.gz` |
| Linux ARM64 | `gz_client_bot_aarch.tar.gz` |
| macOS ARM64（Apple Silicon） | `gz_client_bot_mac_aarch.tar.gz` |

> 启动脚本会自动检测架构并下载对应版本，无需手动选择。

安装脚本兼容 BusyBox 环境，可以直接装在 OpenWrt 路由器上。

ARM64 包按 ARMv8.0 编译，RK3399、Cortex-A53 / A72 这类老一些的 ARM 芯片也能跑。
