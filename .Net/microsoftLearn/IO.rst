IO
=======================
System.IO


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例
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


``Directory.EnumerateFiles`` 若要列出目录中所有文件的名称

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


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

S