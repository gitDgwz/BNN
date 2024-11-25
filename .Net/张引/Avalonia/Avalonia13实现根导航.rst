Avalonia13实现根导航
=============================

*   dpa.Library ViewModels中创建MainWindowViewModel.cs 并且删除原来项目dpa中带的同名cs
    ::

        public class MainWindowViewModel: ViewModelBase
        {
            private ViewModelBase _content;
            
            
            public ViewModelBase Content
            {
                get => _content;
                set => SetProperty(ref _content, value);
            }

        }

*   dpa ServiceLocator中添加
    ::

        public MainWindowViewModel MainWindowViewModel =>
            _serviceProvider.GetRequiredService<MainWindowViewModel>();


        //ServiceLocator构造器里面
        serviceCollection.AddSingleton<MainWindowViewModel>();
        //下面的步骤会有
        serviceCollection.AddSingleton<IRootNavigationService, RootNavigationService>();

*   MainWindow.axaml中　Control移动到另一个文件（下面的 ``ResultView`` ）,并且修改绑定为 
    ``MainWindowViewModel`` , 添加Content绑定.
    ::

        <Window xmlns="https://github.com/avaloniaui"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            
            xmlns:i="using:Avalonia.Xaml.Interactivity"
            xmlns:ia="using:Avalonia.Xaml.Interactions.Core"
            xmlns:ais="using:AvaloniaInfiniteScrolling"
            
            mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
            x:Class="dpa.Views.MainWindow"
            Icon="/Assets/avalonia-logo.ico"
            Title="dpa"
            DataContext="{Binding MainWindowViewModel, Source={StaticResource ServiceLocator}}"
            Content="{Binding Content }"
            >
        
            <i:Interaction.Behaviors>
                    <ia:EventTriggerBehavior EventName="Initialized">
                            <ia:InvokeCommandAction Command="{Binding OnInitializedCommand}"></ia:InvokeCommandAction>
                    </ia:EventTriggerBehavior>
            </i:Interaction.Behaviors>

        </Window>


*   dpa Services RootNavigationService.cs
    ::

        public class RootNavigationService: IRootNavigationService
        {
            public void NavigateTo(string view)
            {
                if (view == nameof(TodayViewModel))
                {
                    ServiceLocator.Current.MainWindowViewModel.Content =
                        ServiceLocator.Current.TodayViewModel;
                }
            }
        }

*   rider File->Settings->Plugins安装 ``AvaloniaRider`` 
   
*   在dpa Views中添加 ``Avalonia User Control`` 名字为　``TodayView``
    ::

        <UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             xmlns:i="using:Avalonia.Xaml.Interactivity"
             xmlns:ia="using:Avalonia.Xaml.Interactions.Core"
             mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
             x:Class="dpa.Views.TodayView"
             DataContext="{Binding  TodayViewModel ,Source={StaticResource ServiceLocator}}"
             >
    
            <i:Interaction.Behaviors>
                <ia:EventTriggerBehavior EventName="Initialized">
                    <ia:InvokeCommandAction Command="{Binding OnInitializedCommand}"></ia:InvokeCommandAction>
                </ia:EventTriggerBehavior>
            </i:Interaction.Behaviors>
        
    
            <Grid Background="Black">
                    <Grid.RowDefinitions>
                            <RowDefinition Height="*" />
                    </Grid.RowDefinitions>
                    
                    <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    
                    <StackPanel Background="Gray"
                                VerticalAlignment="Bottom">
                            <StackPanel Margin="8"
                                        IsVisible="{Binding IsLoading}">
                                    <ProgressBar Foreground="White"
                                                 IsIndeterminate="{Binding IsLoading}"
                                                 Width="50"
                                                 Height="50"
                                                 Margin="0,0,0,8"
                                                 Theme="{DynamicResource progressRing}"
                                                 ShowProgressText="False"/>
                            </StackPanel>
                            <StackPanel Margin="8"
                                        IsVisible="{Binding IsLoading,
                                        Converter={StaticResource NegativeConverter}}">
                                    <Label FontSize="28"
                                           Foreground="Wheat"
                                           Margin="0,0,0,8"
                                           Content="{Binding TodayPoetry.Snippet}"/>
                                    <StackPanel x:Name="DetailStackLayout">
                                            <Label FontSize="18"
                                                   Foreground="White"
                                                   Margin="0,0,8,8"
                                                   Content="{Binding TodayPoetry.Author}"
                                                   VerticalAlignment="Center"/>
                                            <Label FontSize="18"
                                                   Foreground="White"
                                                   Margin="0,0,8,8"
                                                   Content="{Binding TodayPoetry.Name}"
                                                   VerticalAlignment="Center"/>
                                            <Button HorizontalAlignment="Left"
                                                   Margin="0,0,8,8"
                                                   Content="查看详细"
                                                   Command="{Binding ShowDetailCommand}"
                                                   VerticalAlignment="Center"/>
                                    </StackPanel>
                            </StackPanel>
                            
                            <StackPanel Background="Gray">
                                    <Label Foreground="White"
                                           Margin="8"
                                           FontSize="12"
                                           Content="{Binding TodayImage.Copyright}"/>
                            </StackPanel>
                    </StackPanel>
            </Grid>
        
        </UserControl>


