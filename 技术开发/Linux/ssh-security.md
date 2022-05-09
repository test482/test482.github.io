# ssh-security

每次有了新的 vps, ~~第一件事就是换 Arch~~ 第一件事就是做简单的安全防护:

- 上传 ssh 密钥公钥到 `~/.ssh/authorized_keys`

    ```bash
    ssh-copy-id -p $PORT $USERNAME@$HOSTIPADDR
    ```

    要么直接 `vim ~/.ssh/authorized_keys` , 复制粘贴进去

- 编辑 ssh 配置

    `sudo vim /etc/ssh/sshd_config`

  - `Port 22`

    > 更改 ssh 端口为非默认的 22 端口

  - `LogLevel VERBOSE`

    > 更改日志等级，以便配合 fail2ban

- `PermitRootLogin no`

    > 禁止 root 帐户登录

- `PubkeyAuthentication yes`

    > 允许使用密钥登录

- `PubkeyAcceptedKeyTypes=+ssh-rsa`

    > for this, see [https://www.openssh.com/txt/release-8.7](https://www.openssh.com/txt/release-8.7)

- `PasswordAuthentication no`

    > 禁止密码登录
    > 在设置这一项之前，强烈建议你先尝试下密钥登录是否能正常工作, 以防把自己锁在外面

- `X11Forwarding yes`

    > 开启 x11 转发(如果你需要的话).
    > x11 forward 在客户端和服务端都支持的情况可以转发图形应用

- 安装、配置、启用 fail2ban、endlessh

    [fail2ban](./fail2ban.md) [endlessh](./endlessh.md)

完成这几点, 基本就能杜绝那些烦人的脚本小子了. 天天扫端口, 你扫你妹呀! 污染我日志.
