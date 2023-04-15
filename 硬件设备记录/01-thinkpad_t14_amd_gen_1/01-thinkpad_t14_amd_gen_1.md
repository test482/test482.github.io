# ThinkPad T14 AMD Gen 1

<https://wiki.archlinux.org/title/Lenovo_ThinkPad_T14_(AMD)_Gen_1>

## boot parameters

```bash
$ cat /etc/default/grub  | rg GRUB_CMDLINE_LINUX_DEFAULT
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet nowatchdog resume=UUID=xxxx-xxxx"
```
