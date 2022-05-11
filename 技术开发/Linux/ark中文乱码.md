# ark 中文乱码问题

`sudo pacman -Syu p7zip-natspec`, 这个包在 archlinuxcn 有打包.

关掉 ark "设置" -> "插件" 中的 `Info-zip` 和 `Libzip` 两个插件, 选中 `P7zip` 插件, 重启 ark 就可以正常显示 zip 文件的中文了.
