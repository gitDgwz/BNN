FightChess
==================
飞行棋




Random
-----------

伪随机数生成器,不要在短时间内（循环中）生成多个Random实例，因为其实是基于算法的伪随机，会导致得到的数相同.

Random.Next(1,10) 1-9，右边开区间.


Code
----------------

ViewModel.cs
::

    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Globalization;
    using System.Runtime.CompilerServices;
    using System.Windows.Data;
    using System.Windows.Input;

    namespace FightChess;

    public class ViewModel :ObserveObject
    {
        private int[] _mapData=new int[100];
        
        Random random = new Random();

        private int _horizontal;

        private int _aPos = 0;

        private List<int> _wormHoles = new List<int>();

        private int _rounds = 1;
        public int Rounds
        {
            get => _rounds;
            set
            {
                _rounds = value;
                OnPropertyChanged();
            }
        }
        
        public ObservableCollection<string> ActionStack { get; } = new();
        

        private bool _isOver = true;
        public bool IsOver
        {
            get => _isOver;
            set
            {
                _isOver = value;
                OnPropertyChanged();
            }
        }
        
        private string _log = "";
        public string Log
        {
            get => _log;
            set
            {
                _log = value;
                OnPropertyChanged();
            }
        }
        
        private string _message = "";
        public string Message
        {
            get => _message;
            set
            {
                _message = value;
                OnPropertyChanged();
            }
        }
        
        public int APos
        {
            get => _aPos;
            set
            {
                _aPos = value;
                if (_aPos<=0)
                {
                    _aPos = 0;
                }
                if (_aPos>=99)
                {
                    _aPos = 99;
                }
                OnPropertyChanged();
            }
        }
        
        private int _bPos = 0;

        public int BPos
        {
            get => _bPos;
            set
            {
                _bPos = value;
                if (_bPos<=0)
                {
                    _bPos = 0;
                }
                if (_bPos>=99)
                {
                    _bPos = 99;
                }
                OnPropertyChanged();
            }
        }
        
        int i=0;
        int x=0;
        public int Horizontal
        {
            get => _horizontal;
            set
            {
                _horizontal = value;
                OnPropertyChanged();
            }
        }
        
        private int _vectical;
        public int Vectical
        {
            get => _vectical;
            set
            {
                _vectical = value;
                OnPropertyChanged();
            }
        }
        
        private string _map="";
        public string Map
        {
            get=>_map;
            set
            {
                _map = value;
                OnPropertyChanged();
            }
        }
        
        
        public RelayCommand StartOverCommand { get; set; } 
        public RelayCommand NextCommand { get; set; }
        
        public ViewModel()
        {
            StartOverCommand = new RelayCommand(StartOver);
            NextCommand = new RelayCommand(Next);
            StartOver();
        }

        private void Next(object who)
        {
            _message =" ";
            string w=(string)who;
            int n = random.Next(1, 7);
            
            if (w == "A")
            {
                APos += n;
                SendMessage(w,$"向前走了{n}步");
                
                GetSomething(APos,w);
                
                ActionStack.RemoveAt(0);
            }
            else
            {
                BPos += n;
                SendMessage(w,$"向前走了{n}步");
                
                GetSomething(BPos,w);
                ActionStack.RemoveAt(0);
            }

            if (Rounds%2==0)
            {
                ActionStack.Add("A");
                Rounds++;
            }
            else
            {
                ActionStack.Add("B");
                Rounds++;
            }
            
            MapDraw();

        }

        private void GetSomething(int APosOrBPos,string who)
        {
            if (who=="A")
            {
                if (_mapData[APosOrBPos] == 0)
                {

                }
                else if(_mapData[APosOrBPos]==4)
                {
                    SendMessage(who,"被人打进医院");
                    ActionStack[2]="B";
                    Rounds++;
                }
                else if(_mapData[APosOrBPos]==1)
                {
                    APos += 6;
                    SendMessage(who,"坐小汽车往前走了6步");
                    GetSomething(APos,who);
                }
                else if(_mapData[APosOrBPos]==2)
                {
                    APos -= 6;
                    SendMessage(who,"踩到炸弹后退了6步");
                    GetSomething(APos,who);
                }
                else if(_mapData[APosOrBPos]==3)
                {
                    int index = _wormHoles.IndexOf(APosOrBPos);
                    if (index==_wormHoles.Count-1)
                    {
                        SendMessage(who,$"进入了第{index+1}个虫洞，什么都没发生");
                        return;
                    }
                    APos=_wormHoles[index+1];
                    SendMessage(who,$"进入了第{index+1}个虫洞，传送到了下一个地方");
                }
                else if(_mapData[APosOrBPos]==5)
                {
                    SendMessage(who,"赢了");
                }
                
            }
            else
            {
                if (_mapData[APosOrBPos] == 0)
                {

                }
                else if (_mapData[APosOrBPos] == 4)
                {
                    SendMessage(who, "被人打进医院");
                    ActionStack[2]="A";
                    Rounds++;
                }
                else if (_mapData[APosOrBPos] == 1)
                {
                    BPos += 6;
                    SendMessage(who, "坐小汽车往前走了6步");
                    GetSomething(BPos, who);
                }
                else if (_mapData[APosOrBPos] == 2)
                {
                    BPos -= 6;
                    SendMessage(who, "踩到炸弹后退了6步");
                    GetSomething(BPos, who);
                }
                else if (_mapData[APosOrBPos] == 3)
                {
                    int index = _wormHoles.IndexOf(APosOrBPos);
                    if (index == _wormHoles.Count - 1)
                    {
                        SendMessage(who, $"进入了第{index+1}个虫洞，什么都没发生");
                        return;
                    }

                    BPos = _wormHoles[index + 1];
                    SendMessage(who, $"进入了第{index+1}个虫洞，传送到了下一个地方");
                }
                else if (_mapData[APosOrBPos] == 5)
                {
                    SendMessage(who, "赢了");
                }
            }
        }

        private void SendMessage(string who,string mes)
        {
            Message +=who+":"+ mes + "\n";
            Log +=who+":"+ mes + "\n";
        }
        private void StartOver()
        {
            Map = "";
            Horizontal =random.Next(18, 25);
            Vectical = random.Next(3, 6);
            MapInit();
            MapDraw();
        }
        
        
        private  void MapInit()
        {
            int normal = 0;
            int Jump = 1;
            int boom = 2;
            int wormhole = 3;
            int stop = 4;
            int win = 5;
            APos = 0;
            BPos = 0;
            _wormHoles.Clear();
            Log = "";
            
            ActionStack.Clear();
            ActionStack.Add("A");
            ActionStack.Add("B");
            ActionStack.Add("A");

            
            for (int l=0;l<_mapData.Length;l++)
            { 
                int r=random.Next(1, 100);
                if (r<=80)
                {
                    _mapData[l] = normal;
                }
                else if (80<r&&r<=88)
                {
                    _mapData[l] = Jump;
                }
                else if (88<r&&r<=90)
                {
                    _mapData[l] = boom;
                }
                else if (90<r&&r<=96)
                {
                    _mapData[l] = wormhole;
                    _wormHoles.Add(l);
                }
                else if (96<r&&r<=99)
                {
                    _mapData[l] = stop;
                }
            }
            _mapData[99] = 5;
        }

        private async Task MapDraw()
        {
            Map = "";
            i=0;
            x=0;
            while (i<=99)
            {
                await DrawRight();
                await DrawDownRight();
                await DrawLeft();
                await DrawDownleft();
            }
        }

        private async Task DrawRight()
        {
            for ( ;i<x+Horizontal&&i<=99;i++)
            {
                //await Task.Delay(10);
                
                SwitchAndDraw(_mapData[i]);
                
            }
            Map += "\n";
            x = i;
            
        }

        private async Task DrawDownRight()
        {
            for ( ;i<x+Vectical&&i<=99;i++)
            {
                for (int j = 0; j < Horizontal-1; j++)
                {
                    Map += "🏔";
                }
                //await Task.Delay(10);
                
                SwitchAndDraw(_mapData[i]);
                Map += "\n";
            }

            x = i;
        }
        private async Task DrawDownleft()
        {
            for ( ;i<x+Vectical&&i<=99;i++)
            {
                //await Task.Delay(10);
                
                SwitchAndDraw(_mapData[i]);
                
                for (int j = 0; j < Horizontal-1; j++)
                {
                    Map += "🏔";
                }
                
                Map += "\n";
            }

            x = i;
        }
        private async Task DrawLeft()
        {
            if (i == 100) return;
            int newI = i + Horizontal-1;
            
            if (newI > 99)
            {
                int differ = newI - 99;
                for ( int d = 0; d <differ; d++)
                {
                    Map += "🏝";
                }
                i = 99;
            }
            else
            {
                i = newI;
            }
            
            for (;i>=x&&i<=99;i--)
            { 
                //await Task.Delay(10);
                SwitchAndDraw(_mapData[i]);
            }
            Map += "\n";
            i = x + Horizontal;
            x = i;
        }

        private void SwitchAndDraw(int mapData)
        {
            if (APos==BPos&&APos==i)
            {
                Map+="🆎";
            }
            else if(i==APos)
            {
                Map+="🅰";
            }
            else if(i==BPos)
            {
                Map+="🅱"; 
            }
            else if (mapData==0)
            {
                Map += "🛑";
            }
            else if(mapData==1)
            {
                Map +="🚕";
            }
            else if(mapData==2)
            {
                Map += "🤡";
            }
            else if(mapData==3)
            {
                Map += "️🌌";
            }
            else if(mapData==4)
            {
                Map += "🏥";
            }
            else if(mapData==5)
            {
                Map += "🏴";
            }

        }
    }



    public class ListStringJoinConverter : IValueConverter
    {
        public object? Convert(object? value, Type targetType, object? parameter, CultureInfo culture)
        {
            ObservableCollection<string>? list = value as ObservableCollection<string>;
            string joined = string.Join(",",list);
            return joined;
        }

        public object? ConvertBack(object? value, Type targetType, object? parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }

    public class RelayCommand: ICommand
    {
        private Action _action;
        private Action<object> _actionInt;
        public RelayCommand(Action action)
        {
            _action = action;
        }
        public RelayCommand(Action<object> actionInt)
        {
            _actionInt = actionInt;
        }
        
        public bool CanExecute(object? parameter)
        {
            return true;
        }

        public void Execute(object? parameter)
        {
            _action?.Invoke();
            _actionInt?.Invoke(parameter);
        }

        public event EventHandler? CanExecuteChanged;
    }

    public class ObserveObject: INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler? PropertyChanged;

        protected virtual void OnPropertyChanged([CallerMemberName] string? propertyName = null)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }

        protected bool SetField<T>(ref T field, T value, [CallerMemberName] string? propertyName = null)
        {
            if (EqualityComparer<T>.Default.Equals(field, value)) return false;
            field = value;
            OnPropertyChanged(propertyName);
            return true;
        }
    }


