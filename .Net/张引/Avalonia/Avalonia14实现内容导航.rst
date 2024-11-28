Avalonia14实现内容导航
=============================

*   dpa.Library ViewModel 添加MainViewModel
    ::

        public class MainViewModel: ViewModelBase
        {
            public ObservableCollection<ViewModelBase> ContentStack { get; } = [];
            
            private ViewModelBase _content;

            public ViewModelBase Content
            {
                get => _content;
                private set => SetProperty(ref _content, value);
            }

            public void PushContent(ViewModelBase content) =>
                ContentStack.Insert(0, Content = content);

            public void GoBack()
            {
                if (ContentStack.Count <= 1)
                {
                    return;
                }
                
                ContentStack.RemoveAt(0);
                Content = ContentStack[0];
            }
            
        }

        public class MenuItem
        {
            public string View { get; private init; }
            
            public string Name { get; private init; }

            private MenuItem()
            {
                
            }

            private static MenuItem TodayView => 
                new() { Name = "今日推荐", View = MenuNavigationConstant.TodayView };
            private static MenuItem QueryView => 
                new() { Name = "诗词搜索", View = MenuNavigationConstant.QueryView };
            private static MenuItem FavoriteView => 
                new() { Name = "诗词搜索", View = MenuNavigationConstant.FavoriteView };

            
            public static IEnumerable<MenuItem> MenuItems { get; } = [TodayView, QueryView, FavoriteView];
        }

*   dpa Views 修改MainView.axaml
    ::

        xmlns:lvm="using:dpa.Library.ViewModels"
        xmlns:lc="using:dpa.Converters"

*   dpa.Library Services IMenuNavigationService.cs
    ::

        public interface IMenuNavigationService
        {
            
        }

        public static class MenuNavigationConstant
        {
            public const string TodayView = nameof(TodayView);
            public const string QueryView = nameof(QueryView);
            public const string FavoriteView = nameof(FavoriteView);
        }

*   dpa ServiceLocator 依赖注册添加 ``serviceCollection.AddSingleton<MainViewModel>();`` ，并且声明变量 ``public MainViewModel MainViewModel => serviceProvider.GetRequiredService<MainViewModel>();``

*   dpa.Library Services添加IContentNavigationService
    ::

        public interface IContentNavigationService
        {
            void NavigateTo(string view);
        }


*   dpa 添加文件夹 Converters ,里面CountToBoolConverter.cs
    ::

        public class CountToBoolConverter : IValueConverter
        {
            public object? Convert(object? value, Type targetType, object? parameter, CultureInfo culture)=>
            
                value is int count && parameter is string conditionString
                                && int.TryParse(conditionString, out var condition)
                    ? count > condition : null;
            

            public object? ConvertBack(object? value, Type targetType, object? parameter, CultureInfo culture)
            {
                throw new NotImplementedException();
            }
        }