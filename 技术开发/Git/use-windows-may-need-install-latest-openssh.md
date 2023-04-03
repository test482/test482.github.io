# Windows 可能需要安装新版 openssh

* 在 GitHub 中设置了 ssh key,  `ssh -T git@github.com` 提示成功, 却始终不能 push 到远端?

    可以尝试安装最新的 [openssh](https://github.com/PowerShell/Win32-OpenSSH/releases).

    将其添加进系统 PATH 最前面, 以覆盖 Windows 的 ssh.

    在 TortoiseGit "网络” → “SSH 客户端” 处选择 openssh 目录下的 ssh.exe 以覆盖 TortoiseGit 的 SSH.
