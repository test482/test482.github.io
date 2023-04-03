# 开发机-工位-个人VPS 之间的转发

一般情况下, 工位能访问位于内网中的, 无法(或有限)访问互联网的开发机, 而工位电脑无公网 IP 地址. 工位电脑连接互联网正常.

> 假设开发机 IP 地址为 10.0.20.1, 个人 VPS IP 地址为 177.77.77.77.
>

我们在工位电脑上运行 `ssh -R 10022:10.0.20.1:22 user@177.77.77.77 -NT` . 这样, 当我们在个人 VPS 上访问 localhost:10022 的时候就等于访问开发机的 22 端口.

在我们自己的笔记本上时, 我们可以 `ssh -J user@177.77.77.77 dev@localhost -p 10022` 这样借助个人 VPS 作为跳板, 去访问开发机了.

```bash
$ cat .ssh/config
Host vps
    HostName 177.77.77.77
    User user

Host work-dev
    HostName localhost
    User dev
    Port 10022
    proxyjump vps
```

在 ssh config 里写好如上所示, 在自己笔记本上就可以直接 ssh work-dev 连接到内网开发机了.
