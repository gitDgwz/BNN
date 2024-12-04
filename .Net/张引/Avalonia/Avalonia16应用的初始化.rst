Avalonia16应用的初始化
==========================

*   dpa Views MainView.axaml
    ::

        <UserControl xmlns="https://github.com/avaloniaui"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
                    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
                    xmlns:icon="https://github.com/projektanker/icons.avalonia"
                    xmlns:lvm="using:dpa.Library.ViewModels"
                    xmlns:lc="using:dpa.Converters"
                    xmlns:i="using:Avalonia.Xaml.Interactivity"
                    xmlns:ia="using:Avalonia.Xaml.Interactions.Core"
                    mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
                    x:Class="dpa.Views.MainView"
                    DataContext="{Binding MainViewModel , Source={StaticResource ServiceLocator}}">
            <UserControl.Resources>
                <lc:CountToBoolConverter x:Key="CountToBoolConverter"></lc:CountToBoolConverter>
            </UserControl.Resources>
            
            <SplitView
                IsPaneOpen="{Binding IsPaneOpen}">
                <SplitView.Pane>
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="50"></RowDefinition>
                            <RowDefinition Height="*"></RowDefinition>
                        </Grid.RowDefinitions>
                        
                        <Grid Row="0">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="50"></ColumnDefinition>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                            </Grid.ColumnDefinitions>
                            
                            <Button Grid.Column="0"
                                    CornerRadius="0"
                                    VerticalAlignment="Stretch"
                                    HorizontalAlignment="Stretch"
                                    Command="{Binding ClosePaneCommand}"
                                    >
                                <icon:Icon Foreground="SeaGreen"
                                        Value="fa-x">
                                </icon:Icon>
                            </Button>
                            
                            <Grid Grid.Column="1">
                                <Label Foreground="LightGreen"
                                    VerticalAlignment="Center"
                                    FontSize="20"
                                    Margin="10,0,0,0"
                                    Content="每日诗词"
                                    >
                                </Label>
                            </Grid>
                        </Grid>
                        
                        <ListBox Grid.Row="1"
                                ItemsSource="{Binding Source={x:Static lvm:MenuItem.MenuItems}}"
                                SelectedItem="{Binding SelectedMenuItem,Mode=TwoWay}">
                            
                            <i:Interaction.Behaviors>
                                <ia:EventTriggerBehavior EventName="Tapped">
                                    <ia:InvokeCommandAction
                                        Command="{Binding OnMenuTappedCommand}">
                                    </ia:InvokeCommandAction>
                                </ia:EventTriggerBehavior>
                            </i:Interaction.Behaviors>
                            
                            <ListBox.ItemTemplate>
                                <DataTemplate>
                                    <Label Content="{Binding Name}"
                                        FontSize="20"
                                        Margin="50,5,0,5">
                                    </Label>
                                </DataTemplate>
                            </ListBox.ItemTemplate>
                            
                        </ListBox>
                    </Grid> 
                </SplitView.Pane>
                
                <SplitView.Content>
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="50"></RowDefinition>
                            <RowDefinition Height="*"></RowDefinition>
                        </Grid.RowDefinitions>
                        
                        <Grid Grid.Row="0">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                                <ColumnDefinition Width="*"></ColumnDefinition>
                            </Grid.ColumnDefinitions>
                        
                        
                            <Button Grid.Column="0"
                                    Width="50"
                                    VerticalAlignment="Stretch"
                                    HorizontalAlignment="Stretch"
                                    CornerRadius="0"
                                    Command="{Binding OpenPaneCommand}"
                                    >
                                <icon:Icon Foreground="Coral"
                                        Value="fa-bars">
                                </icon:Icon>
                            </Button>
                        
                            <Button Grid.Column="1"
                                    Width="50"
                                    CornerRadius="0"
                                    HorizontalAlignment="Stretch"
                                    VerticalAlignment="Stretch"
                                    IsVisible="{Binding ContentStack.Count,Converter={StaticResource CountToBoolConverter},ConverterParameter=1}"
                                    >
                                    <!--Command="{Binding GoBackCommand}"-->
                                <icon:Icon Foreground="Coral"
                                        Value="fa-arrow-left">
                                </icon:Icon>
                            </Button>
                                
                            <Grid Grid.Column="2"
                                    Background="Beige"
                                    >
                                <Label Foreground="Peru"
                                    VerticalAlignment="Center"
                                    FontSize="28"
                                    Margin="10,0,0,0"
                                    Content="{Binding Title}">
                                </Label>
                            </Grid>
                        </Grid>
                            
                        <ContentControl Grid.Row="1"
                                        >
                                        <!--Content="{Binding }"-->
                        </ContentControl>
                    </Grid>
                </SplitView.Content>
            </SplitView>
        </UserControl>



