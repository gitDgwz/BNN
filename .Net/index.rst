.Net学习笔记
====================

.. toctree:: 
    :maxdepth: 2

    杨中科/index

    microsoftLearn/index


VScode
=================

跨平台代码编辑器

vscode commands
--------------------

``code .`` 以当前目录打开vscode


VScode .Net安装
----------------

如果要开发 .NET 应用，请安装 SDK(包括运行时)。
或者，如果只需运行应用程序，请安装运行时.
如果要安装该运行时，建议安装 ASP.NET Core 运行时，
因为它同时包括 .NET 和 ASP.NET Core 运行时。

openSUSE 15安装 .NET 之前，请运行以下命令，将 Microsoft 包签名密钥添加到受信任密钥列表，
并添加 Microsoft 包存储库。 打开终端并运行以下命令：::

    sudo zypper install libicu
    安装libicu包管理

    sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
    这条命令导入微软的 GPG 密钥，用于验证从微软仓库下载的软件包的真实性，确保软件包在传输过程中没有被篡改。

    wget https://packages.microsoft.com/config/opensuse/15/prod.repo
    这条命令使用 wget 工具从微软的服务器下载产品仓库配置文件(prod.repo)

    sudo mv prod.repo /etc/zypp/repos.d/microsoft-prod.repo
    这条命令将下载的 prod.repo 文件移动到 /etc/zypp/repos.d/ 目录下，并将其重命名为 microsoft-prod.repo,
    zypper 包管理器会从这个目录查找仓库配置。
    
    sudo chown root:root /etc/zypp/repos.d/microsoft-prod.repo
    这条命令将 microsoft-prod.repo 文件的所有权改为 root:root,这是一种安全措施,防止未经授权的用户修改仓库配置文件。

VScode 新建项目
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``dotnet new list`` 查看模板列表

VScode Blazor项目创建(.net)

    * ``dotnet --version`` 确认安装了.Net

    * ``dotnet new blazor -o BlazorAPP`` 创建Blazor 并取名为BlazorAPP

    * ``cd BlazorAPP`` 进入项目文件夹

    * ``ls`` 查看项目结构

    * ``dotnet watch`` 创建并且启动应用程序


VScode 工作区和配置文件
--------------------------
| 工作区:能保存在桌面,直接点击图标快速启动,并且能保存工作区使用的扩展
| 配置文件:创建保存后,可以通过切换配置,更换相应的扩展

* 工作区:文件->将文件添加到工作区->将工作区另存为...

* 配置文件:左下角齿轮设置->配置文件->创建配置文件