archlinux
=================
轻量化,极简,高定制的系统,需要一定的动手能力,拥有无与伦比的wiki和文档(中文),

下载和安装
-------------
archlinux官方有非常详细的步骤,按照官方说明即可.

踩坑的地方

#.  磁盘分区没有修改分区类型    
#.  加载国内镜像时默认没有打开,/etc/pacman.d/mirrorlist里面全是注释的,手动将其取消注释,装机成功后建议添加清华源
#.  安装GRUB时候之前使用了arch-chroot,已经在新系统中了.按照wiki中输入安装grub代码即可,注意esp为EFI System磁盘挂载点,安装之后要生成配置文件

装机后建议
----------------

*   创建普通用户

        ``useradd -m name`` ,添加用户并且创建home

*   声音

        ALSA,内核自带,可以使用alsa-utils工具, ``alsamixer`` 打开终端调节音量

*   输入法  
        
        fcitx5,输入法,仅提供基本框架,且仅支持英文。如果要输入其他语言（例如中文或日文）,则需要安装输入法引擎（IME）。

        fcitx5-im,包组提供 fcitx5包 本体、#配置工具和#输入法模块。 
        
        fcitx5-chinese-addons,包含与中文相关的 addon,例如拼音、双拼和五笔。

        安装后在GUI,configure中添加pinyin,默认只显示系统语言输入法.

*   系统中文化

        #.  修改 ``/etc/locale.gen`` 文件来设定系统中可以使用的locale,取消对应项前的注释符号“#”即可.

        #.  然后执行 ``locale-gen`` 命令,便可以在系统中使用这些 locale。

        #.  ``/etc/locale.conf`` 全局locale不建议设置为中文,可以不用修改

        #.  安装wqy-zenhei包.

*    图形界面


图形界面
---------------------------------------
*       显示服务器 X,Wayland
*       显示管理器(登陆管理器)
*       窗口管理器 
*       桌面环境


桌面环境(Xfce4)
^^^^^^^^^^^^^^^^^^^^^

#.  安装 xfce4 包

#.  需要从显示管理器中选择Xfce Session,因此需要先安装显示管理器,这里使用LightDM

        *       安装 lightdm ,xorg-server,lightdm-gtk-greeter,三个包
        *       确保使用 systemctl 启用 lightdm.service,如此来让 LightDM 开机启动.

#.  登录的右上角可以选择Xfce Session,进入桌面

#.  可以按照wiki自定义窗口显示器(开机后可能会黑屏一分钟)


python                
---------------
**python-pip与pacman冲突**,只能使用pipx,pipx则是调用pip,在虚拟隔离环境下进行安装

VScode
----------------
archlinux官方开源版无法使用扩展,微软官方版在AUR中

sphinx
-----------------
*sphinx用到的包,以下皆为在pacman中安装*

|       python-sphinx,sphinx,官方包
|       python-sphinx_rtd_theme,rtd主题,官方包
|       make,GNU make,官方包

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

VScode中esbonio插件使用pip安装,会造成冲突,使用了pacman安装也不起作用,

解决方法

#.      使用makefile文件make html
#.      安装VScode html实时预览插件,预览html文件


