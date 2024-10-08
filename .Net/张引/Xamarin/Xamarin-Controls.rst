Controls
====================


Grid
-----------------------
网格
::

    #Grid网格
    <Grid>
        #网格列定义
        <Grid.ColumnDefinitions>
            #两个ColumnDefinitions说明有两列
            #Width为列宽度
            <ColumnDefinitions Width="Auto">
            <ColumnDefinitions Width="*">
        </Grid.ColumnDefinitions>
        
        #网格行定义
        <Grid.RowDefinitions>
            #一个RowDefinitions说明一行
            #Height为行高,Auto表示由组内控件大小决定,如果不添加新行,则平铺
            <RowDefinitions Height="Auto">
        </Grid.RowDefinitions>
                
                #Text为文本
        <label  Text="shomthing
                #表示此控件在行1(0开始)
                #如果没有定义控件的行和列,默认为第0行0列(第一个格子)
                Grid.Row="1"
                #此控件在列0
                Grid.Column="0"
                #横跨2个列
                Grid.ColumnSpan="2"
                #垂直居中
                VerticalOptions="Center"
                #边距为4
                Margin="4"></label>
    
    </Grid>

StackLayout
-------------------------
类似于godot的boxContainer,自动排列的容器
::

    <StackLayout Grid.Row="1"
                 Grid.Column="1"
                 #排列方向为水平,还有一种为垂直(默认)
                 Orientation="Horizontal"
                 #排列间距为0,默认不为0
                 Spacing="0">
    
        <BoxView Color="Blue"
                 #宽度设置,在垂直排列中无效
                 WidthRequest="50"
                 #高度设置
                 HeightRequest="50"/>

        <StackLayout Spacing="0">
        <BoxView Color="Red"
                 WidthRequest="50"
                 HeightRequest="25"
                 ></BoxView>
        <BoxView Color="Yellow"
                 WidthRequest="50"
                 HeightRequest="25"
                 ></BoxView>
        </StackLayout>
    </StackLayout>

ScrollView
----------------------------
类似于godot中的Scrollcontainer,超过一定大小出现滚动条
::

    <ScrollView Grid.Row="2"
                Grid.Column="1"
                HeightRequest="75"
                WidthRequest="200">
        #图片源,在UWP中可用,在android中,教学视频中可用,但是本人测试无效
        <Image Source="hw.jpg"/>

    </ScrollView>

Hyperlink
-------------------------------
超链接

xaml ::

    <Label Grid.Row="3"
           Grid.Column="1"
           Text="Click Me!"
           Margin="8"
           x:Name="ClickMeHyperlinkButton"
           TextDecorations="Underline"
       >
        #Xamarin中没有超链接,用Label完成
        <Label.GestureRecognizers>
            #Tappeded为方法名称
            <TapGestureRecognizer Tapped="ClickMeHyperlinkButton_OnTapped"></TapGestureRecognizer>
        </Label.GestureRecognizers>                     
    </Label>

cs ::

    private async void ClickMeHyperlinkButton_OnTapped(object sender, EventArgs e)
    {
        await Browser.OpenAsync("https://learn.microsoft.com/zh-cn/");
    }
    
Pop-up
-------------------------------
弹出按钮

xaml ::

        <Button x:Name="MyPopupButton"
        Margin="8"
        Grid.Column="1"
        Grid.Row="4"
        Text="Show Pop-up"
        Clicked="MyPopupButton_Clicked"></Button>

cs ::

        private void MyPopupButton_Clicked(object sender, EventArgs e)
        {
            DisplayAlert("Greetings!", "you hava clicked me","ok");
        } 

Switch
-------------------------------

::

    <Switch Grid.Row="5"
        Grid.Column="1"
        #设置从左排列,默认为右边
        HorizontalOptions="Start"
        Margin="8"></Switch>

Slider
-------------------------------

::

    <StackLayout Grid.Row="6"
                 Grid.Column="1">
        
        <Slider Margin="8"
                Maximum="100"
                Minimum="0"
                #在水平排列中无效,必须使用HorizontalOptions设置才能起效
                #可能HorizontalOptions默认值为Fill,所以宽度被覆盖了
                WidthRequest="200"
                HorizontalOptions="Start"
                x:Name="MySlider"></Slider>

                #数据绑定
        <Label  Text="{Binding Value}"
                BindingContext="{x:Reference MySlider}"
                Margin="8"/>
    </StackLayout>
