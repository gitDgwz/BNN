Avalonia5Mock
=====================

*   第一次调试直接空指针,来自于 ``typeof(PoetryStorage).Assembly.GetManifestResourceStream(DbName);``

    经过 ``typeof(PoetryStorage).Assembly.GetManifestResourceNames()`` 返回所有资源名
    ``dpa.Library.poetrydb.sqlite3`` 发现资源名有前缀

*   右键dpa.Library->Edit->编辑csproj,添加逻辑名poetrydb.sqlite3
    ::

        <ItemGroup>
        <None Remove="poetrydb.sqlite3" />
            <EmbeddedResource Include="poetrydb.sqlite3" >
                <LogicalName>poetrydb.sqlite3</LogicalName>
            </EmbeddedResource>
        </ItemGroup>
*   在dpa.UnitTest中创建一个Helpers文件夹，在其添加PoetryStorageHelper.cs
    ::

        public class PoetryStorageHelper
        {
            public static void RemoveDatabaseFile() =>
                File.Delete(PoetryStorage.PoetryDbPath);
        }
*   修改PoetryStorageTest.cs
    继承IDisposable，在构造函数中执行Before函数，在Dispose方法中执行After函数
    ::

        public class PoetryStorageTest : IDisposable
        {
            public PoetryStorageTest()
            {
                PoetryStorageHelper.RemoveDatabaseFile();
            }
            
            [Fact]
            public async Task InitializeAsync_Default()
            {
                var poetryStorage = new PoetryStorage();
                
                Assert.False(File.Exists(PoetryStorage.PoetryDbPath));

                await poetryStorage.InitializeAsync();
                
                Assert.True(File.Exists(PoetryStorage.PoetryDbPath));
            }
            
            public void Dispose()
            {
                PoetryStorageHelper.RemoveDatabaseFile();
            }
        } 
*   在各个平台有不同的键值存储，linux没有键值存储，所以我们手搓一个文件存储。
*   在dpa.Library Services中添加IPreferenceStorage.cs
    ::

        public interface IPreferenceStorage
        {
            void Set(string key, int value);

            int Get(string key, int defaultValue);

            void Set(string key, string value);

            string Get(string key, string defaultValue);

            void Set(string key, DateTime value);

            DateTime Get(string key, DateTime defaultValue);
        }

*   在dpa.Library Services中添加FilePreferenceStorage.cs
    ::

        public class FilePreferenceStorage : IPreferenceStorage
        {
            public void Set(string key, int value) => Set(key, value.ToString());

            public int Get(string key, int defaultValue) =>
                int.TryParse(Get(key, string.Empty), out var value) ? value : defaultValue;

            public void Set(string key, DateTime value) => Set(key, value.ToString());

            public DateTime Get(string key, DateTime defaultValue) =>
                DateTime.TryParse(Get(key,""), out var value) ? value : defaultValue;
        
            public void Set(string key, string value)
            {
                var path = PathHelper.GetLocalFfilePath(key);
                File.WriteAllText(path,value);
            }
            public string Get(string key, string defaultValue)
            {
                var path = PathHelper.GetLocalFfilePath(key);
                return File.Exists(path) ? File.ReadAllText(path) : defaultValue;
            }

        }

*   修改PoetryStorage.cs
    ::

        private readonly IPreferenceStorage _preferenceStorage;

        public PoetryStorage(IPreferenceStorage preferenceStorage)
        {
            _preferenceStorage = preferenceStorage;
        }

        public bool IsInitialized =>
            _preferenceStorage.Get(PoetryStorageConstant.VersionKey, default(int)) == PoetryStorageConstant.Version;
        
        public async Task InitializeAsync()
        {
            await using var dbFileStream = new FileStream(PoetryDbPath, FileMode.OpenOrCreate) ;
            await using var dbAssetStream = typeof(PoetryStorage).Assembly.GetManifestResourceStream(DbName);
            typeof(PoetryStorage).Assembly.GetManifestResourceNames();
            await dbAssetStream.CopyToAsync(dbFileStream);
            
            //新添加
            _preferenceStorage.Set(PoetryStorageConstant.VersionKey,PoetryStorageConstant.Version);
        }
*   dpa.UnitTest 安装Moq包
*   在PoetryStorageTest中添加mock.
    ::

        public async Task InitializeAsync_Default()
        {
            //新添加
            var preferenceStorageMock = new Mock<IPreferenceStorage>();

            var mockPreferenceStorage = preferenceStorageMock.Object;
            
            var poetryStorage = new PoetryStorage(mockPreferenceStorage);
            
            //

            Assert.False(File.Exists(PoetryStorage.PoetryDbPath));

            await poetryStorage.InitializeAsync();
            
            Assert.True(File.Exists(PoetryStorage.PoetryDbPath));
        }