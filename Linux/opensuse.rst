opensuse
=====================
本人使用的系统,它拥有功能较全的Yast控制系统,rpm包,并且自带夜灯功能

下载和安装
------------------------

*   官网下载系统软件
*   插入USB,并将系统软件写入到USB(需要用到U盘写入软件)
*   设置BIOS从U盘启动,并且将BIOS模式改到UEFI
*   重新启动,进入引导程序

详细过程在opensuse官网,有中文社区

常用命令
------------------------

zypper包管理器

``zypper install`` 下载
    
``zypper remove`` 删除

``zypper search`` 查找


DNS和Hosts文件
------------------------

可用 ``cat`` , ``vim`` 查看或者修改以下文件

DNS系统路径 ``/etc/resolv.conf``

Hosts系统路径 ``/etc/hosts``

