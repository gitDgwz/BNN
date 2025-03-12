
文档的构建
===========

此文档建设用到的技术


github创建项目
-----------------------------

*   登录github
*   点击New repository,创建新仓库
*   clone仓库到本地 

python下载
------------------------------
下载python和pip.

*   windows中直接Store中下载就可以,自带pip.最方便,手动去官网下载网络连接困难.
*   linux只用包管理器下载
*   .. _python换源:

    **换清华源** ``pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple``,安装快了,但是search还是慢,应该只换了下载源


使用sphinx创建reST项目
-----------------------------
    
下载python-sphinx包,包名一般为python-sphinx或者python3-sphinx

*   python pip命令

       
*   opensuse zypper命令

下载完成后,在本地仓库终端输入 ``sphinx-quickstart`` 创建初始项目

在用一下代码上传::

    git add .
    git commit -m "message"
    git push

搭建ReadTheDocs线上文档
-----------------------------

*   注册并且登录ReadTheDocs
*   关联github账号
*   选择某一个仓库
*   在目录根路径添加.readthedocs.yaml文件(ReadTheDocs官网提供默认文件)
*   在ReadTheDocs网页中点击build

一些需要注意的地方
-----------------------------

#.  conf.py文件中中文为zh_CN是下划线
#.  如果要使用rtd主题(必须在构建的时候有这个包)

    *   要在ReadTheDocs构建环境中添加sphinx-rtd-theme (yaml文件build.commands中添加此包)
    *   conf.py中添加 ``html_theme = 'sphinx_rtd_theme'`` (如果是别的主题就自行更改),注意是下划线
#.  ReadTheDocs输出的路径为 ``$READTHEDOCS_OUTPUT`` 默认环境

#.  不支持#符号作为文件名
#.  可以写多个 ``..totree`` 进行引用

2024-07-04 下午10:43

vscode预览
--------------------------------
使用Esbonio插件(可以一遍修改一遍预览),安装后vscode右上角会有个预览的按键.

#.  必须要有python环境和pip,因为安装Esbonio它使用的就是pip下载

        如果在windows中可以直接在windowsStore中下载安装,因为python的网络并不好

        下载后可以换源 :ref:`换清华源<python换源>`

#.  必须有对应的主题包,要不然会报错

#.  不需要有sphinx包

**如果预览案件不起作用.注意vscdoe左下角的报错**

**记得重启vscode,有时候必须重启才能起效.**


reStructuredText的基本用法
-------------------------------------------

链接树::

    .. toctree:: 
        
        :glob: #如果要使用*包揽全局

        :caption: #添加标题

        #以当前层级目录为位置

        .. #上一层目录

        * #当前目录所有
        




标题::

    =========== 
    一级标题
    -----------
    二级标题
    ^^^^^^^^^^^
    三级标题

    ***********
    忘了几级标题

内联标记::
    
    *text*
    斜体
    **text**
    加粗
    ``text``
    代码

图片插入::

    .. image:: path
        :height: 100px
        :width: 200 px
        :scale: 50 %
        :alt: alternate text
        :align: right
        
    可以用绝对路径/ ,或者相对路径../


常用

    换行::

        | text

    代码块::

        :: 
            text

    告诫Admonitions::

        ("attention", "caution", "danger", "error", "hint", "important", "note", "tip", "warning")
        .. note::
            text

    注释::

        .. text

    脚注::

        .. [#] text

    字段::

        :fieldname: text


交叉引用
--------------------------

语法规则::
    
    :role:`target`

    例如:
    
    :ref:``

创建标签::

    必须要有下划线
    .. _label:

    text

跳转到标签::

    不要有下划线
    :ref:`here <label>`

