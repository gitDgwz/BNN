IO
=======================
System.IO


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Directory

::

    📂 stores
    📄 sales.json
    📄 totals.txt
    📂 201
       📄 sales.json
       📄 salestotals.json
       📄 inventory.txt
    📂 202

``Directory.EnumerateDirectories`` 列出顶级仔细阅读目录的名称.

::

    IEnumerable<string> listOfDirectories = Directory.EnumerateDirectories("stores");

    foreach (var dir in listOfDirectories) {
        Console.WriteLine(dir);
    }

    // Outputs:
    // stores/201
    // stores/202


``Directory.EnumerateFiles`` 若要列出目录中所有文件的名称,传入相对路径则打印相对路径,传入绝对则打印绝对路径

::

    IEnumerable<string> files = Directory.EnumerateFiles("stores");

    foreach (var file in files)
    {
        Console.WriteLine(file);
    }

    // Outputs:
    // stores/totals.txt
    // stores/sales.json

::

    // Find all *.txt files in the stores folder and its subfolders
    IEnumerable<string> allFilesInAllFolders = Directory.EnumerateFiles("stores", "*.txt", SearchOption.AllDirectories);

    foreach (var file in allFilesInAllFolders)
    {
        Console.WriteLine(file);
    }

    // Outputs:
    // stores/totals.txt
    // stores/201/inventory.txt

``Directory.GetCurrentDirectory`` 获得当前目录的完整路径

``Directory.CreateDirectory`` 创建目录

``Directory.Exists`` 目录是否存在

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Environment:

``System.Environment.SpecialFolder`` 枚举,指定用于检索特殊系统文件夹路径的常量

``Environment.NewLine`` 换行符


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Path:

``Path.DirectorySeparatorChar`` 自动将该字段解释为适用于操作系统的分隔符,
例如,Windows 使用反斜杠 (stores\201),而 macOS 使用正斜杠 (stores/201)。

``Path.Combine`` 联接路径,如果想要获取stores里面的201文件夹
::

    Console.WriteLine(Path.Combine("stores","201")); // outputs: stores/201

``Path.GetExtension`` 指示文件的扩展名

``DirectoryInfo`` 类

``FileInfo`` 类
::

    string fileName = $"stores{Path.DirectorySeparatorChar}201{Path.DirectorySeparatorChar}sales{Path.DirectorySeparatorChar}sales.json";

    FileInfo info = new FileInfo(fileName);

    Console.WriteLine($"Full Name: {info.FullName}{Environment.NewLine}Directory: {info.Directory}{Environment.NewLine}Extension: {info.Extension}{Environment.NewLine}Create Date: {info.CreationTime}"); // And many more

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

File:

``File.WriteAllText`` 来创建文本和内容,覆盖老文件

``File.AppendAllText`` 追加文本内容

