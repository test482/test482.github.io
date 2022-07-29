# Ubuntu 20.04 修改网卡配置

> 转载自: <https://blog.frytea.com/archives/435/>

最近在测试公司项目, 常常用到 Ubuntu 20.04 server 镜像. 在用到 virtualbox 的 NAT + Host Only 双网卡实现 Guest 通过 Host 的网络联网 (公司防火墙有一些规则)且 Host 可以直接访问 Guest 的场景时, 新加的网卡没有自动 UP 并且 DHCP 默认也是关闭的, 想搜索如何修改网卡设置, 找了一大圈教程都是无效, 最后发现:

> **ubuntu 从 17.10 开始，已放弃在 /etc/network/interfaces 里固定 IP 的配置，即使配置也不会生效，而是改成 netplan 方式 ，配置写在 /etc/netplan/01-netcfg.yaml 或者类似名称的 yaml 文件里**

不明白为什么还是有许多博客文章是说配置 `interfaces` ，耽误事情，因此在这里简单记录。

## 步骤一，修改配置

具体文件可能有变化，但一定在 netplan 下

`sudo vim /etc/netplan/00-installer-config.yaml`

## 步骤二，修改内容即可

```yaml
# $ cat /etc/netplan/00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp0s3: # NAT 的网卡
      dhcp4: true
    enp0s8: # Host Only 的网卡, 这个网卡的配置是刚添加的
      dhcp4: true
  version: 2
```

## 步骤三，生效配置

```bash
sudo netplan apply
```
