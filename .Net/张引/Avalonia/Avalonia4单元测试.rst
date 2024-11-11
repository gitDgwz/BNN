Avalonia4单元测试
========================

**开始一个新项目dpa**

一个解决方案里面三个项目,一个UI,一个业务,一个单元测试


*   安装 ``semi.Avalonia`` 包
*   App.axaml修改
    ::

        <!--上面添加-->
        xmlns:semi="https://irihi.tech/semi"

        <Application.Styles>
            <!--将Sytle替换为semi-->
            <!--<FluentTheme />-->
            <semi:SemiTheme Locale="zh-CN"></semi:SemiTheme>
        </Application.Styles>

*   在解决方案下在建一个项目dpa.Library,右键dpa.Library属性->可空引用类型禁用，并且删除Class1初始类。
*   在dpa.Library中创建Models文件夹，并在其中创建Poetry.cs
    ::

        [SQLite.Table("works")]
        public class Poetry
        {
            [SQLite.Column("id")] public int Id { get; set; }
            
            [SQLite.Column("name")] public string Name { get; set; }

            [SQLite.Column("author_name")] public string Author { get; set; } = string.Empty;

            [SQLite.Column("dynasty")] public string Dynasty { get; set; } = string.Empty;
            
            [SQLite.Column("content")] public string Content { get; set; } = string.Empty;

            private string _snippet;

            [SQLite.Ignore] public string Snippet => _snippet ??= Content.Split('。')[0].Replace("\r\n", " ");
        }

*   在dpa.Library添加Helpers文件夹，并在里面创建PathHelper.cs
    ::

        public class PathHelper
        {
            private static string _localFolder = string.Empty;

            private static string LocalFoler
            {
                get
                {
                    if (!string.IsNullOrEmpty(_localFolder))
                    {
                        return _localFolder;
                    }

                    _localFolder = Path.Combine(
                        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), nameof(dpa));
                    
                    if (!Path.Exists(_localFolder))
                    {
                        Directory.CreateDirectory(_localFolder);
                    }

                    return _localFolder;
                }
            }

            public static string GetLocalFfilePath(string fileName)
            {
                return Path.Combine(_localFolder, fileName);
            }
        }

*   在dpa.Library添加Services文件夹，并在里面创建IPoetryStorage接口和PoetryStorage.cs
    ::

        public interface IPoetryStorage
        {
            bool IsInitialized { get; }

            Task InitializeAsync();

            Task<Poetry> GetPoetryAsync(int id);

            Task<IList<Poetry>> GetPoetriesAsync(Expression<Func<Poetry, bool>> where, int skip, int take);
        }

    ::

        public class PoetryStorage : IPoetryStorage
        {
            public const int NumberPoetry = 30;

            public const string DbName = "poetrydb.sqlite3";

            public static readonly string PoetryDbPath =
                PathHelper.GetLocalFfilePath(DbName);

            private SQLiteAsyncConnection _connection;

            private SQLiteAsyncConnection Connection => 
                _connection ??= new SQLiteAsyncConnection(PoetryDbPath);
            
            public bool IsInitialized { get; }
            
            public async Task InitializeAsync()
            {
                await using var dbFileStream = new FileStream(PoetryDbPath, FileMode.OpenOrCreate) ;
                await using var dbAssetStream = typeof(PoetryStorage).Assembly.GetManifestResourceStream(DbName);
                await dbAssetStream.CopyToAsync(dbFileStream);
            }

            public async Task<Poetry> GetPoetryAsync(int id)
            {
                throw new NotImplementedException();
            }

            public async Task<IList<Poetry>> GetPoetriesAsync(Expression<Func<Poetry, bool>> where, int skip, int take)
            {
                throw new NotImplementedException();
            }
        }
*   将数据库文件复制到dpa.Library项目下，并且右键属性，构建操作选择嵌入式资源

*   再在dpa解决方案下创建一个Test项目，选择xunit，名为dpa.UnitTest.
    并且在项目里面添加Services文件夹，在文件夹中创建PoetryStorageTest.cs
    
    ::

        public class PoetryStorageTest
        {
            [Fact]
            public async Task InitializeAsync_Default()
            {
                var poetryStorage = new PoetryStorage();
                
                Assert.False(File.Exists(PoetryStorage.PoetryDbPath));

                await poetryStorage.InitializeAsync();
                
                Assert.True(File.Exists(PoetryStorage.PoetryDbPath));
            }
        }

*   左上角view->tool windows->Test ，打开测试窗口