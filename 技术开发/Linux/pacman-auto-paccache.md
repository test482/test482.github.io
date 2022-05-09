# pacman auto paccache

```text
[Trigger]
Operation = Install
Operation = Upgrade
Type = Package
Target = *

[Action]
Description = "Keep the latest cache and the currently installed."
When = PostTransaction
Exec = /usr/bin/paccache -rvk2
```

添加成 pacman hook, 每次滚包的时候都会仅保留最新的两个版本包缓存.