*   dpa.Library ViewModels MainViewModel.cs
    ::

        using System.Collections.ObjectModel;
        using System.Net;
        using System.Windows.Input;
        using CommunityToolkit.Mvvm.Input;
        using dpa.Library.Services;

        namespace dpa.Library.ViewModels;

        public class MainViewModel: ViewModelBase
        {

            private readonly IMenuNavigationService _menuNavigationService;
            
            public MainViewModel(IMenuNavigationService menuNavigationService)
            {
                _menuNavigationService = menuNavigationService;
                GoBackCommand = new RelayCommand(GoBack);
                OpenPaneCommand = new RelayCommand(OpenPane);
                ClosePaneCommand = new RelayCommand(ClosePane);
                OnMenuTappedCommand = new RelayCommand(OnMenuTapped);
            }

            private string _title = "DailyPoetryA";

            public string Title
            {
                get => _title;
                private set => SetProperty(ref _title, value);
            }
            
            private bool _isPaneOpen;
            public bool IsPaneOpen
            {
                get => _isPaneOpen;
                private set => SetProperty(ref _isPaneOpen, value);
            }
            
            public ICommand OpenPaneCommand { get; }

            public void OpenPane() => IsPaneOpen = true;
            
            public ICommand ClosePaneCommand { get; }

            public void ClosePane() => IsPaneOpen = false;
            
            
            public ObservableCollection<ViewModelBase> ContentStack { get; } = [];
            
            private ViewModelBase _content;

            public ViewModelBase Content
            {
                get => _content;
                private set => SetProperty(ref _content, value);
            }

            public void PushContent(ViewModelBase content) =>
                ContentStack.Insert(0, Content = content);

            public void SetMenuAndContent(string view, ViewModelBase content)
            {
                ContentStack.Clear();
                PushContent(content);
                SelectedMenuItem =
                    MenuItem.MenuItems.FirstOrDefault(p => p.View == view);
                Title = SelectedMenuItem.Name;
                IsPaneOpen = false;
            }

            private MenuItem _selectedMenuItem;

            public MenuItem SelectedMenuItem
            {
                get => _selectedMenuItem;
                set => SetProperty(ref _selectedMenuItem, value);
            }
            
            public ICommand GoBackCommand { get; }

            public void GoBack()
            {
                if (ContentStack.Count <= 1)
                {
                    return;
                }
                
                ContentStack.RemoveAt(0);
                Content = ContentStack[0];
            }
            
            public ICommand OnMenuTappedCommand { get; }

            public void OnMenuTapped()
            {
                if (SelectedMenuItem is null)
                {
                    return;
                }

                _menuNavigationService.NavigateTo(SelectedMenuItem.View);
            }
        }

        public class MenuItem
        {
            public string View { get; private init; }
            
            public string Name { get; private init; }

            private MenuItem()
            {
                
            }

            private static MenuItem TodayView => 
                new() { Name = "今日推荐", View = MenuNavigationConstant.TodayView };
            private static MenuItem QueryView => 
                new() { Name = "诗词搜索", View = MenuNavigationConstant.QueryView };
            private static MenuItem FavoriteView => 
                new() { Name = "诗词收藏", View = MenuNavigationConstant.FavoriteView };
            
            public static IEnumerable<MenuItem> MenuItems { get; } = [TodayView, QueryView, FavoriteView];
        }
*   dpa.Library ViewModels 添加 QueryViewModel.cs和FavoriteViewModel.cs
    ::

        public class QueryViewModel: ViewModelBase
        {
            
        }

        public class FavoriteViewModel: ViewModelBase
        {
            
        }



