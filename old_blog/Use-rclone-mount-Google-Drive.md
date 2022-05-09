---
title: 使用 rclone 挂载 Google Drive 硬盘
---

> 记录一下使用 rclone 挂载 Google Drive 硬盘内容的过程

# 下载安装所需工具

[rclone](https://rclone.org/downloads/)、[WinFsp](http://www.secfs.net/winfsp/download/)

P.S：rclone 下载完成之后需要配置环境变量 && ~~使用 Proxifier 设置走代理~~，WinFsp 直接下载之后安装即可。

2019-11-26 更新：rclone 官方的 doc 中提到了系统代理，详见 <https://rclone.org/docs/#other-environment-variables> 。简单地说，就是添加 HTTPS_PROXY 和 HTTP_PROXY 两个系统环境变量，里面的值填 `http://127.0.0.1:1080`（你的 ssr 代理端口），即可让 rclone 走代理。

# rclone 添加云盘

> 如果你有着不错的英文文档阅读能力，可以先去看一下文章底部的官方文档链接

1. 在 cmd 中输入 `rclone config` ，输入 `n` 回车，选择创建 New remote；
2. 输入 config 的名字，回车；
3. Google Drive 是第 12 个选项（`12 / Google Drive`） ，所以输入 `12` ，回车；
4. `client_id`、`client_secret` 直接回车使用默认值跳过；
5. `Choose a number from below, or type in your own value`：输入 `1` ， 回车；
6. `root_folder_id`、`service_account_file` 直接回车跳过；
7. 出现 `Edit advanced config? (y/n)` 时，输入 `y` 回车；
8. `service_account_credentials`、`team_drive`、`auth_owner_only`、`use_trash`、`skip_gdocs` 的选项，直接回车跳过；
9. `shared_with_me`：**如果你想挂载 与我分享 文件夹里的内容，一定要输入 `true`**
10. **余下选项全部回车跳过，直到出现** `Use auto config?` ，输入 `n` 回车；
11. 此时浏览器应该会弹到一个 Google Drive 的授权登录地址，**点击允许并复制授权代码**，回到 cmd 内粘贴授权代码然后回车；
12. `Configure this as a team drive?`，输入 `n` 回车；
13. 确认 remote 配置无误，输入 `y` 回车。

# 挂载云盘

`rclone mount Riho:/ X: --cache-dir F:\Temp --vfs-cache-mode writes`

说明:

"Riho" 为『rclone 添加云盘』中第二步所输入的 **remote config 的名字**。

"X" 为挂载于本地 PC 上的盘符 (必须是未使用的。比如有了 C, D, E, F, G，那么除了这些盘符以外的都可以)。

"F" 为设置缓存的目录。(在本地PC上，已有的盘符，需要选择剩余空间较多的一个。)

关于 `vfs-cache-mode` 的说明:

<https://rclone.org/commands/rclone_mount/#file-caching>

根据说明修改为自己需要的代码之后，先在cmd里运行一下，如果没有意外的话，是可以看到已经挂载了的。

# 使用 VBS 脚本来挂载

创建名为 `rclone` 的 VBS 文件，代码如下:

```vbscript
Option Explicit
Dim WMIService, Process, Processes, Flag, WS
Set WMIService = GetObject("winmgmts:{impersonationlevel=impersonate}!\\.\root\cimv2")
Set Processes = WMIService.ExecQuery("select * from win32_process")
Flag = true
for each Process in Processes
     if strcomp(Process.name, "rclone.exe") = 0 then
         Flag = false
         exit for
     end if
next
Set WMIService = nothing
if Flag then
     Set WS = Wscript.CreateObject("Wscript.Shell")
     WS.Run "rclone mount Riho:/ X: --cache-dir F:\Temp --vfs-cache-mode writes", 0

end if
```

注意：倒数第二行这里，WS.Run之后的代码，与第二步(挂载云盘)的代码相同。只需修改这一条即可。

输入代码的时候，直接从 `Option Explicit` 这里开始，不需要空行。最后一行，即是 `end if` 。除了倒数二条以外，其余的不要做任何更改。

保存，退出。双击这个 rclone 的 VBS 文件，但是，不会有任何反应的。打开任务管理器查看，没有意外的话，应该有一个叫 `rclone` 在运行。  

> Rclone 挂载 Google Drive 硬盘的部分到此结束。以后的话，只要双击这个"rclone"的VBS文件，就可以加载Google Drive的网盘了喔。

# 设置 Potplayer

> 因为我挂载 Google Drive 硬盘主要是为了观看小萌分享给我的美剧，所以 Potplayer 的配置也放到这里了

下载安装 [madVR](http://www.madvr.com/)、[LAVFilters](https://github.com/Nevcairiel/LAVFilters/releases)、[xy-VSFilter](https://github.com/Cyberbeing/xy-VSFilter/releases) 。

\----------  多图预警  ----------

![potplayer01](Use-rclone-mount-Google-Drive/potplayer01.jpg)
![potplayer02](Use-rclone-mount-Google-Drive/potplayer02.jpg)
![potplayer03](Use-rclone-mount-Google-Drive/potplayer03.jpg)
![potplayer04](Use-rclone-mount-Google-Drive/potplayer04.jpg)
![potplayer05](Use-rclone-mount-Google-Drive/potplayer05.jpg)
![potplayer06](Use-rclone-mount-Google-Drive/potplayer06.jpg)
![potplayer07](Use-rclone-mount-Google-Drive/potplayer07.jpg)
![potplayer08](Use-rclone-mount-Google-Drive/potplayer08.jpg)
![potplayer09](Use-rclone-mount-Google-Drive/potplayer09.jpg)
![potplayer10](Use-rclone-mount-Google-Drive/potplayer10.jpg)
![potplayer11](Use-rclone-mount-Google-Drive/potplayer11.jpg)

LAV Splitter 的设置，input 这里勾选所有，前面的那一栏保持默认：

![LAVSplitter](Use-rclone-mount-Google-Drive/LAVSplitter.jpg)

LAV Audio 的设置参考“万年冷冻库”的文章。 <https://lysandria1985.blogspot.com/2013/01/2-lav-filters.html>

LAV Video 的设置，保持默认就好。(软解)
硬解的话，最好是用 cuvid ，要是用不了，那就用 dxva 。举例：1070的显卡可以用 cuvid 硬解 hevc10bit 。970 的就只能 avc8bit ，解不了 hevc10bit ，要用 dxva 才行。anyway，这是由芯片决定的。综合来说的话，dxva 最好。（但是同时也更消耗性能）
如果要用硬解的话，反交错可以设置 50/60p 。软解的话，千！万！不！要！打！开！

madVR 的设置，参考“万年冷冻库”。 <https://lysandria1985.blogspot.com/2013/01/3-madvr.html>

最后一步就是显卡驱动设置。如下：

![NVIDIA显卡设置](Use-rclone-mount-Google-Drive/NVIDIA01.jpg)

![NVIDIA显卡设置](Use-rclone-mount-Google-Drive/NVIDIA02.jpg)

这里要特别说明一下，**HDMI 接口的必须这样设置**。不然会出现错误的 yc 伸张。我的是 DP 接口。

# 感谢 & 版权声明

非常感谢小萌在群组里发的教程（づ￣3￣）づ╭❤～这个~~不算是教程的~~教程其实是受小萌的启发写的。

文中『设置 Potplayer』处的图片皆为小萌提供的截图

![1540742117699](Use-rclone-mount-Google-Drive/Riho.png)

# 其他

[RcloneBrowser](https://github.com/mmozeiko/RcloneBrowser)：一个 Rclone 的 GUI 客户端。（~~GUI 赛高~~

> 关于 rclone GUI，我注意到官网的文档说现在增加了一个 [GUI 实验性功能](https://rclone.org/gui/)，可以让 rclone 提供基于 web 的 GUI 。

附一些参考链接：

[rclone 官网的说明](https://rclone.org/docs/)

[rclone 的 GitHub 地址](https://github.com/ncw/rclone)
