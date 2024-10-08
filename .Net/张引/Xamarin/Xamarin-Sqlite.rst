sqlite
=================================
codeFirst

xaml ::

        #用于显示数据的滚动条控件
        <ScrollView Grid.Row="0">
            <ListView x:Name="ResultListView">
                <ListView.ItemTemplate>

                    <DataTemplate>
                        <TextCell Text="{Binding Id}"
                                  Detail="{Binding IsFavorite}"/>
                    </DataTemplate>

                </ListView.ItemTemplate>
            </ListView>
        </ScrollView>
        
        #三个按钮,创建数据库,插入数据和读取数据
        <StackLayout Grid.Row="1">
            <Button Text="create database"
                    x:Name="CreateDatabaseButton"
                    Clicked="CreateDatabaseButton_Clicked"/>
            
            <Button Text="insert database"
                    x:Name="InsertDatabaseButton"
                    Clicked="InsertDatabaseButton_Clicked"/>
            
            <Button Text="read database"
                    x:Name="ReadDatabaseButton"
                    Clicked="ReadDatabaseButton_Clicked"/>
        </StackLayout>

cs ::

    public partial class MainPage : ContentPage
    {   

        public MainPage()
        {
            InitializeComponent();
        }

        #数据库连接
        private SQLiteAsyncConnection connection;

        private async void CreateDatabaseButton_Clicked(object sender, EventArgs e)
        {   
            #使用Path.Combine来连接路径,因为不同操作系统路径符不同
            #Environment.GetFolderPath获得路径
            #Environment.SpecialFolder.LocalApplicationData一个特殊路径,UWP和android,ios都有的路径
            #创建MyData.db文件
            var databasePath=
            Path.Combine(
                Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData),
                "MyData.db");
            
            #创建连接,需要数据库文件路径
            connection = new SQLiteAsyncConnection(databasePath);

            #创建表如果它不存在
            await connection.CreateTableAsync<Favorite>();
        }

        
        private Random random = new Random();

        private async void InsertDatabaseButton_Clicked(object sender, EventArgs e)
        {   
        
            var favorite=new Favorite() {IsFavorite=random.NextDouble()>0.5 };
            #插入数据
            await connection.InsertAsync(favorite);

        }


        private async void ReadDatabaseButton_Clicked(object sender, EventArgs e)
        {   
            #将数据库表数据组成List传入ListView
            ResultListView.ItemsSource=await connection.Table<Favorite>().ToListAsync();
        }
    }

    public class Favorite
    {   
        #主键,自增
        [PrimaryKey,AutoIncrement]
        public int Id { get; set; }

        public bool IsFavorite { get; set; }
    
    }


下载SqliteBrowser查看数据库