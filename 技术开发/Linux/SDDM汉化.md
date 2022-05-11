# SDDM 汉化

`sudo systemctl edit sddm.service`

在 `### Anything between here and the comment below will become the new contents of the file` 一行下面写：

```text
[Service]
Environment=LANG=zh_CN.UTF-8
```
