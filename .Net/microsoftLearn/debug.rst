debug
=========================


vscode debug
---------------------------

查看变量
    左侧面板中,可以双击改变值,或者将鼠标放在代码变量上可以直接查看值

监视变量
    可以添加变量以监视其变化

调用堆栈
    每次程序输入函数时，都会向调用堆栈中添加一个条目

    错误消息下方那一组 at [...] 行称为“堆栈跟踪”。 堆栈跟踪提供了出现异常前调用的每个函数的名称和源

控件执行
    *   继续或暂停执行：
            如果执行已暂停，则它将继续，直到命中下一个断点。 如果程序正在运行，则按钮会切换到暂停按钮，可以使用该按钮来暂停执行。
    *   单步跳过：
            执行当前上下文中的下一个代码语句。
    *   单步执行：
            与单步跳过类似，但如果下一个语句是函数调用，则转到该函数的第一个代码语句（与 step 命令相同）。
    *   单步跳出：
            如果在函数内部，则执行该函数的其余代码，并在初始函数调用后跳回到该语句（与 out 命令相同）。
    *   重启：
            从头开始重启程序。
    *   停止：
            结束执行并退出调试器。

使用调试控制台
    在运行时候的额外控制台,调试控制台不接受正在运行的程序的终端输入.


左下角的断点
    可以取消单选框,取消断点    


.NET 应用程序中的日志记录和跟踪
-------------------------------------------

将信息写入输出窗口
^^^^^^^^^^^^^^^^^^^^^^^^^^

选择使用哪种打印样式 API 由用户自己决定。 主要区别包括：

*   System.Console

    *    始终启用，并始终写入控制台。
    *    适用于客户可能需要在发行版中看到的信息。
    *    由于这是最简单的方法，所以常常用于临时调试。 此调试代码通常不会签入到源代码管理中。

*   System.Diagnostics.Trace

    *    仅在定义 TRACE 时启用。
    *    写入附加侦听器，默认情况下为 DefaultTraceListener。
    *    创建将在大多数生成中启用的日志时，请使用此 API。

*   System.Diagnostics.Debug

    *    仅在定义 DEBUG 时才启用（处于调试模式时）。
    *    写入附加调试器。
    *    创建仅在调试生成中启用的日志时，请使用此 API。

定义 TRACE 和 DEBUG 常数
^^^^^^^^^^^^^^^^^^^^^^^^^^

默认情况下，当应用程序在调试模式下运行时，将定义 DEBUG 常数。 
可以通过在属性组的项目文件中添加 DefineConstants 条目进行控制。
除了对 Debug 配置启用 DEBUG 之外，
下面的示例还演示了对 Debug 和 Release 配置启用 TRACE。

::

    <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
        <DefineConstants>DEBUG;TRACE</DefineConstants>
    </PropertyGroup>
    <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
        <DefineConstants>TRACE</DefineConstants>
    </PropertyGroup>

条件输出
^^^^^^^^^^^^^^^^^^^^^^^^^^

除了简单的 Write 和 WriteLine 方法之外，还可以使用 WriteIf 和 WriteLineIf 添加条件

::

    if(count == 0)
    {
        Debug.WriteLine("The count is 0 and this may cause an exception.");
    }
    
::

    Debug.WriteLineIf(count == 0, "The count is 0 and this may cause an exception.");

断言,验证是否存在特定条件
^^^^^^^^^^^^^^^^^^^^^^^^^^

断言或 Assert 语句会测试指定为 Assert 语句参数的条件。 如果条件的计算结果为 true，则不会发生任何操作。 如果条件的计算结果为 false，则断言将失败。 **如果运行的是调试生成，则程序会进入中断模式**。

可以使用位于 System.Diagnostics 命名空间中的 Debug 或 Trace 的 Assert 方法。 **程序的发行版中不包含 Debug 类方法**，因此它们不增大发行代码的大小，也不会减慢发行代码的速度。

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

利用 System.Diagnostics 命名空间中的 Debug 和 Trace 是在运行和调试应用程序时提供附加上下文的好方法。