Database
========================
*   数据库迁移内嵌
*   几乎所有平台都支持文件的内嵌
*   版本对比
*   const 和 static readonly 都是常量,只不过static readonly可以经过计算得出,const只能字符串赋值

Model ::

    Poetry.cs

    public class Poetry
    {
        /// 主键
        [SQLite.Column("id")]
        public int Id { get; set; }

        /// 标题
        [SQLite.Column("name")]
        public string Name { get; set; }

        /// 正文
        [SQLite.Column("content")]
        public string Content { get; set; }

        /// 朝代
        [SQLite.Column("author_name")]
        public string AuthorName { get; set; }

        /// 译文
        [SQLite.Column("translation")]
        public string Translation { get; set; }

        /// 布局
        [SQLite.Column("layout")]
        public string Layout { get; set; }

        /// 居中布局
        public const string CenterLayout = "center";
        /// 缩进布局
        public const string IndentLayout= "indent";

        /// 预览
        private string _snippet;

        [SQLite.Ignore]
        public string Snippet => 
            _snippet ?? (_snippet = Content.Split('。')[0].Replace("\r\n"," "));

    }

Services ::

    IPoetryStorage.cs

    public interface IPoetryStorage
    {
        /// 初始化诗词存储
        Task InitializeAsync();

        /// 判断诗词存储是否已经初始化
        bool IsInitialized();

        /// 获取一个诗词
        /// <param name="id"></param>
        /// <returns></returns>
        Task<Poetry> GetPoetryAsync(int id);

        /// 获取满足给点条件的诗词集合
        /// <param name="where">Where条件</param>
        /// <param name="skip">跳过结果的数量</param>
        /// <param name="take">返回结果的数量</param>
        /// <returns></returns>
        Task<IList<Poetry>> GetPoetriesAsync(
            Expression<Func<Poetry, bool>> where,int skip,int take);

    }

    public static class PoetryStorageConstants
    {
        /// 诗词数据库版本号
        public const int Version = 1; 

        public const string VersionKey=nameof(PoetryStorageConstants)+"."+nameof(Version);

    }

    ----------------------------------------------------------------

    PoetryStorage.cs

    public class PoetryStorage : IPoetryStorage
    {
        /// 数据库文件名
        private const String DbName = "poetrydb.sqlite3";

        private static readonly string PoetryDbpath =
            Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), DbName);

        private SQLiteAsyncConnection _connection;

        public SQLiteAsyncConnection Connection => 
            _connection ?? (_connection = new SQLiteAsyncConnection(PoetryDbpath));

        public async Task<Poetry> GetPoetryAsync(int id) =>
            await Connection.Table<Poetry>().FirstOrDefaultAsync(p=>p.Id==id);

        public async Task<IList<Poetry>> GetPoetriesAsync(Expression<Func<Poetry, bool>> where, int skip, int take)=>
        
            await Connection.Table<Poetry>().Where(where).Skip(skip).Take(take).ToListAsync(); 

        public async Task InitializeAsync()
        {
            using (var dbFilesStream = new FileStream(PoetryDbpath, FileMode.Create))
            using (var dbAssertStream = Assembly.GetExecutingAssembly()
                .GetManifestResourceStream(DbName)) 
            {
                await dbAssertStream.CopyToAsync(dbFilesStream);
            }
            Preferences.Set(PoetryStorageConstants.VersionKey,PoetryStorageConstants.Version);
        }

        public bool IsInitialized() =>
            Preferences.Get(PoetryStorageConstants.VersionKey, -1) == PoetryStorageConstants.Version;
         
    }

根目录 poetrydb.sqlite3(右键属性,生成操作 嵌入的资源)