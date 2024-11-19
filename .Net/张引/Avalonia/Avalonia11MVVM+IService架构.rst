Avalonia11MVVM+IService架构
========================================

*   实现二级缓存修改JinrishiciService.cs
    ::

        public class JinrishiciService : ITodayPoetryService
        {
            private readonly IAlertService _alertService;

            private readonly IPreferenceStorage _preferenceStorage;

            private readonly IPoetryStorage _poetryStorage;
            
            private string _domainName;

            public JinrishiciService(IAlertService alertService,
                IPreferenceStorage preferenceStorage,
                IPoetryStorage poetryStorage,
                string domainName="v2.jinrishici.com")
            {
                _alertService = alertService;
                _poetryStorage = poetryStorage;
                _preferenceStorage = preferenceStorage;
                _domainName = domainName;
            }

            private string _token = string.Empty;

            public static readonly string JinrishiciTokenKey =
                $"{nameof(JinrishiciService)}.Token";

            private const string Server = "今日诗词服务器";    
            
            public async Task<string> GetTokenAsync()
            {
                if (!string.IsNullOrWhiteSpace(_token))
                {
                    return _token;
                }

                _token = _preferenceStorage.Get(JinrishiciTokenKey, string.Empty);

                if (!string.IsNullOrWhiteSpace(_token))
                {
                    return _token;
                }
                
                using var httpClient = new HttpClient();

                HttpResponseMessage response;
                    
                try
                {
                    response = await httpClient.GetAsync($"https://{_domainName}/token");
                    response.EnsureSuccessStatusCode();
                }
                catch (Exception e)
                {
                    await _alertService.AlertAsync(ErrorMessageHelper.HttpClientErrorTitle,
                        ErrorMessageHelper.GetHttpClientError(Server ,e.Message));
                    
                    return _token;
                }

                var json= 
                    await response.Content.ReadAsStringAsync();

                var jinrishiciToken=
                    JsonSerializer.Deserialize<JinrishiciToken>(json,
                        new JsonSerializerOptions
                        {
                            PropertyNameCaseInsensitive = true
                        });
                
                _token= jinrishiciToken.Data;
                _preferenceStorage.Set(JinrishiciTokenKey,_token);
                return _token;
            }

            public async Task<TodayPoetry> GetTodayPoetryAsync()
            {
                var token = await GetTokenAsync();
                if (string.IsNullOrWhiteSpace(token))
                {
                    return await GetRandomPoetryAsync();
                }

                using var httpClient = new HttpClient();
                httpClient.DefaultRequestHeaders.Add("X-User-Token",token);

                HttpResponseMessage response;

                try
                {
                    response =
                        await httpClient.GetAsync("https://v2.jinrishici.com/sentence");
                    response.EnsureSuccessStatusCode();
                }
                catch (Exception e)
                {
                    await _alertService.AlertAsync(ErrorMessageHelper.HttpClientErrorTitle,
                        ErrorMessageHelper.GetHttpClientError(Server ,e.Message));
                    return await GetRandomPoetryAsync();
                }

                var json = await response.Content.ReadAsStringAsync();
                JinrishiciSentence jinrishiciSentence;
                try
                {
                    jinrishiciSentence = JsonSerializer.Deserialize<JinrishiciSentence>(
                        json,
                        new JsonSerializerOptions
                        {
                            PropertyNameCaseInsensitive = true
                        }) ?? throw new JsonException();
                }
                catch (Exception e)
                {
                    _alertService.AlertAsync(
                        ErrorMessageHelper.JsonDeserializationErrorTitle,
                        ErrorMessageHelper.GetJsonDeserializationError(Server, e.Message));
                        return await GetRandomPoetryAsync();
                }

                try
                {
                    return new TodayPoetry
                    {
                        Snippet = jinrishiciSentence.Data?.Content ?? throw new JsonException(),
                        Name = jinrishiciSentence.Data.Origin?.Title ?? throw new JsonException(),
                        Dynasty = jinrishiciSentence.Data.Origin?.Dynasty ?? throw new JsonException(),
                        Author = jinrishiciSentence.Data?.Origin.Author ?? throw new JsonException(),
                        Content = String.Join("\n", jinrishiciSentence.Data?.Content ?? throw new JsonException()),
                        Source = TodayPoetrySources.Jinrishici
                    };
                }
                catch (Exception e)
                {
                    _alertService.AlertAsync(
                        ErrorMessageHelper.JsonDeserializationErrorTitle,
                        ErrorMessageHelper.GetJsonDeserializationError(Server, e.Message));
                    return await GetRandomPoetryAsync();
                }
            }

            private async Task<TodayPoetry> GetRandomPoetryAsync()
            {
                var poetries= await _poetryStorage.GetPoetriesAsync(
                    Expression.Lambda<Func<Poetry,bool>>(Expression.Constant(true),
                        Expression.Parameter(typeof(Poetry),"p")),
                    new Random().Next(PoetryStorage.NumberPoetry),
                    1);
                var poetry = poetries.First();
                return new TodayPoetry
                {
                    Snippet = poetry.Snippet,
                    Name = poetry.Name ,
                    Dynasty = poetry.Dynasty,
                    Author = poetry.Author,
                    Content = poetry.Content,
                    Source = TodayPoetrySources.Local
                };
            }
        }

        //这里是今日诗词返回的Json信息

        public class JinrishiciToken
        {
            //public string Status { get; set; }
            public string Data { get; set; }
        }

        public class JinrishiciSentence
        {
            public JinrishiciData Data { get; set; } = new();
        }

        public class JinrishiciData
        {
            public string Content { get; set; }=String.Empty;
            public JinrishiciOrigin Origin { get; set; } = new();
        }

        public class JinrishiciOrigin
        {
            public string Title { get; set; }=String.Empty;
            public string Dynasty { get; set; }=String.Empty;
            public string Author { get; set; }=String.Empty;
            
            public List<string> Content { get; set; } = [];
        }

