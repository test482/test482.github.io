# ssh key 总是被拒绝

在确认 ssh 正确无误, 且正确添加至帐号后, 你可能需要：

```bash
ssh-add ~/.ssh/xxx.pri
```

如果提示 `Could not open a connection to your authentication agent`, 这是你的 ssh-agent 没有在运行的缘故。

需要运行下 SSH agents, 看了 arch wiki 之后, 我觉得更优雅的方式是

```bash
$ systemctl --user cat ssh-agent.service
# /home/eliot/.config/systemd/user/ssh-agent.service
[Unit]
Description=SSH key agent

[Service]
Type=simple
Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
# DISPLAY required for ssh-askpass to work
Environment=DISPLAY=:0
ExecStart=/usr/bin/ssh-agent -D -a $SSH_AUTH_SOCK

[Install]
WantedBy=default.target
```

然后在你的 shell 初始化文件（例如 `.bashrc`）中 `export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/ssh-agent.socket"`,

写好之后 `systemctl --user enable --now ssh-agent.service` 就可以使 ssh-agent 开机自启且在后台运行了.

如果你在 windows 下, 你可能需要换到新版的 openssh, 参见[链接](技术开发/Git/use-windows-may-need-install-latest-openssh.md)
