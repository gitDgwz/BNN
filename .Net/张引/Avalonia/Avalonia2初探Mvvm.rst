Avalonia2初探Mvvm
=====================
本节完成view和viewmodel的数据绑定，locator的数据导航，以及依赖注入

#.  导入包 ``Microsoft.Extensions.DependencyInjection`` -依赖注入包
#.  修改MainWindowViewModel.cs
    ::

        public partial class MainWindowViewModel : ViewModelBase
        {

            private readonly IPoetryStorage _poetryStorage;
            //构造函数添加了参数   
            public MainWindowViewModel(IPoetryStorage poetryStorage)
            {
                _poetryStorage = poetryStorage;
                SayHelloCommand = new RelayCommand(SayHello); 
            }

            private string _message;

            public string Message
            {
                get => _message;
                set => SetProperty(ref _message, value); 
            }

            public void SayHello()
            {
                Message = "Hello,Avalonia!";
            }
            
            public ICommand SayHelloCommand { get; }
        }

#.  在根目录添加文件ServiceLocator.cs
    ::

        public class ServiceLocator
        {
            
            private readonly IServiceProvider _serviceProvider;

            public ServiceLocator()
            {
                var serviceCollection = new ServiceCollection();

                serviceCollection.AddSingleton<MainWindowViewModel>();
                serviceCollection.AddSingleton<IPoetryStorage, PoetryStorage>();

                _serviceProvider = serviceCollection.BuildServiceProvider();
            }

            public MainWindowViewModel MainWindowViewModel=>
                _serviceProvider.GetService<MainWindowViewModel>();
            
            
        }

#.  修改App.axaml
    ::

        <Application xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="Mvvm.App"
             xmlns:local="using:Mvvm"
             RequestedThemeVariant="Default">
             <!-- "Default" ThemeVariant follows system theme variant. "Dark" or "Light" are other available options. -->

            <!--添加ServiceLocator为全局资源,x:key指它的名字-->
            <Application.Resources>
                <ResourceDictionary>
                    <local:ServiceLocator x:Key="ServiceLocator"></local:ServiceLocator>
                </ResourceDictionary>
            </Application.Resources>

            <Application.DataTemplates>
                <local:ViewLocator/>
            </Application.DataTemplates>
        
            <Application.Styles>
                <FluentTheme />
            </Application.Styles>
        
        </Application>

#.  修改MainWindow.axaml
    ::

        <Window xmlns="https://github.com/avaloniaui"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:vm="using:Mvvm.ViewModels"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
            x:Class="Mvvm.Views.MainWindow"
            x:DataType="vm:MainWindowViewModel"
            Icon="/Assets/avalonia-logo.ico"
            Title="Mvvm"
            <!--绑定全局资源ServiceLocator里面的MainWindowViewModel-->
            DataContext="{Binding MainWindowViewModel , Source={StaticResource ServiceLocator}}"
            >

        <!--去除原本的标记，改为以下代码,分别绑定了MainWindowViewModel的Message和SayHelloCommand-->
        <StackPanel>
            <TextBlock Text="{Binding Message}" HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <Button Content="Click me!"
                    Command="{Binding SayHelloCommand}"></Button>
        </StackPanel>
        

        </Window>


#.  App.axaml.cs 注释掉 ``DataContext = new MainWindowViewModel()``,改为依赖注入

#.  右键Mvvm项目，edit，编辑Mvvm.csproj.将
    ``<AvaloniaUseCompiledBindingsByDefault>true</AvaloniaUseCompiledBindingsByDefault>``
    改为false
    ``<AvaloniaUseCompiledBindingsByDefault>false</AvaloniaUseCompiledBindingsByDefault>``