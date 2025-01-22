Avalonia10访问JSONWeb服务
===============================

*   在dpa.Library Services中添加ITodayPoetryService.cs和JinrishiciService.cs
    ::

        public interface ITodayPoetryService
        {
            
        }  
    
    ::

        public class JinrishiciService : ITodayPoetryService
        {
            private readonly IAlertService _alertService;

            private string _domainName;

            public JinrishiciService(IAlertService alertService,
                string domainName="v2.jinrishici.com")
            {
                _alertService = alertService;
                _domainName = domainName;
            }
            public async Task<string> GetTokenAsync()
            {
                var httpClient = new HttpClient();

                HttpResponseMessage response;
                    
                try
                {   
                    //用$符合拼接变量
                    response = await httpClient.GetAsync($"https://{_domainName}/token");
                }
                catch (Exception e)
                {
                    await _alertService.AlertAsync("", e.Message);
                    return null;
                }

                var json= 
                    await response.Content.ReadAsStringAsync();


                //可以无视大小写
                var jinrishiciToken=
                    JsonSerializer.Deserialize<JinrishiciToken>(json,
                        new JsonSerializerOptions
                        {
                            PropertyNameCaseInsensitive = true
                        });
                
                return jinrishiciToken.Data;
            }
        }

        //复制Jinrishici返回的Token，Rider左上角paste : JSON as Classes自动生成

        public class JinrishiciToken
        {
            //public string Status { get; set; }
            public string Data { get; set; }
        }



*   在dpa.Test Services中添加JinrishiciServiceTest.cs
    ::

        public class JinrishiciServiceTest
        {
            [Fact]
            public async Task GetTokenAsync_Default()
            {
                var alertServiceMock = new Mock<IAlertService>();
                var mockAlertService = alertServiceMock.Object;
                
                var jinrishiciService = new JinrishiciService(mockAlertService);
                var json = await jinrishiciService.GetTokenAsync();
                Assert.False(string.IsNullOrWhiteSpace(json));
            }
        }


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

序列号就是把一个类转化成字符串或者二进制数据。
反序列化是把字符串或者二进制数据转换成一个类

``JsonSerializer`` :

    序列号时候会把属性换成小写，反序列化的时候不会改变大小写.

    可以部分反序列化。

用$和{}符号在string中拼接变量

HttpClient处理网络请求

Rider左上角paste : JSON as Classes自动生成

