Avalonia8无限滚动及其原理
=================================

*   在dpa.Library安装AvaloniaInfiniteScrolling.Collection包
*   在dpa中安装AvaloniaInfiniteScrolling.Control包
*   修改ResultViewModel.cs,AvaloniaInfiniteScroll相关组件,将Collection改为无限滚动
    ::

        //修改后
        public class ResultViewModel : ViewModelBase
        {
            private readonly IPoetryStorage _poetryStorage;
        
            public ResultViewModel(IPoetryStorage poetryStorage)
            {
                _poetryStorage = poetryStorage;
                
                //poetryStorage.InitializeAsync().RunSynchronously();
                
                PoetryCollection = new AvaloniaInfiniteScrollCollection<Poetry>()
                {
                    OnCanLoadMore= ()=>_canLoadMore,
                    
                    OnLoadMore= async () =>
                    {
                        Status = Loading; 
                        var poetries = await _poetryStorage.GetPoetriesAsync(Expression.Lambda<Func<Poetry, bool>>(Expression.Constant(true),
                            Expression.Parameter(typeof(Poetry), "p")),PoetryCollection.Count,PageSize);
                        Status = string.Empty;

                        if (poetries.Count < PageSize)
                        {
                            _canLoadMore = false;
                            Status = NoMoreResult;
                        }

                        if (PoetryCollection.Count == 0 && poetries.Count == 0)
                        {
                            Status = NoResult;
                        }

                        return poetries;
                    }
                };
            }
            
            public const int PageSize = 100;

            public AvaloniaInfiniteScrollCollection<Poetry> PoetryCollection { get; }

            private bool _canLoadMore = true;
            
            private string _status;

            public string Status
            {
                get => _status;
                private set => SetProperty(ref _status, value);
            }

            public const string Loading = "正在载入";
            public const string NoResult = "没有满足条件的结果";
            public const string NoMoreResult = "没有更过结果";
            
        }

*   修改MainWindow.axaml,删除Behaviors和ItemsCon trol,添加ais命名空间
    ::

        <!--修改后-->
        <Window xmlns="https://github.com/avaloniaui"
                xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                xmlns:vm="using:dpa.ViewModels"
                xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
                xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
                
                xmlns:i="using:Avalonia.Xaml.Interactivity"
                xmlns:ia="using:Avalonia.Xaml.Interactions.Core"
                xmlns:ais="using:AvaloniaInfiniteScrolling"
                
                mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
                x:Class="dpa.Views.MainWindow"
                Icon="/Assets/avalonia-logo.ico"
                Title="dpa"
                DataContext="{Binding ResultViewModel, Source={StaticResource ServiceLocator}}"
                >
            
                <ais:AvaloniaInfiniteScrollControl ItemsSource="{Binding PoetryCollection}">
                        <ais:AvaloniaInfiniteScrollControl.ItemTemplate>
                                <DataTemplate>
                                        <TextBlock Text="{Binding Name}"></TextBlock>
                                </DataTemplate>
                        </ais:AvaloniaInfiniteScrollControl.ItemTemplate>
                </ais:AvaloniaInfiniteScrollControl>
            
        </Window>


*   在App.axaml ``<Application.Styles>`` 中添加 ``<StyleInclude Source="avares://AvaloniaInfiniteScrolling.Control/AvaloniaInfiniteScrollControlStyle.axaml" />``

*   在dpa.UnitTest中添加ViewModels文件夹并在其中创建ResultViewModelTest.cs
    
    .. danger:: 
        这次的Test不完整，在视频中有一行代码看不见。所以跑不过去.

    ::
        
        public class ResultViewModelTest : IDisposable
        {
            public ResultViewModelTest() => PoetryStorageHelper.RemoveDatabaseFile();

            public void Dispose() => PoetryStorageHelper.RemoveDatabaseFile();

            [Fact]

            public async Task PoetryCollection_Default()
            {
                var where = Expression.Lambda<Func<Poetry, bool>>(Expression.Constant(true),
                    Expression.Parameter(typeof(Poetry), "p"));

                var poetryStorage =
                    await PoetryStorageHelper.GetInitializedPoetryStorage();
                var resultViewModel = new ResultViewModel(poetryStorage);

                var statusList = new List<string>();

                resultViewModel.PropertyChanged += (sender, args) =>
                {
                    if (args.PropertyName == nameof(resultViewModel.Status))
                    {
                        statusList.Add(resultViewModel.Status);
                    }
                };
                
                Assert.Equal(0,resultViewModel.PoetryCollection.Count);
                await resultViewModel.PoetryCollection.LoadMoreAsync();
                Assert.Equal(100,resultViewModel.PoetryCollection.Count);
                Assert.Equal("醉花阴",resultViewModel.PoetryCollection.Last().Name);
                Assert.True(resultViewModel.PoetryCollection.CanLoadMore);
                Assert.Equal(2,statusList.Count);
                Assert.Equal(ResultViewModel.Loading,statusList[0]);
                Assert.Equal("", statusList[1]);
                var poetryCollectionChanged = false;
                //resultViewModel.PoetryCollection.CollectionChanged += (sender, args) => ();
                await resultViewModel.PoetryCollection.LoadMoreAsync();
                Assert.True(poetryCollectionChanged);
                Assert.Equal(139, resultViewModel.PoetryCollection.Count);
                Assert.Equal("永州八记 · 其一 · 始得西山宴游记",
                    resultViewModel.PoetryCollection[138].Name);
                Assert.False(resultViewModel.PoetryCollection.CanLoadMore);
                Assert.Equal(5,statusList.Count);
                Assert.Equal("",statusList[3]);

            }
        }


*   在xunit中,单元测试是并行的,可能同时创建和删除数据库导致错误
    在dpa.UnitTest中创建xunit.runner.json文件
    ::

        {
        "parallelizeAssembly": false,
        "parallelizeTestCollection": false
        }
    
    右键xunit.runner.json属性, ``复制到输出目录``选择 ``如果较新则复制`` (``Copy if newer``),测试变成单线程

*   在首次开启项目时候没有初始化数据库,导致报错,但是InitializeAsync是个异步方法不能够发在构造器里面,
  
    于是在 ``ResultViewModel``构造器添加 ``poetryStorage.InitializeAsync().RunSynchronously();`` 用来初始化，但是添加了这句，后面数据则不能显示.

    .. danger:: 

        后面会介绍真正应该使用的方法，现在必须使用 ``poetryStorage.InitializeAsync().RunSynchronously();`` 并且开启项目来初始化数据库，初始化后要关闭项目再删掉该行项目才能正常运行.

        是一个临时的没有办法的方法.

~~~~~~~~~~~~~~~~~~~~~~~~~~
Avalonia判断滚动条触发加载

MAUI判断显示的是否是最后一个item来判断加载

Control在View层,Collection在Library层

构造函数不能异步

