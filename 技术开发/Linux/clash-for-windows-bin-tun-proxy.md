# clash for windows 配置

为了开启 tun 透明代理, 需要先安装好 `nftables`, `iproute2` 依赖, 在 General 面板安装好 Service, 点击 tun mode 旁边的小齿轮, 示例配置：

```yaml
dns:
  enable: true
  enhanced-mode: fake-ip
  nameserver:
    - 119.29.29.29
    - 223.5.5.5
    - 114.114.114.114
    - 1.1.1.1
  fallback: []
  fake-ip-filter:
    - nmcheck.gnome.org
    - networkcheck.kde.org
    - ping.archlinux.org
tun:
  enable: true
  stack: gvisor
  dns-hijack:
    - 1.0.0.1:53
```

`fake-ip-filter` 添加这些域名是为了让网络连通性检测正确运行.

经过 archlinux-cn-appearance 一位群友指点, 可以直接这样写：

```yaml
fake-ip-filter:
    - '+.*'
```

这样就成了可以 remote dns 的 redir-host（
