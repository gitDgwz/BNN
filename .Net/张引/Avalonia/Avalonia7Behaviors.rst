Avalonia7Behaviors
============================

*   在dpa中添加Microsoft.Extensions.DependencyInjection包
*   在dpa中添加ServiceLocator.cs
*   ResultViewModel.cs中添加以下代码
    ::

        public class ResultViewModel : ViewModelBase
        {
            private readonly IPoetryStorage _poetryStorage;
        
            public ResultViewModel(IPoetryStorage poetryStorage)
            {
                _poetryStorage = poetryStorage;
                OnInitializedCommand = new AsyncRelayCommand(OnInitializedAsync);
            }

            public ObservableCollection<Poetry> PoetryCollection { get; } = new();

            public ICommand OnInitializedCommand { get; }

            public async Task OnInitializedAsync()
            {
                await _poetryStorage.InitializeAsync();
                var poetires=  await _poetryStorage.GetPoetriesAsync(Expression.Lambda<Func<Poetry, bool>>(Expression.Constant(true),
                    Expression.Parameter(typeof(Poetry), "p")), 0, int.MaxValue);
                
                foreach (var poetry in poetires)
                {
                    PoetryCollection.Add(poetry);
                }
            }

            
        }

*   在App.axaml中注册全局资源
    ::

        <Application.Resources>
            <ResourceDictionary>
                <!--local就是dpa-->
                <local:ServiceLocator x:Key="ServiceLocator"></local:ServiceLocator>
            </ResourceDictionary>
        </Application.Resources>

*   修改MainWindow.axaml
    ::

        //在上方添加
        DataContext="{Binding ResultViewModel, Source={StaticResource ServiceLocator}}"
        
        //并且删除
        x:DataType="vm:MainWindowViewModel"

*   在App.axaml.cs中修改以下代码
    ::

        //修改前
        desktop.MainWindow = new MainWindow
        {
            DataContext = new MainWindowViewModel(),
        };

        //修改后
        desktop.MainWindow = new MainWindow();

*   如果报错，要将<AvaloniaUseCompiledBindingsByDefault>false</AvaloniaUseCompiledBindingsByDefault>中间改为false
    也有可能是因为dpa和dpa.Library项目CommunityToolkit版本不同，要统一版本

*   在MainWindow.axaml下面添加
    ::

        <ItemsControl ItemsSource="{Binding PoetryCollection}">
            <ItemsControl.ItemTemplate>
                <DataTemplate>
                    <TextBlock Text="{Binding Name}"></TextBlock>
                </DataTemplate>
            </ItemsControl.ItemTemplate>
        </ItemsControl>

*   安装Avalonia.Xaml.Behaviors包
*   MainWindow.axaml添加代码
    ::

        
        <!--在上方添加两个命名空间-->
        xmlns:i="using:Avalonia.Xaml.Interactivity"
        xmlns:ia="using:Avalonia.Xaml.Interactions.Core"

        <!--在下方添加-->
        <i:Interaction.Behaviors>
            <ia:EventTriggerBehavior EventName="Initialized">
                <ia:InvokeCommandAction Command="{Binding OnInitializedAsync}"></ia:InvokeCommandAction>
            </ia:EventTriggerBehavior>
        </i:Interaction.Behaviors>

.. danger:: 

    在Avalonia中有很多运行时候的错误并不报错，只有在调试模式才报错,这是个坑

*   打开发现没有数据，调试OnInitializedAsync方法,发现有报错.

    *Unable to resolve service for type 'dpa.Library.Services.IPoetryStorage'while attempting to activate 'dpa.Library.ViewModels.ResultViewModel'*
    
    是因为没有依赖注入注册IPoetryStorage.
    
    在dpa.ServiceLocator.cs中构造函数后添加 ``serviceCollection.AddSingleton<IPoetryStorage, PoetryStorage>();``

*   打开发现还是没有数据，再次调试,发现有报错.
    
    *Unable to resolve service for type 'dpa.Library.Services.IPreferenceStorage' while attempting to activate 'dpa.Library.Services.PoetryStorage'.*

    在dpa.ServiceLocator.cs中构造函数后添加 ``serviceCollection.AddSingleton<IPreferenceStorage, FilePreferenceStorage>();``

*   再次打开，发现还是没有，调试也不出bug。原因是程序没有调用数据库初始化，所以没有数据.
    
    在ResultViewModel.cs的OnInitializedAsync方法开头添加 ``await _poetryStorage.InitializeAsync();``