*   删除dpa中 ViewModels 中的ViewModelBase.cs(同名).将 dpa ViewModels文件夹直接删掉.

*   在dpa ViewLocator.cs中修改一行
    ::
        
        //        var name = data.GetType().FullName!.Replace("ViewModel", "View", StringComparison.Ordinal)
        var name = data.GetType().FullName!.Replace("ViewModel", "View", StringComparison.Ordinal);
            .Replace("dpa.Library", "dpa");

*   安装 ``Projektanker.Icons.Avalonia.FontAwesome`` 9.4.2版本
*   dpa Views创建 Avalonia user control 名为"MainView"
    ::

        <UserControl xmlns="https://github.com/avaloniaui"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
                    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
                    xmlns:icon="https://github.com/projektanker/icons.avalonia"
                    mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
                    x:Class="dpa.Views.MainView">
            <SplitView>
                <SplitView.Pane>
                    
                </SplitView.Pane>
                <SplitView.Content>
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="50"></RowDefinition>
                            <RowDefinition Height="*"></RowDefinition>
                        </Grid.RowDefinitions>
                        
                        <Grid Row="0">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                                <ColumnDefinition Width="*"></ColumnDefinition>
                            </Grid.ColumnDefinitions>
                            
                            <Button Grid.Column="0"
                                    Width="50"
                                    HorizontalAlignment="Stretch"
                                    Command="{Binding OpenPaneCommand}"
                                    >
                                <icon:Icon Foreground="Coral"
                                        Value="fa-bars">
                                </icon:Icon>
                            </Button>
                            
                            <Button Grid.Column="1"
                                    Width="50"
                                    HorizontalAlignment="Stretch"
                                    Command="{Binding OpenPaneCommand}"
                                    IsVisible="True"
                                    >
                                <icon:Icon Foreground="Coral"
                                        Value="fa-arrow">
                                </icon:Icon>
                            </Button>
                            
                            <Grid Grid.Column="2"
                                    Background="Peru"
                                    >
                                <Label Foreground="Aqua"
                                    VerticalAlignment="Center"
                                    FontSize="28"
                                    Margin="10,0,0,0"
                                    Content="{}">
                                </Label>
                            </Grid>
                        </Grid>
                    </Grid>
                </SplitView.Content>
            </SplitView>
        </UserControl>



*   在dpa Program.cs中修改一句话
    ::

        //修改前
        public static AppBuilder BuildAvaloniaApp()
            =>  AppBuilder.Configure<App>()
                .UsePlatformDetect()
                .WithInterFont()
                .LogToTrace();
        
        //修改后
        public static AppBuilder BuildAvaloniaApp()
        {
            IconProvider.Current.Register<FontAwesomeIconProvider>();
            return AppBuilder.Configure<App>()
                .UsePlatformDetect()
                .WithInterFont()
                .LogToTrace();
        }

        
