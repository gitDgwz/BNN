Xamarin-UnitTestingDatabase
===================================

#.  新建项目NUnit项目Dpx.UnitTest
#.  新建文件夹Helpers
#.  添加PoetryStorageHepler.cs 静态帮助类
    ::

        public static class PoetryStorageHepler
        {
            /// <summary>
            /// 诗词数量
            /// </summary>
            public const int NumberPoetry = 139;
            
            /// <summary>
            /// 获得已初始化诗词存储
            /// </summary>
            /// <returns></returns>
            public static async Task<PoetryStorage> GetInitializedPoetryStorageAsync()
            {
                PoetryStorage poetryStorage = new PoetryStorage(new Mock<IPreferenceStorage>().Object);
                await poetryStorage.InitializeAsync();
                return poetryStorage;
            }

            /// <summary>
            /// 删除诗词数据库文件.
            /// </summary>
            public static void RemoveDatabaseFile() => File.Delete(PoetryStorage.PoetryDbpath);


        }

#.  新建文件夹 Services
#.  .. danger:: 
        Assembly.
        GetExecutingAssembly().
        GetManifestResourceStream(DbName);
        
        虽然参数中DbName为poetrydb.sqlite3,但是取得的值自动添加了前缀得Dpx.poetrydb.sqlite3
        所以要在Dpx项目中设置,使其逻辑值固定为poetrydb.sqlite3
        ::

            <ItemGroup>
            <EmbeddedResource Include="poetrydb.sqlite3" >
                <LogicalName>poetrydb.sqlite3</LogicalName>
            </EmbeddedResource>
            </ItemGroup>
            

#.  添加PoetryStorageTest.cs
    ::
        
        public class PoetryStorageTest
        {   
            [SetUp,TearDown]
            public static void RemoveDatabaseFile()=>
                PoetryStorageHepler.RemoveDatabaseFile();
            
            /// <summary>
            /// 测试初始化诗词存储
            /// </summary>
            [Test]
            public async Task TestInitializeAsync()
            {
                Assert.IsFalse(File.Exists(PoetryStorage.PoetryDbpath));

                var preferenceStorageMock = new Mock<IPreferenceStorage>();
                var mockPreferenceStorage=preferenceStorageMock.Object;

                PoetryStorage poetryStorage = new PoetryStorage(mockPreferenceStorage);

                await poetryStorage.InitializeAsync();

                Assert.IsTrue(File.Exists(PoetryStorage.PoetryDbpath));

                ///验证使用到了Set函数,并且只使用了一次
                preferenceStorageMock.Verify(
                    p=>p.Set(PoetryStorageConstants.VersionKey,PoetryStorageConstants.Version),Times.Once);


            }
        }

    [SetUp]:指方法在测试开始的时候执行

    [TearDown]:指方法在测试结束的时候执行

    [Test]:指该方法是单元测试方法

    Assert断言方法,IsFalse(bool)其中bool一定为false才能过,否则报错,
    IsTrue(bool)其中bool一定为true才能过,否则报错.

    Mock需要安装moq包,暂时没有讲解,作用是可以在不实现接口的情况下快速提供实现类

    ::

        /// <summary>
        /// 测试诗词存储已经初始化
        /// </summary>
        [Test]
        public void TestIsInitialized()
        {
            var preferenceStorageMock = new Mock<IPreferenceStorage>();

            preferenceStorageMock.Setup(p => p.Get(PoetryStorageConstants.VersionKey, -1)).
                Returns(PoetryStorageConstants.Version);

            var mockPreferenceStorage = preferenceStorageMock.Object;

            PoetryStorage poetryStorage = new PoetryStorage(mockPreferenceStorage);

            Assert.IsTrue(poetryStorage.IsInitialized());   
        }

    ::

        /// <summary>
        /// 测试诗词存储没有初始化
        /// </summary>
        [Test]
        public void TestIsNotInitialized()
        {
            var preferenceStorageMock = new Mock<IPreferenceStorage>();

            preferenceStorageMock.Setup(p => p.Get(PoetryStorageConstants.VersionKey, -1)).
                Returns(PoetryStorageConstants.Version-1);

            var mockPreferenceStorage = preferenceStorageMock.Object;

            PoetryStorage poetryStorage = new PoetryStorage(mockPreferenceStorage);

            Assert.IsFalse(poetryStorage.IsInitialized());
        }
    
    ::

        /// <summary>
        /// 测试获取一个诗词
        /// </summary>
        /// <returns></returns>
        [Test]
        public async Task TestGetPoetryAsync()
        {
            PoetryStorage poetryStorage = await PoetryStorageHepler.GetInitializedPoetryStorageAsync();
            var poetry = await poetryStorage.GetPoetryAsync(10001);
            Assert.AreEqual("临江仙 · 夜归临皋", poetry.Name);
            await poetryStorage.CloseAsync();
        }
    
    ::

        /// <summary>
        /// 获取满足给定条件的诗词集合
        /// </summary>
        /// <returns></returns>
        [Test]
        public async Task TestGetPoetriesAsync()
        {
            PoetryStorage poetryStorage = await PoetryStorageHepler.GetInitializedPoetryStorageAsync();
            
            var where=Expression.Lambda<Func<Poetry,bool>>(Expression.Constant(true),
                Expression.Parameter(typeof(Poetry),"p"));
            
            var poetries=await poetryStorage.GetPoetriesAsync(where,0,int.MaxValue);

            Assert.AreEqual(PoetryStorageHepler.NumberPoetry, poetries.Count());

            await poetryStorage.CloseAsync();

        }
 

