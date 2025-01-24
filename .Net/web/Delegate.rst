Delegate
==========================

------------------
委托

委托不是函数，后面的(string message)，

意思不是让你传个string参数，而是这个delegate对应一个string参数的方法.

::

    //定义委托
    public delegate void NotificationHandler(string message);

    //创建实例
    NotificationHandler handler = message => Console.WriteLine($"Received: {message}");

::

    //用Action和Func不用定义，因为系统已经定义好了
    // 使用 Action
    Action<string> notify = message => Console.WriteLine(message);

    // 使用 Func
    Func<int, int, int> add = (a, b) => a + b;

---------------------
事件

::

    //Event,在实例委托的时候用的,如果已经有了委托类型则不需要定义委托
    //event关键字会封装delegate，使得可以直接使用+=，不用先赋值,而且内部始终为private；
    //只暴露+=和-=给其他类订阅,不会被其他类赋值

    public delegate void NotificationHandler(string message);

    public event NotificationHandler Notification;

::

    //	EventHandler,EventHandler<>
    //  封装过后的delegate类型，与Action和Func类似

    //  EventHandler
    //  是无返回值，且有(object obj,EventArgs args)参数的delegate。
    //  如:public delegate void SomeHandler(object obj,EventArgs args);

    public event EventHandler Event;

    //  EventArgs是一个类，一般创建一个继承类来自定义传输信息作为参数
    

    //  EventHandler<T>
    //  相比EventHandler将EventArgs改变为T
    //  如:public delegate void SomeHandler(object obj,T args);
    //  不必专门创建一个EventArgs类，较为轻便