*   编写测试JinrishiciServiceTest.cs
    ::

        public class JinrishiciServiceTest
        {
            [Fact(Skip = "依赖远程服务的测试")]
            public async Task GetTokenAsync_ReturnIsNotNullOrWhiteSpace()
            {
                var alertServiceMock = new Mock<IAlertService>();
                var mockAlertService = alertServiceMock.Object;

                var preferenceServiceMock = new Mock<IPreferenceStorage>();
                var mockPreferenceService = preferenceServiceMock.Object;

                var poetryStorageMock = new Mock<IPoetryStorage>();
                var mockPoetryStorage = poetryStorageMock.Object;

                var jinrishiciService = new JinrishiciService(mockAlertService,mockPreferenceService,mockPoetryStorage);
                var token = await jinrishiciService.GetTokenAsync();
                Assert.False(string.IsNullOrWhiteSpace(token));
                alertServiceMock.Verify(p=>
                    p.AlertAsync(It.IsAny<string>(),It.IsAny<string>()),Times.Never);
                preferenceServiceMock.Verify(p=>
                    p.Get(JinrishiciService.JinrishiciTokenKey,string.Empty),Times.Once);
                preferenceServiceMock.Verify(p=>
                    p.Set(JinrishiciService.JinrishiciTokenKey,token),Times.Once);
            }
            
            [Fact]
            public async Task GetTokenAsync_NetworkError()
            {
                var alertServiceMock = new Mock<IAlertService>();
                var mockAlertService = alertServiceMock.Object;

                var preferenceServiceMock = new Mock<IPreferenceStorage>();
                var mockPreferenceService = preferenceServiceMock.Object;

                var poetryStorageMock = new Mock<IPoetryStorage>();
                var mockPoetryStorage = poetryStorageMock.Object;

                var jinrishiciService = new JinrishiciService(mockAlertService,mockPreferenceService,mockPoetryStorage,
                    "http://no.such.url");
                var token = await jinrishiciService.GetTokenAsync();
                
                Assert.True(string.IsNullOrWhiteSpace(token));
                alertServiceMock.Verify(p=>
                    p.AlertAsync(It.IsAny<string>(),It.IsAny<string>()),Times.Once);
                preferenceServiceMock.Verify(p=>
                    p.Get(JinrishiciService.JinrishiciTokenKey,string.Empty),Times.Once);
                preferenceServiceMock.Verify(p=>
                    p.Set(JinrishiciService.JinrishiciTokenKey,token),Times.Never);
            }
        }



*   在dpa.Library Helpers中添加ErrorMessageHelper.cs
    ::

        public class ErrorMessageHelper
        {
            public const string HttpClientErrorTitle = "连接错误";

            public static string GetHttpClientError(string server, string message) =>
                $"与{server}连接时发生了错误:\n{message}";

            public const string JsonDeserializationErrorTitle = "读取数据错误";
            
            public static string GetJsonDeserializationError(string server,string message)=>
                $"从{server}读取数据时发生了错误:\n{message}";

            public const string JsonDeserializationErrorButton = "确定";
        }

*   在dpa.Library Models中创建TodayPoetry.cs
    ::

        public class TodayPoetry
        {
            public string Snippet { get; set; }=string.Empty;

            public string Name { get; set; } = string.Empty;

            public string Dynasty { get; set; } = string.Empty;

            public string Author { get; set; } = string.Empty;

            public string Content { get; set; } = string.Empty;

            public string Source { get; set; } = string.Empty;
        }

*   完成dpa.Library Services中的ITodayPoetryService
    ::

        public interface ITodayPoetryService
        {
            Task<TodayPoetry> GetTodayPoetryAsync();
        }

        public static class TodayPoetrySources
        {
            public const string Jinrishici = nameof(Jinrishici);

            public const string Local = nameof(Local);
        }


*   dpa ServiceLocator.cs添加注册
    ::

        public ServiceLocator()
        {
            var serviceCollection = new ServiceCollection();

            serviceCollection.AddSingleton<ResultViewModel>();
            serviceCollection.AddSingleton<IPoetryStorage, PoetryStorage>();
            serviceCollection.AddSingleton<IPreferenceStorage, FilePreferenceStorage>();
            serviceCollection.AddSingleton<TodayPoetry>();
            serviceCollection.AddSingleton<ITodayPoetryService, JinrishiciService>();
            serviceCollection.AddSingleton<IAlertService,AlertService>();
            
            _serviceProvider = serviceCollection.BuildServiceProvider();
        }
        
*   在dpa中创建文件夹Services,并且创建AlertService.cs
    ::

        public class AlertService: IAlertService
        {
            public async Task AlertAsync(string title, string message)
            {
                //await MessageBox.ShowAsync(message,title);
            }
        }