# android usb network share

1. 连接手机, 并在“个人热点”处开启 USB 网络共享
2. Linux 端查看新增网卡的名称
    
    执行命令 `ip a` , 会看到新增的网卡
    
    例如: `enp0s20f0u8`
    
3. 运行 `sudo dhclient $NETCARDNAME`
    
    对于 Arch Linux, 这个包在:
    
    ```
    extra/dhclient 4.4.3-1 [已安装]
        A standalone DHCP client from the dhcp package
    ```