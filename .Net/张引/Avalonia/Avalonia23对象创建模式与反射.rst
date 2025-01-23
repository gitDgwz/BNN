Avalonia 23：对象创建模式与反射
=================================

设计模式
--------------------------

静态工厂方法模式

::

    //C#
    TimeSpane.FromMinutes(1);

工厂方法模式

::

    //Kotlin
    val simpleServiceFactory=SimpleServiceFactory()
    val simpleService=simpleServiceFactory.CreateInstance()





建造者模式(复杂对象创建)

::

    //C#
    var builder=WebApplication.CreateBuilder(args);
    builder.AddCustomCors();

    var app=builder.Build();


依赖注入模式

::

    //利用构造函数
    public class Business
    {
        private readonly IService _service;

        public Business(IService service)
        {
            _service=service?? throw new ArgumentNullException(nameof(service));
        }
    }

    //
    public static IServiceProvider ConfigServices()
    {
        var services=new ServiceCollection();
        services.AddTransient<IService,service>();
        services.AddTransient<Business>();
        return services.BuildServiceProvider();
    }

抽象工厂模式

原型模式(复制一份)


反射
-----------------------------------
代价很高，几乎慢10倍


获得反射
::

    //C#
    public class Order{}

    var orderType=Typeof(Order)

    var typeType=typeof(Type)


反射的唯一性(类和实例都可以获得Type信息)
::

    //C#
    var order=new Order();
    var orderTypeFromInstance=order.GetType();

    //C#
    //两个实例是同一个,详细信息是唯一的
    typeof(Order)==order.GetType() //true
    

反射创建实例
::

    //C#
    var orderInstance= new Order();

    var orderTypeInfo=typeof(Order);
    
    var anotherOrderInstance=(Order) Activator.CreateInstance(orderTypeInfo);

根据字符串获得类型信息
::

    //c#
    namespace Reflection;

    public class Order{}

    //Main函数中,GetExecutingAssembly不能有参构造函数
    var orderTypeFromInstance=Assembly.GetExecutingAssembly().GetType("Reflection.Order");


子类型赋值给父类型
::

    Console.WriteLine(simpleServiceType.IsAssignableTo(IServiceType)); //true

    Console.WriteLine(simpleServiceType.BaseType); //System.Object

查询继承接口
::

    //C#
    foreach(var @interface in simpleServiceType.GetInterfaces())
    {
        Console.WriteLine(@interface);
    }
    //输出Reflection.IService

查询程序中所有type，包括接口等待，找出IServiceType的子类
::

    //C#
    var types=Assembly.GetExecutingAssembly().GetTypes();

    //C#
    var subTypesOfIService=types.Where(p=>p.IsAssignableTo(IServiceType));


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

反射描述函数

::

    //C#
    public class SomeObject
    {
        public string SomeFunction(int i) => string.Empty;
    }

    //C#,先获得类的信息
    var SomeObjectType=typeof(SomeObject);
    //获得方法的信息 
    var SomeFunctionInfomation=SomeObjectType.GetMethod(nameof(SomeObject.SomeFunction));

    Console.WriteLine(SomeFunctionInfomation.IsPublic);
    //true
    Console.WriteLine(SomeFunctionInfomation.RetrunType);
    //System.String
    Console.WriteLine(SomeFunctionInfomation.Name);
    //SomeFunction
    Console.WriteLine(SomeFunctionInfomation.GetParameters().Length);
    //1
    Console.WriteLine(SomeFunctionInfomation.GetParameters()[0].ParameterType);
    //System.Int32
    Console.WriteLine(SomeFunctionInfomation.GetParameters()[0].Name);
    //i

反射调用函数
::

    //C#
    var someObject=new SomeObject();

    //成员函数，必须有对象,
    参数为object数组,因为有可能有零或多个参数,
    在反射中直接使用object类型,因为它不知道明确类型.
    var ret=(string)SomeFunctionInfomation.Invoke(someObject,new object?[]{500});

反射调用构造函数

::

    //C#
    //可能有多个构造函数
    var constructors=complexClassType.GetConstructors();

    var firstConstructor=constructors.First();

    var constructorParameters=firstConstructor.GetParameters();

    Console.WriteLine(constructorParameters.Length);
    //2
    Console.WriteLine(constructorParameters[0].ParameterType);
    //System.Int32
    Console.WriteLine(constructorParameters[1].ParameterType);
    //Reflection.IService

    public complexClass(int i,IService service){}

    //构造函数是静态方法不需要成员
    var complexClassInstance=(complexClass) firstConstructor.Invoke(new object[]{1,new SimpleService()});