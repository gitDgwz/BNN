xamarin-Mvvm-Iservice
=========================

需要以下三个包

*   MvvmLightLibs
*   Refractored.MvvmHelpers
*   sqlite-net-pcl

项目结构

*   Models(dir)

    *   Favorite.cs 

    :: 

        public class Favorite
        {
            //Atrribute
            [PrimaryKey, AutoIncrement]
            public int Id { get; set; }
            public bool IsFavorite { get; set; }
        }

*   Services(dir)

    *   IFavoriteStorage.cs

    ::

        public interface IFavoriteStorage
        {
            Task CreateDatabaseAsync();
            Task InsertDatabaseAsync(Favorite favorite);
            Task<IList<Favorite>> ReadDatabaseAsync();
        }

    *   FavoriteStorage.cs

    ::

        public class FavoriteStorage : IFavoriteStorage
        {
            private SQLiteAsyncConnection connection;

            public async Task CreateDatabaseAsync()
            {
                var connectionPath =
                Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "mydb.db");
                connection = new SQLiteAsyncConnection(connectionPath);

                await connection.CreateTableAsync<Favorite>();

            }

            public async Task InsertDatabaseAsync(Favorite favorite)=>
                await connection.InsertAsync(favorite);

            public async Task<IList<Favorite>> ReadDatabaseAsync()=>
                await connection.Table<Favorite>().ToListAsync();
        }

*   ViewModels(dir)

    *   DatabasePageViewModel.cs

    ::

        public class DatabasePageViewModel : ViewModelBase
        {
            private IFavoriteStorage _favoriteStorage;

            public DatabasePageViewModel(IFavoriteStorage favoriteStorage)
            {
                _favoriteStorage = favoriteStorage;
            }


            private RelayCommand _createDatabaseCommand;
            public RelayCommand CreateDatabaseCommand =>
                _createDatabaseCommand ?? (new RelayCommand(async () => await _favoriteStorage.CreateDatabaseAsync()));



            private RelayCommand _insertDatabaseCommand;
            public RelayCommand InsertDatabaseCommand =>
                _insertDatabaseCommand ?? (new RelayCommand(async () => await _favoriteStorage.InsertDatabaseAsync(
                    new Favorite { IsFavorite=true})));



            private RelayCommand _readDatabaseCommand;
            public RelayCommand ReadDatabaseCommand =>
                _readDatabaseCommand ?? (new RelayCommand(async () =>
                    Favorites.AddRange(await _favoriteStorage.ReadDatabaseAsync())));

            public ObservableRangeCollection<Favorite> Favorites { get; }= new ObservableRangeCollection<Favorite>();
        }

    *   ViewModelLocator.cs

    ::

        public class ViewModelLocator
        {
            public ViewModelLocator() {
                SimpleIoc.Default.Register<MainPageViewModel>();    
                SimpleIoc.Default.Register<DatabasePageViewModel>();
                SimpleIoc.Default.Register<IFavoriteStorage,FavoriteStorage>();
            }
            public MainPageViewModel MainPageViewModel=>
                SimpleIoc.Default.GetInstance<MainPageViewModel>();

            public DatabasePageViewModel DatabasePageViewModel =>
                SimpleIoc.Default.GetInstance<DatabasePageViewModel>();
        }
*   App.xaml

    ::

        <Application xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:vm="clr-namespace:Mvvm.ViewModels;assembly=Mvvm"
                x:Class="Mvvm.App">

            <Application.Resources>
                <vm:ViewModelLocator x:Key="ViewModelLocator"/>
            </Application.Resources>
        </Application> 

*   App.xaml.cs

    ::

        public App()
        {
            InitializeComponent();

            MainPage = new DatabasePage();
        }

*   DatabasePage.xaml

    ::

        <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Mvvm.DatabasePage"
             BindingContext="{Binding DatabasePageViewModel , Source={StaticResource ViewModelLocator}}">
            <ContentPage.Content>
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <ScrollView Grid.Row="0">
                        <ListView x:Name="ResultListView"
                                SelectionMode="None"
                                ItemsSource="{Binding Favorites}">
                            <ListView.ItemTemplate>
                                <DataTemplate>
                                    <TextCell Text="{Binding Id}"
                                        Detail="{Binding IsFavorite}"/>
                                </DataTemplate>
                            </ListView.ItemTemplate>
                        </ListView>
                    </ScrollView>

                    <StackLayout Grid.Row="1">
                        <Button Text="create database"
                            Command="{Binding CreateDatabaseCommand}"/>
                        <Button Text="insert database"
                            Command="{Binding InsertDatabaseCommand}"/>
                        <Button Text="read database"
                            Command="{Binding ReadDatabaseCommand}"/>
                    </StackLayout>
                </Grid>
            </ContentPage.Content>
        </ContentPage>

    *   DatabasePage.xaml.cs

page.xaml->App.xaml->ViewModelLocator.cs->ViewModel.cs

ViewModel.cs->iServece.cs->Services.cs->Model.cs