*   dpa ServiceLocator.cs添加QueryViewModel,FavoriteViewModel的依赖注入
*   dpa.Library ViewModels 添加 InitializationViewModel.cs
    ::

       public class InitializationViewModel : ViewModelBase
        {
            private readonly IPoetryStorage _poetryStorage;

            private readonly IRootNavigationService _rootNavigationService;

            private readonly IMenuNavigationService _menuNavigationService;

            public InitializationViewModel(IPoetryStorage poetryStorage, 
                IRootNavigationService rootNavigationService, 
                IMenuNavigationService menuNavigationService)
            {
                _poetryStorage = poetryStorage;
                _rootNavigationService = rootNavigationService;
                _menuNavigationService = menuNavigationService;
                OnInitializedCommand = new AsyncRelayCommand(OnInitializedAsync);
            }
            
            private ICommand OnInitializedCommand { get; }

            public async Task OnInitializedAsync()
            {
                if (!_poetryStorage.IsInitialized)
                {
                    await _poetryStorage.InitializeAsync();
                }

                await Task.Delay(2000);
                
                _rootNavigationService.NavigateTo(RootNavigationConstant.MainView);
                _menuNavigationService.NavigateTo(MenuNavigationConstant.TodayView);
                
            }
        } 
*   dpa Services 修改RootNavigationService.cs
    ::

        public void NavigateTo(string view)
        {
            ServiceLocator.Current.MainWindowViewModel.Content = view switch
            {
                RootNavigationConstant.InitializationView => ServiceLocator.Current.InitializationViewModel,
                RootNavigationConstant.MainView => ServiceLocator.Current.MainViewModel,
                _ => throw new Exception("no view")
            };
        }

*   dpa ServiceLocator添加InitializationViewModel的依赖注入和公开变量。
*   dpa Views 添加InitializationViewModel.axaml
    ::

        <UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
             x:Class="dpa.Views.InitializationView"
             xmlns:i="using:Avalonia.Xaml.Interactivity"
             xmlns:ia="using:Avalonia.Xaml.Interactions.Core"
             DataContext="{Binding InitializationViewModel , Source={StaticResource ServiceLocator}}"
             >
            <i:Interaction.Behaviors>
                <ia:EventTriggerBehavior EventName="Initialized">
                    <ia:InvokeCommandAction Command="{Binding OnInitializedCommand}"></ia:InvokeCommandAction>
                </ia:EventTriggerBehavior>
            </i:Interaction.Behaviors>
            
            <ProgressBar Width="100"
                        Height="100"
                        IsIndeterminate="True"
                        Theme="{DynamicResource ProgressRing}"
                        ShowProgressText="False"/>
        
        </UserControl>

*   dpa.Library 添加ITodayImageService.cs
    ::

        public interface ITodayImageService
        {
            Task<TodayImage> GetTodayImageAsync();
            Task<ITodayImageServiceCheckUpdateResult> CheckUpdateAsync();
        }

        public class ITodayImageServiceCheckUpdateResult
        {
            public bool HasUpdate { get; set; }

            public TodayImage TodayImage { get; set; } = new();
            
            
            
        }

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

图片

*   dpa.Library Models 添加TodayImage.cs
    ::

        public class TodayImage
        {
            public string FullStartDate { get; set; } = string.Empty;
            
            public DateTime ExpiresAt { get; set; }

            public string Copyright { get; set; } = string.Empty;

            public string CopyrightLink { get; set; } = string.Empty;
            
            public byte[] ImageBytes { get; set; }

        }

*   dpa.Library Services 添加ITodayImageStorage.cs
    ::

        public interface ITodayImageStorage
        {
            Task<TodayImage> GetTodayImageAsync(bool isIncludingImageStream);

            Task SaveTodayImageAsync(TodayImage todayImage, bool isSavingExpiresAtOnly);
        }

