# 上手教程

[English](./en/quickstart.md)

从零跑通一遍：装客户端 → 激活 → 上传甲骨文 API → 开一台机 → 连上去。

除了甲骨文抢机时间，其余步骤十来分钟。

---

## 1. 装客户端

找一台能开端口的机器（VPS、家里的服务器、软路由都行），执行：

```bash
mkdir rbot && cd rbot
wget -O sh_client_bot.sh https://github.com/semicons/java_oci_manage/releases/latest/download/sh_client_bot.sh && chmod +x sh_client_bot.sh && bash sh_client_bot.sh
```

脚本会自动下载适合你机器的版本，装完就在后台运行，默认端口 9527。

装在哪台机器上，API 私钥就存在哪台机器上。选一台你自己控制的。

没有公网 IP 也能用：编辑 `client_config`，把 `model=` 改成 `model=local`，之后只通过 Telegram 机器人操作，不开 Web 端。

## 2. 激活客户端

浏览器打开 `https://你的IP:9527`。证书是自签的，浏览器会拦一下，点继续访问。

页面顶部有一条红色未激活提示栏，里面是一条 `/bindclient 用户名 密码` 命令。复制，发给 [@radiance_helper_bot](https://t.me/radiance_helper_bot)，回来刷新页面即可。

换服务器重装、想让新客户端归到原账户名下，点红条里的「已有账户？」，填旧的用户名和密码。

> 用户名和密码存在 `client_config` 里，务必留个备份。Telegram 号丢了可以凭这个重新绑。

## 3. 上传甲骨文 API

先在甲骨文控制台拿到 API 参数：右上角头像 → 用户设置 → 左下资源栏 API 密钥 → 添加 API 密钥 → 下载私钥（.pem） → 添加后弹出的配置文件预览，整段复制。

预览里长这样：

```ini
[DEFAULT]
user=ocid1.user.oc1..aaaaxxxx
fingerprint=b8:33:6f:xxxx:45:43:33
tenancy=ocid1.tenancy.oc1..aaaaxxxx
region=ap-singapore-1
key_file=<path to your private keyfile>
```

细节和其他入口见 [甲骨文云 API 配置](./oracle.md)。

然后二选一上传：

**Web 端（推荐）**：顶部「设置 → 配置文件设置 → OCI」，粘贴上面那段文本，再把 `.pem` 私钥文件一起上传，`key_file` 路径会自动填好。保存即生效，不用重启。

**机器人端**：把私钥用 scp 传到客户端服务器上，记住路径，然后把配置里的 `key_file=` 改成这个路径，整段跟在 `/oci` 后面发给机器人。出于安全考虑机器人不接收私钥文件本身，只接收路径。

多个甲骨文账号就多写几段，`[DEFAULT]`、`[tokyo]`、`[osaka]` 这样区分，方括号里的名字就是 Profile 名。

## 4. 开第一台机器

Web 端：进「云管理 → 实例管理 → 创建实例」，点「快速配置」里的 `ARM A1 2C/12G` 或 `AMD 微型 1C/1G`，选好系统和公钥，提交。

机器人端：发 `/oracle`，点「开机(刷ARM)」，按提示逐项选完，二次确认后点确定开机。

参数逐项说明、抢机机制、失败排查见 [甲骨文开机教程](./boot-oracle.md)。

开机是后台任务，撞到容量不足会一直重试，结果通过 Telegram 通知，不用守着页面。

## 5. 连上服务器

开机成功的通知里带 IP 和 root 密码（密码只发这一次，机器人不留存）。

Web 端在实例卡片上点「SSH」按钮，直接跳到终端并连上，不用手填 IP。

也可以在主机列表里手动添加会话：填 IP、用户名，密码或私钥二选一。私钥在「密钥管理」里存一次，之后所有会话都能选它。

已经开好的机器可以批量导入：云管理里的「云主机同步」，把 OCI、AWS、GCP、Azure、DO、SolusVM、VirtFusion 上的实例一次性拉进会话列表。

---

## 接下来

| 想做的事 | 去哪 |
|------|------|
| 搞懂开机的每个参数 | [甲骨文开机教程](./boot-oracle.md) |
| 换 IP、自动更新 DNS、A1 降配、域名到期监控 | [常用操作教程](./howto.md) |
| 机器人有哪些命令和菜单 | [机器人操作与命令](./BOT-README.md) |
| Web SSH 终端还能干什么 | [Web SSH 终端指南](./webssh.md) |
| 云管理面板的完整能力 | [Web 云管理面板指南](./cloud.md) |
| 配置文件每个字段什么意思 | [安装与配置](./install.md) |
