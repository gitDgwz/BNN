Avalonia1创建项目
==========================
准备工作

#.  安装jetbrains rider
#.  使用.net安装Avalonia,官网有
#.  使用rider 直接创建avalonia Mvvm项目

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*   安装sqlite-net-pcl包
*   在项目中创建helpers文件夹,并创建PathHelper.cs用于数据库路径的创建
    ::

        PathHelper.cs 

        public static class PathHelper
        {
            private static string _localFolder = String.Empty;

            private static string LocalFolder
            {
                get
                {
                    if (!string.IsNullOrEmpty(_localFolder))
                    {
                        return _localFolder;
                    }

                    _localFolder = Path.Combine
                        (Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), nameof(Mvvm));

                    if (!Directory.Exists(_localFolder))
                    {
                        Directory.CreateDirectory(_localFolder);
                    }

                    return _localFolder;
                }
                
            }

            public static string GetLocalFilePath(string fileName)
            {
                return Path.Combine(LocalFolder, fileName);
            }
        }

*   创建Models文件夹和Poetry.cs
    ::

        public class Poetry
        {
            public int Id { get; set; }
            public string Name { get; set; } = string.Empty;
        }

*   创建Services文件夹，IPoetryStorage.cs和PoetryStorage.cs
    ::

        public interface IPoetryStorage
        {
            Task InsertAsync(Poetry poetry);

            Task InitializeAsync();
        }

    ::

        public class PoetryStorage : IPoetryStorage
        {
            public const string DbName = "poetrydb.sqlite3";

            public static readonly string PoetryDbPath =
                PathHelper.GetLocalFilePath(DbName);

            private SQLiteAsyncConnection _connection;

            private SQLiteAsyncConnection Connection =>
                _connection ??= new SQLiteAsyncConnection(PoetryDbPath);
            
            public async Task InitializeAsync()
            {
                await Connection.CreateTableAsync<Poetry>();
            }
            
            public async Task InsertAsync(Poetry poetry)
            {
                await Connection.InsertAsync(poetry);
            }
        }




code first

C# sqlite先连接数据库再创表,可以连接空数据库