XAML
::

    <Window x:Class="FightChess.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:FightChess"
        mc:Ignorable="d"
        Title="MainWindow" Height="850" Width="1250">
        
        <Window.DataContext>
            <local:ViewModel></local:ViewModel>
        </Window.DataContext>
        <Window.Resources>
            <local:ListStringJoinConverter x:Key="ListStringJoinConverter"></local:ListStringJoinConverter>
        </Window.Resources>
        
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="*"></RowDefinition>
                <RowDefinition Height="*"></RowDefinition>
                <RowDefinition Height="*"></RowDefinition>
            </Grid.RowDefinitions>
            
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"></ColumnDefinition>
                <ColumnDefinition Width="*"></ColumnDefinition>
                <ColumnDefinition Width="*"></ColumnDefinition>

            </Grid.ColumnDefinitions>
            
            <TextBlock Grid.RowSpan="3" Grid.ColumnSpan="2" Margin="8"
                    Text="{Binding Map}"  FontSize="24"></TextBlock>
            
            <Grid Grid.Row="0" Grid.Column="2" Grid.RowSpan="2" Margin="8" Background="Snow">
                
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"></RowDefinition>
                    <RowDefinition Height="*"></RowDefinition>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*"></ColumnDefinition>
                </Grid.ColumnDefinitions>
                
                <Grid Grid.Row="0" Grid.Column="0" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"></RowDefinition>
                        <RowDefinition Height="Auto"></RowDefinition>

                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="*"></ColumnDefinition>
                        <ColumnDefinition Width="*"></ColumnDefinition>
                        <ColumnDefinition Width="*"></ColumnDefinition>
                        <ColumnDefinition Width="*"></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    
                    <Button Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="4"
                            Content="重新开始"
                            Command="{Binding   StartOverCommand}"></Button>
                
                    <TextBlock Grid.Row="1" Grid.Column="0" Text="行" VerticalAlignment="Center"></TextBlock>
                    <TextBlock Grid.Row="1" Grid.Column="1" Text="{Binding Horizontal}" VerticalAlignment="Center"></TextBlock>
                    <TextBlock Grid.Row="1" Grid.Column="2" Text="竖" VerticalAlignment="Center"></TextBlock>
                    <TextBlock Grid.Row="1" Grid.Column="3" Text="{Binding Vectical}" VerticalAlignment="Center"></TextBlock>
                    
                </Grid>
                
                <ScrollViewer  Grid.Row="1" Grid.Column="0" >
                    <TextBlock Text="{Binding Log}"></TextBlock>
                </ScrollViewer>
                
            </Grid>
            
            <Grid Grid.Row="2" Grid.Column="2"  Margin="8" Background="Lavender" >
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"></RowDefinition>
                    <RowDefinition Height="*"></RowDefinition>
                    <RowDefinition Height="Auto"></RowDefinition>
                </Grid.RowDefinitions>
                <!--
                <TextBlock Grid.Row="0" Text="{Binding ActionStack,Converter={StaticResource ListStringJoinConverter}}"></TextBlock>
                -->
                <ListBox Grid.Row="0"
                        ItemsSource="{Binding ActionStack}">
                </ListBox>
                
                <TextBlock Grid.Row="1" Text="{Binding Message}"></TextBlock>
                <Button Grid.Row="2" Content="行动"
                        Command="{Binding NextCommand}"
                        CommandParameter="{Binding ActionStack[0]}"
                        IsEnabled="{Binding !IsOver}"></Button>
            </Grid>
        </Grid>
    
    </Window>
