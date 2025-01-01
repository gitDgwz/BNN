Avalonia18ViewModel in ViewModel与表达式树
=================================================

在QueryViewModel.cs 中添加了搜索，并且讲解了Expression

::

     private static Expression GetExpression(ParameterExpression parameter, FilterViewModel filterViewModel)
     {
         var property = Expression.Property(parameter, filterViewModel.Type.PropertyName);

         var method = typeof(string).GetMethod("Contains", new[] { typeof(string) });

         var condition = Expression.Constant(filterViewModel.Content, typeof(string));

         return Expression.Call(property, method, condition);
     }
    
Expression.Property : 参数的成员，取到的是成员表达式，也就是 p.Name

method : 利用反射取得方法, string 的 Contains方法 ，而且是包含一个string参数的方法

Expression.Constant : 将一个stirng转化为常数表达式

Expression.Call : 表达式方法调用, P.Name 调用 Contains方法，参数为condition

::

     public void Query()
     {
         var parameter = Expression.Parameter(typeof(Poetry), "p");

         var aggregatedExpression = FilterViewModelCollection
             .Where(p => !string.IsNullOrWhiteSpace(p.Content))
             .Select(p => GetExpression(parameter, p))
             .Aggregate(Expression.Constant(true) as Expression, Expression.AndAlso);

         var where = Expression.Lambda<Func<Poetry, bool>>(aggregatedExpression, parameter);
         
         _contentNavigationService.NavigateTo(ContentNavigationConstant.ResultView);
         
         
     }

参数表达式：规定参数的类型

将filterViewModel中的ViewModels做非空筛选，

然后执行GetExpression获得一个表达式，

再将这些表达式聚合(用true聚合).

