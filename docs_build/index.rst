
文档的构建
===========

此文档建设用到的技术


github创建项目
-----------------------------

*   登录github
*   点击New repository,创建新仓库
*   clone仓库到本地 

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
#.  如果要使用rtd主题

    *   要在ReadTheDocs构建环境中添加sphinx-rtd-theme (yaml文件build.commands中添加此包)
    *   conf.py中添加 ``html_theme = 'sphinx_rtd_theme'`` ,注意是下划线
#.  ReadTheDocs输出的路径为 ``$READTHEDOCS_OUTPUT`` 默认环境


2024-07-04 下午10:43

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

reStructuredText的基本用法

标题::

    =========== 
    一级标题
    -----------
    二级标题
    ^^^^^^^^^^^
    三级标题

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

    脚注::

        .. [#] text

    字段::

        :fieldname: text


交叉引用
--------------------------

语法规则::
    
    :role:`target`


创建标签::

    必须要有下划线
    .. _label:

    text

跳转到标签::

    不要有下划线
    :ref:`here <label>`

