Avalonia12导航的原理
===============================
.. danger:: 
    升级Avalonia的包(带有Avalonia图标的)统一到11.1.4,要不然打开会使透明的窗口.

*   dpa安装 ``Irihi.Ursa`` , ``Irihi.Ursa.Themes.Semi``
*   dpa App.axaml中上面添加 ``xmlns:u-semi="https://irihi.tech/ursa/themes/semi"``
    在 ``<Application.Styles>`` 标签内添加 ``<u-semi:SemiTheme Locale="zh-CN"/>``
*   在AlertService中导入库，并且修改
    ::

        public class AlertService: IAlertService
        {
            public async Task AlertAsync(string title, string message)
            {
                await MessageBox.ShowAsync(message,title,button: MessageBoxButton.OK);
            }
        }

*   dpa ServiceLocator 添加
    ::

        public TodayViewModel TodayViewModel => _serviceProvider.GetRequiredService<TodayViewModel>();

*   dpa MainWindows.axaml修改
    ::

        DataContext="{Binding TodayViewModel, Source={StaticResource ServiceLocator}}"
        >
    
        <i:Interaction.Behaviors>
                <ia:EventTriggerBehavior EventName="Initialized">
                        <ia:InvokeCommandAction Command="{Binding OnInitializedCommand}"></ia:InvokeCommandAction>
                </ia:EventTriggerBehavior>
        </i:Interaction.Behaviors>

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

这里讲了许多Avalonia导航的原理:

是基于字符串修改反射的跳转方式

ViewLocator是一个IDataTemplate，App.xamal中注册了全局的ViewLocator,
在view中更改Content就会触发ViewLocator中的Match只要是ViewModelBase,
就会进入Build方法,Build方法会根据你的ViewModel返回一个自带该ViewModel的View，然后显示页面.

*   dpa.Library Services中创建IRootNavigationService
    ::

        public interface IRootNavigationService
        {
            void NavigateTo(string view);
        }

        public static class RootNavigationConstant
        {
            public const string InitializationView = nameof(InitializationView);

            public const string MainView = nameof(MainView);
        }

*   dpa Services中 创建RootNavigationService
    ::

        public class RootNavigationService: IRootNavigationService
        {
            public void NavigateTo(string view)
            {
                throw new System.NotImplementedException();
            }
        }

*   dpa Views创建 Avalonia user control 名为"MainView",安装 ``Projektanker.Icons.Avalonia.FontAwesome`` 9.4.2版本
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