*   dpa.Library Services 添加TodayImageStorage.cs
    ::

        using dpa.Library.Helpers;
        using dpa.Library.Models;

        namespace dpa.Library.Services;

        public class TodayImageStorage
        {
            private readonly IPreferenceStorage _preferenceStorage;

            public TodayImageStorage(IPreferenceStorage preferenceStorage)
            {
                _preferenceStorage = preferenceStorage;
            }

            public static readonly string FullStartDateKey = nameof(TodayImageStorage) + "." + nameof(TodayImage.FullStartDate);

            public static readonly string ExpiresAtKey = nameof(TodayImageStorage) + "." + nameof(TodayImage.ExpiresAt);

            public static readonly string CopyrightKey = nameof(TodayImageStorage) + '.' + nameof(TodayImage.Copyright);

            public static readonly string CopyrightLinkKey = nameof(TodayImageStorage) + "." + nameof(TodayImage.CopyrightLink);

            public const string FullStartDateDefault = "201901010700";

            public static readonly DateTime ExpiresAtDefault = new(2019, 1, 2);

            public const string CopyrightDefault = "salt field province vietnam work(Quangpraha/Pixabay)";
            
            public const string CopyrightLinkDefault = "https://pixabay.com/";

            public const string Filename = "todayImage.bin";

            public static readonly string TodayImagePath = PathHelper.GetLocalFfilePath(Filename);

            public async Task<TodayImage> GetTodayImageAsync(bool isIncludingImageStream)
            {
                var todayImage = new TodayImage()
                {
                    FullStartDate = _preferenceStorage.Get(FullStartDateKey, FullStartDateDefault),
                    ExpiresAt = _preferenceStorage.Get(ExpiresAtKey, ExpiresAtDefault),
                    Copyright = _preferenceStorage.Get(CopyrightKey, CopyrightDefault),
                    CopyrightLink = _preferenceStorage.Get(CopyrightLinkKey, CopyrightLinkDefault)
                };

                if (!File.Exists(TodayImagePath))
                {
                    await using var imageAssetFileStream = new FileStream(TodayImagePath, FileMode.Create) ??
                                                        throw new NullReferenceException("Null fill steam");
                    await using var imageAssetStream = typeof(TodayImageStorage).Assembly.GetManifestResourceStream(Filename) ??
                                                    throw new NullReferenceException("Null Manifest resource stram");
                    await imageAssetStream.CopyToAsync(imageAssetFileStream);
                }

                if (! isIncludingImageStream)
                {
                    return todayImage;
                }

                var imageMemoryStream = new MemoryStream();
                await using var imageFileStream = new FileStream(TodayImagePath, FileMode.Open);
                await imageFileStream.CopyToAsync(imageMemoryStream);
                todayImage.ImageBytes = imageMemoryStream.ToArray();

                return todayImage;
            }

            public async Task SaveTodayImageAsync(TodayImage todayImage,
                bool isSavingExpiresAtOnly)
            {
                _preferenceStorage.Set(ExpiresAtKey,todayImage.ExpiresAt);
                if (isSavingExpiresAtOnly)
                {
                    return;
                }

                if (todayImage.ImageBytes == null)
                {
                    throw new ArgumentException($"Null imageg bytes.",
                        nameof(todayImage));
                }
                
                _preferenceStorage.Set(FullStartDateKey,todayImage.FullStartDate);
                _preferenceStorage.Set(CopyrightKey,todayImage.Copyright);
                _preferenceStorage.Set(CopyrightLinkKey,todayImage.CopyrightLink);

                await using var imageFileStream = new FileStream(TodayImagePath, FileMode.Create);
                await imageFileStream.WriteAsync(todayImage.ImageBytes, 0, todayImage.ImageBytes.Length);
                

            }
        }


*   在dpa.Library中添加todayImage.bin图片,右键Edit改为嵌入式资源，再到csproj中修改逻辑名

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. danger:: 
    发现一个bug，sqlite3文件会在Rider编辑器打开的时候自动出现，而且不经过生成的代码
    
    因此无法debug到，我改了sqlite3的名字，让他报错，然后删除之后就会经过代码生成了，

    但是重新打开Rider又出现这个问题，我再次打断点还是不命中，如果我把代码删了它就生成不了，也就是它调用了该方法。
    经过我仔细的观察发现在项目debug开始时还没进入程序该文件就被生成。难不成是某种缓存造成的？