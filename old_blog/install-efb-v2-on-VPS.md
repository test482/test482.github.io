---
title: 在 Telegram 上实现微信收发，EHForwarderBot 搭建记录
tags: [Telegram,Python,Bot]
---

EH Forwarder Bot（简称 EFB）是一个可扩展的聊天平台隧道框架，基于 Python 3。同时 EFB 配备了详尽的文档，欢迎有兴趣的朋友们开发自己的主端或从端，来支持更多的平台。EFB [在 GitHub 中开放了源代码](https://github.com/blueset/ehforwarderbot)，并且[在 Read The Docs 平台上发布的开发文档](https://ehforwarderbot.readthedocs.io/)（英文，`en-US`）。（~~抄自奶冰大佬的博客~~

# 使用前须知

自 2016 年中旬以来，陆续有用户报告其微信网页版登录被腾讯封禁。 表现为用任何方式登录微信网页版提示「当前登录环境异常。为了你的账号安全， 暂时不能登录 Web 微信。你可以通过手机客户端或 Windows 微信登录」 或类似的提示。大部分用户会在封禁后三个月内解封，不同用户的解封耗时不同。 该封禁不影响其他客户端的登录。目前封禁的原因尚不明确。

如果你对网页版登录有要求的话，请慎用此 Channel。详细的相关信息请参见 项目 Wiki 。

# 搭建过程

> 以 DgitalOcean Ubuntu 18.10 x86_64 Server 为例

## 申请 Telegram bot

**以下这段信息引用自 efb 作者博客:[1a23](https://blog.1a23.com/2017/01/09/EFB-How-to-Send-and-Receive-Messages-from-WeChat-on-Telegram-zh-CN/)**

> Telegram Bot 是 EFB（Telegram 主端）的出口，也是呈献给用户的渠道。我们在这里使用了 Telegram 官方的 Bot API，以最大化利用 Telegram Bot 所提供的各种便利功能。
>
> 要创建一个新的 Bot，要先向 @BotFather 发起会话。发送指令 /newbot 以启动向导。期间，你需要指定这个 Bot 的名称与用户名（用户名必须以 bot 结尾）。
>
> 完毕之后 @BotFather会提供给你一个密钥（Token），妥善保存这个密钥。**请注意，为保护您的隐私及信息安全， 请不要向任何人提供你的 Bot 用户名及密钥，这可能导致聊天信息泄露等各种风险。**

接下来还要对刚刚启用的 Bot 进行进一步的配置：允许 Bot 读取非指令信息、允许将 Bot 添加进群组、以及提供指令列表。

发送 `/setprivacy` 到 @BotFather，选择刚刚创建好的 Bot 用户名，然后选择 “Disable”.

发送 `/setjoingroups` 到 @BotFather，选择刚刚创建好的 Bot 用户名，然后选择 “Enable”.

发送 `/setcommands` 到 @BotFather，选择刚刚创建好的 Bot 用户名，然后发送[如下内容](https://github.com/blueset/efb-telegram-master#usage)：

```text
help - Show commands list.
link - Link a remote chat to a group.
unlink_all - Unlink all remote chats from a group.
info - Display information of the current Telegram chat.
chat - Generate a chat head.
extra - Access additional features from Slave Channels.
update_info - Update the group name and profile picture.
```

## 获取 Telegram ID

在 Telegram 上和 [@get_id_bot](https://t.me/get_id_bot) 点击 /start 即可获得你的 Telegram ID，一串数字（Chat ID）。

## 安装 EFB 主从端

先滚一滚系统：
`apt update && apt upgrade -y`

安装所需依赖和工具：
`apt install python3 python3-pip python3-pil python3-setuptools python3-numpy python3-yaml python3-requests ffmpeg libmagic-dev libwebp-dev nano screen -y`

pip 安装 efb2 主体和模块：
`pip3 install ehforwarderbot efb-telegram-master efb-wechat-slave`

## 创建配置文件

### 配置文件主目录

`mkdir -p ~/.ehforwarderbot/profiles/default/`

`nano ~/.ehforwarderbot/profiles/default/config.yaml` 写入以下内容:

```text
master_channel: blueset.telegram
slave_channels:
- blueset.wechat
```

### Telegram 主端配置文件

`mkdir -p ~/.ehforwarderbot/profiles/default/blueset.telegram`

`nano ~/.ehforwarderbot/profiles/default/blueset.telegram/config.yaml` 写入以下内容：

```text
token: "12345678:1a2b3c4d5e6g7h8i9j"
# " "内替换为你在 @BotFather 处获得的 bot token
admins:
- 123456789
# - 后面的数字替换为你在 bot 处获得的 chat id
```

### EFB 微信从端配置文件

参见 <https://github.com/blueset/efb-wechat-slave#可选的配置文件>

## 试运行

在终端输入 `ehforwarderbot` ，应该会看到类似下图的提示：

~~此处应有图~~（好吧，我懒得再搭建一次

扫码登录即可。

> 如果你登录成功了，但是收发信息和控制 bot 有异常，那是因为目前 EFB 需要指定 python-telegram-bot\<12.0.0
>
> 使用 `pip3 install python-telegram-bot==11.1.0` 来指定版本安装

## 使用 systemd 管理

如果不想每次启动 efb 都手动运行的话，建议设置一个自启动脚本运行 efb2，如果不是比较了解 systemd 建议使用 screen 启动 efb 。

使用 systemd 启动 efb 的话就不需要再使用 screen 了。如果已经使用 screen 运行了 efb2 请先退出（按 `CTRL + c` 键结束进程），然后再使用 systemd 运行 efb2 。

`nano /etc/systemd/system/efb2.service` 创建脚本，并写入以下内容：

```text
[Unit]
Description=efb2 Service
After=network.target
Wants=network.target

[Service]
Type=simple
PIDFile=/var/run/efb2.pid
ExecStart=/usr/local/bin/ehforwarderbot
Restart=on-failure
User=root
# 用户修改为你创建配置文件的用户，这里我用的 root 用户
Group=root
# 用户组更改为 你创建配置文件的用户所在组，一般与用户同名 这里我用的 root 组

[Install]
WantedBy=multi-user.target
```

关于 systemd 的解释，可以参见 [Arch Wiki 相关内容](https://wiki.archlinux.org/index.php/Systemd)

* * *

启动 efb2：`systemctl start efb2.service`

打印日志文件：`journalctl -f -u efb2.service`

日志文件会给出一个登录二维码网页链接，类似于这样 `https://login.weixin.qq.com/qrcode/1a2b3c4d==` ，链接打开后，扫描网页上的二维码，然后确认网页登录。

登录成功后按 `CTRL + c` 键退出日志打印

然后设置 efb2 的开机自启动：`systemctl enable efb2.service`

到这里 efb2 的自启动就设置完成了，下次服务器重启就不需要再手动运行 efb 2了。

# 其他

因为我本身是微信的极轻度用户，一周也没有几条消息，所以 efb2 的配置过程至此对我来说算是完成了，其余的绑定会话之类的操作对我意义并不大，这里就不叙述了。

另外，请注意文章的更新时间，由于 EFB 项目目前更新频繁，~~这篇文章仅保证在发布时是可以正常搭建的（~~

# 一些我参考过的链接

\[[EFB How-to: Send and Receive Messages from WeChat on Telegram (zh-CN)](https://blog.1a23.com/2017/01/09/EFB-How-to-Send-and-Receive-Messages-from-WeChat-on-Telegram-zh-CN/)]

\[[安装并使用 EFB：在 Telegram 收发 QQ 消息 - Milkice's IceBox](https://milkice.me/2018/09/17/efb-how-to-send-and-receive-messages-from-qq-on-telegram/)]

\[[EH Forwarder Bot 2 安装 - 葉子](https://niconiconi.fun/2018/03/17/install-efb-v2/)]

\[GitHub Repositories [EFB Telegram 主端](https://github.com/blueset/efb-telegram-master) 、 [EFB 微信从端](https://github.com/blueset/efb-wechat-slave)]

非常感谢上面大佬的博文帮助😋