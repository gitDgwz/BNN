Avalonia6Mock2
=================================

*   测试IsInitialized，在PoetryStorageTest.cs添加
    ::

        [Fact]
        public void IsInitialize_Default()
        {
            var preferenceStorageMock = new Mock<IPreferenceStorage>();
            //设置Mock行为，当有人用这两个参数，则返回某个值
            preferenceStorageMock.Setup(p => p.Get(PoetryStorageConstant.VersionKey, default(int)))
                .Returns(PoetryStorageConstant.Version);
            //
            var mockPreferenceStorage = preferenceStorageMock.Object;
            var poetryStorage = new PoetryStorage(mockPreferenceStorage);

            Assert.True(poetryStorage.IsInitialized);
            
            //验证它调用了这个方法，并且是一次
            preferenceStorageMock.Verify(
                p=>p.Get(PoetryStorageConstant.VersionKey,default(int)),Times.Once);

        }

*   poetryStorage添加返回一首诗
    ::

        public async Task<Poetry> GetPoetryAsync(int id) =>
            await Connection.Table<Poetry>().FirstOrDefaultAsync(p => p.Id==id);

*   dpa.UnitTest PoetryStorageHelper.cs添加GetInitializedPoetryStorage方法,
    用来单元测试中获得一个数据库连接
    ::

        public static async Task<PoetryStorage> GetInitializedPoetryStorage()
        {
            var preferenceStorageMock = new Mock<IPreferenceStorage>();
            preferenceStorageMock.Setup(p => p.Get(PoetryStorageConstant.VersionKey, -1))
                .Returns(-1);
            var mockPreferenceStorage = preferenceStorageMock.Object;
            var poetryStorage = new PoetryStorage(mockPreferenceStorage);
            await poetryStorage.InitializeAsync();
            return poetryStorage;
        }
*   PoetryStorageTest.cs中添加测试获得一首诗词
    ::

        [Fact]
        public async Task GetPoetryAsync_Default()
        {
            var poetryStorage = await PoetryStorageHelper.GetInitializedPoetryStorage();
            var poetry = await poetryStorage.GetPoetryAsync(10001);
            Assert.Equal("临江仙 · 夜归临皋",poetry.Name);
            
        }

*   测试发现抱错，是因为没有关闭数据库连接而去删除数据库
*   PoetryStorage.cs中添加CloseAsync方法
    ::

        public async Task CloseAsync()=>await Connection.CloseAsync();

*   在PoetryStorageTest.cs添加一行
    ::

        [Fact]
        public async Task GetPoetryAsync_Default()
        {
            var poetryStorage = await PoetryStorageHelper.GetInitializedPoetryStorage();
            var poetry = await poetryStorage.GetPoetryAsync(10001);
            Assert.Equal("临江仙 · 夜归临皋",poetry.Name);
            await poetryStorage.CloseAsync();
        }

*   PoetryStorage.cs中实现GetPoetriesAsync方法
    ::
   
        public async Task<IList<Poetry>> GetPoetriesAsync(Expression<Func<Poetry, bool>> where, int skip, int take)=>
            await Connection.Table<Poetry>().Where(where).Skip(skip).Take(take).ToListAsync();
*   在dpa.Library中添加ViewModels文件夹.并且添加ResultViewModels.cs和一个新的ViewModelBase.cs
