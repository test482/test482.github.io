# SDDM 汉化

`sudo systemctl edit sddm.service`

在 `### Anything between here and the comment below will become the new contents of the file` 一行下面写：

```text
[Service]
Environment=LANG=zh_CN.UTF-8
```

其实我觉得 SDDM 的界面并不需要汉化, 总共没几个词, 还都是简单词语. 不过这是个学习 `systemctl edit` 的好例子.
