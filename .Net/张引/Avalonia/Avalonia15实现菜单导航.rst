Avalonia15实现菜单导航
========================

*   完善dpa MainView.axaml
    ::

        <UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             xmlns:icon="https://github.com/projektanker/icons.avalonia"
             xmlns:lvm="using:dpa.Library.ViewModels"
             xmlns:lc="using:dpa.Converters"
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

*   dpa.Services MainViewModel
    ::

        public class MainViewModel: ViewModelBase
        {
            public MainViewModel()
            {
                GoBackCommand = new RelayCommand(GoBack);
                OpenPaneCommand = new RelayCommand(OpenPane);
                ClosePaneCommand = new RelayCommand(ClosePane);
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
                new() { Name = "诗词搜索", View = MenuNavigationConstant.FavoriteView };
            
            public static IEnumerable<MenuItem> MenuItems { get; } = [TodayView, QueryView, FavoriteView];
        }

*   dpa Services 添加 MenuNavigationService.cs
    ::

        public class MenuNavigationService: IMenuNavigationService
        {
            public void NavigateTo(string view)
            {
                ViewModelBase viewModel = view switch
                {
                    MenuNavigationConstant.TodayView => ServiceLocator.Current.TodayViewModel,
                    _ => throw new Exception("some view we dont know ")
                };
                ServiceLocator.Current.MainViewModel.SetMenuAndContent(view,viewModel);
            }
        }

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

switch表达式,有返回值
::

    public void NavigateTo(string view)
    {
        ViewModelBase viewModel = view switch
        {
            MenuNavigationConstant.TodayView => ServiceLocator.Current.TodayViewModel,
            _ => throw new Exception("some view we dont know ")
        };
        ServiceLocator.Current.MainViewModel.SetMenuAndContent(view,viewModel);
    }