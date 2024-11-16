Avalonia8无限滚动及其原理
=================================

*   在dpa.Library安装AvaloniaInfiniteScrolling.Collection包
*   在dpa中安装AvaloniaInfiniteScrolling.Control包
*   修改ResultViewModel.cs,AvaloniaInfiniteScroll相关组件
    ::

*   修改MainWindow.axaml,删除Behaviors和ItemsCon trol,添加ais命名空间
    ::

*   在App.axaml ``<Application.Styles>`` 中添加 ``<StyleInclude Source="avares://AvaloniaInfiniteScrolling.Control/AvaloniaInfiniteScrollControlStyle.axaml" />``

*   在dpa.UnitTest中添加ViewModels文件夹并在其中创建ResultViewModelTest.cs
    ::

*   在xunit中，单元测试是并行的，可能同时创建和删除数据库导致错误，
    
    在dpa.UnitTest中创建xunit.runner.json文件
    ::

        {
        "parallelizeAssembly": false,
        "parallelizeTestCollection": false
        }
    
    右键xunit.runner.json属性, ``复制到输出目录``选择 ``如果较新则复制`` (``Copy if newer``),测试变成单线程

*   在首次开启项目时候没有初始化数据库,导致报错,但是InitializeAsync是个异步方法不能够发在构造器里面,
  
    于是在 ``ResultViewModel``构造器添加 ``poetryStorage.InitializeAsync().RunSynchronously();`` 用来初始化，但是添加了这句，后面数据则不能显示.

    .. danger:: 

        后面会介绍真正应该使用的方法，现在必须使用 ``poetryStorage.InitializeAsync().RunSynchronously();`` 并且开启项目来初始化数据库，初始化后要关闭项目再删掉该行项目才能正常运行.

        是一个临时的没有办法的方法.

~~~~~~~~~~~~~~~~~~~~~~~~~~
Avalonia判断滚动条触发加载

MAUI判断显示的是否是最后一个item来判断加载

Control在View层,Collection在Library层

构造函数不能异步

