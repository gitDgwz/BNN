WebService
=============================
安装Newtonsoft.Json包

xaml ::

        <StackLayout>
            <Button x:Name="CallWebServiceButton"
                    Text="call web service"
                    Clicked="CallWebServiceButton_Clicked"/>
            <Label x:Name="ResultLable"/>
        </StackLayout>

cs ::

        public partial class MainPage : ContentPage
        {
            #系统默认初始化
            public MainPage()
            {
                InitializeComponent();
            }
            #Button点击事件
            private async void CallWebServiceButton_Clicked(object sender, EventArgs e)
            {
                #web请求类
                HttpClient client = new HttpClient();
                #发送Get请求
                var response=await client.GetAsync("https://v2.jinrishici.com/token");
                #将获得的结果(Task)转换为Json
                var json=await response.Content.ReadAsStringAsync();
                #将Json反序列化为类
                jinrishiciToken token = JsonConvert.DeserializeObject<jinrishiciToken>(json);
                #Label显示data
                ResultLable.Text = token?.data;
            }
        }

        #Json反序列化的类型
        public class jinrishiciToken
        {
            public string status { get; set; }
            public string data { get; set; }
        }