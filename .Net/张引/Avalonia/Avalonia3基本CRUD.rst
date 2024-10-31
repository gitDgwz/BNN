Avalonia3基本CRUD
=========================

*   修改MainWindowViewModel.cs
    ::

        public partial class MainWindowViewModel : ViewModelBase
        {
            private readonly IPoetryStorage _poetryStorage;
            
            public MainWindowViewModel(IPoetryStorage poetryStorage)
            {
                _poetryStorage = poetryStorage;
                SayHelloCommand = new RelayCommand(SayHello);
                //本节新添加***********************************
                InitializeCommand = new AsyncRelayCommand(InitializeAsync);
                InsertCommand = new AsyncRelayCommand(InsertAsync);
                ListCommand = new AsyncRelayCommand(ListAsync);
                //**********************************************
            }

            private string _message;

            public string Message
            {
                get => _message;
                set => SetProperty(ref _message, value); 
            }

            public void SayHello()=>
                Message = "Hello,Avalonia!";
            
            public ICommand SayHelloCommand { get; }
            
            //本节新添加***********************************

            public async Task InitializeAsync()
            {
                await _poetryStorage.InitializeAsync();
            }
            
            public ICommand InitializeCommand { get; }


            public async Task InsertAsync()=>
                await _poetryStorage.InsertAsync(new Poetry { Name = "Name" + new Random().Next() });
            
            public ICommand InsertCommand { get; }


            public ObservableCollection<Poetry> Poetries { get; set; } = new();

            public async Task ListAsync()
            {
                var poetries = await _poetryStorage.ListAsycn();
                Poetries.Clear();
                foreach (var poetry in poetries)
                {
                    Poetries.Add(poetry);
                }
            }
            
            public ICommand ListCommand { get; }
            
            //**********************************************
        }

*   修改MainWindow.axaml
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
                DataContext="{Binding MainWindowViewModel , Source={StaticResource ServiceLocator}}"
                >

            <StackPanel>
                <TextBlock Text="{Binding Message}" HorizontalAlignment="Center" VerticalAlignment="Center"/>
                <Button Content="Click me!"
                        Command="{Binding SayHelloCommand}"></Button>
                <!--本节新添加-->
                <Button Content="Initialize"
                        Command="{Binding InitializeCommand}"></Button>
                <Button Content="Insert"
                        Command="{Binding InsertCommand}"></Button>
                <Button Content="List"
                        Command="{Binding ListCommand}"></Button>
                <!--专门用来显示列表数据的-->
                <ItemsControl ItemsSource="{Binding   Poetries}">
                        <!--里面每个单独元素的显示-->
                        <ItemsControl.ItemTemplate>
                                <DataTemplate>
                                        <!--直接绑定单独元素的属性-->
                                        <TextBlock Text="{Binding Name}"></TextBlock>
                                </DataTemplate>
                        </ItemsControl.ItemTemplate>
                </ItemsControl>
                
                <!------------>
            </StackPanel>
            

        </Window>
