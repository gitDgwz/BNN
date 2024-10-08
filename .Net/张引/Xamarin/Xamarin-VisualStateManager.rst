visualStateManager
---------------------------

xaml ::

            <StackLayout Grid.Row="0"
                     Grid.Column="1"
                     Margin="8"
                     x:Name="BoxStackLayout">
                #视觉状态管理器
                <VisualStateManager.VisualStateGroups>
                #状态组
                <VisualStateGroup>
                    #状态Portrait
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            #设置名为Orientation的属性值为Vertical
                            <Setter Property="Orientation"
                                        Value="Vertical"/>
                        </VisualState.Setters>
                    </VisualState>
                    #状态Landscape
                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            #设置名为Orientation的属性值为Horizontal
                            <Setter Property="Orientation"
                                            Value="Horizontal">
                            </Setter>
                        </VisualState.Setters>
                    </VisualState>

                </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

            </StackLayout>

cs ::

        public MainPage()
        {
            InitializeComponent();
            #信号,当视图大小改变
            SizeChanged += (sender, args) =>
            {
                #将BoxStackLayout组件的状态为改为Landscape或者Portrait
                VisualStateManager.GoToState(BoxStackLayout,
                    Width > 600 ? "Landscape" : "Portrait");
            };

        }  

listView
--------------------------------

xaml ::

        <ListView Grid.Row="1"
                  Grid.Column="1"
                  Margin="8"
                  x:Name="PoetryListView"
                  #无法被选中
                  SelectionMode="None"
                  #点击事件方法
                  ItemTapped="PoetryListView_ItemTapped">
                  
            #listView的显示模板
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Name}"
                              Detail="{Binding Content}"/>
                </DataTemplate>
            </ListView.ItemTemplate>

        </ListView>        

cs ::

    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();

            #制造重复数据
            var poetryList=new List<Poetry>() 
            { new Poetry { Name="Nmae 1",Content="content1"},
            new Poetry { Name="Nmae 2",Content="content2"},
            new Poetry { Name="Nmae 3",Content="content3"},};

            #将xaml中PoetryListView数据赋值为poetryList
            PoetryListView.ItemsSource = poetryList;
        }

        #定义一个类
        public class Poetry
        {
            public String Name { get; set; }

            public String Content { get; set; }
        }

        #listView的点击事件
        private void PoetryListView_ItemTapped(object sender, ItemTappedEventArgs e)
        {
            DisplayAlert("Item Tapped!", "You tapped" + (e.Item as Poetry)?.Name, "ok");
                
        }
    }        