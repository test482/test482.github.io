# fail2ban

[https://wiki.archlinux.org/title/Fail2ban](https://wiki.archlinux.org/title/Fail2ban)

[https://github.com/fail2ban/fail2ban](https://github.com/fail2ban/fail2ban)

仅 ban 指定端口

```bash
$ cat /etc/fail2ban/jail.d/sshd.local
[sshd]
enabled   = true
port      = 2222
filter    = sshd
banaction = iptables
backend   = systemd
maxretry  = 5
findtime  = 1d
bantime   = -1
ignoreip  = 127.0.0.1/8
```

> fail2ban 对于服务端口在未指定状态下是从 `/etc/services` 中获得的, 如果你未更改过 sshd 服务的端口, 可以省略 `port = 2222` 这一行.
>

`cat /etc/fail2ban/action.d/iptables.conf` 可以看到里面写了 `before = iptables-common.conf` ,

而在 `iptables-common.conf` 中可以看到它有 `after = iptables-blocktype.local iptables-common.local` , 所以可以将个人配置写到 `iptables-common.local` 中.

在`iptables-common.conf`中可以看到默认行为是 `blocktype = REJECT --reject-with icmp-port-unreachable`

如果需要 Drop

```bash
$ cat /etc/fail2ban/action.d/iptables-common.local
[Init]
blocktype = DROP

[Init?family=inet6]
blocktype = DROP
```

ban 掉 IP 对所有端口的访问

```bash
$ cat /etc/fail2ban/jail.d/sshd.local
[sshd]
enabled   = true
filter    = sshd
banaction = iptables-allports
backend   = systemd
maxretry  = 5
findtime  = 1d
bantime   = -1
ignoreip  = 127.0.0.1/8
```

> 在这个配置文件不需要指定端口是因为 fail2ban 会从 sshd 的日志中判断拉黑一个 ip 对所有端口的访问, 并不介意你是否更改了 sshd 的端口.
>

在 `iptables-allports` 中也能看到它 `before = iptables-common.conf`
