# ThinkPad T14 AMD Gen 1

## modprobe.d

```bash
$ tree /etc/modprobe.d/
/etc/modprobe.d/
├── disable_beep.conf # 关闭蜂鸣器
├── iwlwifi.conf # AX200 网卡设置项
├── sp5100_tco.conf # 关闭 watchdog
```

```bash
$ cat disable_beep.conf 
blacklist pcspkr
```

```bash
$ cat iwlwifi.conf 
options iwlwifi power_save=0
#https://wiki.archlinux.org/title/Lenovo_ThinkPad_T14_(AMD)_Gen_1#Wireless
options iwlwifi 11n_disable=8
```

```bash
$ cat sp5100_tco.conf 
# disable watchdog
blacklist sp5100_tco
```

## boot parameters

```bash
$ cat /etc/default/grub  | rg GRUB_CMDLINE_LINUX_DEFAULT
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet nowatchdog resume=UUID=xxxx-xxxx"
```
