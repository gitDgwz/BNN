LINQ
============

LINQ 查询与传统 foreach 循环相比具有三大优势：

    它们更简明、更易读，尤其在筛选多个条件时。

    它们使用最少的应用程序代码提供强大的筛选、排序和分组功能。

    无需修改或只需做很小的修改即可将它们移植到其他数据源。

**在 LINQ 中，查询变量本身不执行任何操作并且不返回任何数据。 它只是存储在以后某个时刻执行查询时为生成结果而必需的信息。**

查询变量本身从不保存查询结果,类似于只是保存了SQL语句,只是个查询规则.

::

    //evenNumQuery就是查询变量,IEnumerable<T>类型
    var evenNumQuery = from num in numbers
                   where (num % 2) == 0
                   select num;

    //并且可以直接使用foreach，
    foreach (int num in numQuery)
    {
        Console.Write("{0,1} ", num);
    }

执行方式
--------------------

标准查询运算符方法的 LINQ to Objects 实现主要通过两种方法之一执行：立即执行和延迟执行。 

立即执行其实就是声明加调用，延迟执行就是先声明后再用。哪些方法是立即哪些是延迟是官方已经设计好的。

立即执行
^^^^^^^^^^^^^^^^^^^

立即执行指的是读取数据源并执行一次运算.

Count、Max、Average 和 First 就属于此类查询.

| 可以使用 Enumerable.ToList 或 Enumerable.ToArray 方法强制任何查询立即执行.
| 还可以通过在紧跟查询表达式之后的位置放置一个 foreach 循环来强制执行查询。 

延迟执行
^^^^^^^^^^^^^^^^^^^

"延迟执行提供了查询重用功能，因为在每次循环访问查询结果时，查询都会从数据源中提取更新的数据."

延迟执行的查询运算符可以进一步分为两种类别：流式处理和非流式处理。

流式处理

    一边读取源数据，一边生成。

非流式处理

    读取所有源数据之后再生成结果。排序或分组等运算均属于此类别。

查询语句
------------------------

类似SQL语句

from
^^^^^^^^^^^^^^^^^^^^

from源数据，取出临时变量.和foreach类似.

::

    //country会被转化为强类型，所有可以直接"."出变量
    IEnumerable<Country> countryAreaQuery =
        from country in countries
        where country.Area > 20 //sq km
        select country;

    //如果里面又嵌套，可以使用多个from
    IEnumerable<City> cityQuery =
        from country in countries
        from city in country.Cities
        where city.Population > 10000
        select city;

group
^^^^^^^^^^^^^^^^^^^^^^^

数据分组

::

    var queryCountryGroups =
        from country in countries
        group country by country.Name[0];


select
^^^^^^^^^^^^^^^^^^^^^^^^^^^

得到按规则查询之后的对象，可以生成其他类型的序列（投影）。

::

    //直接select就是取得结果之后的数据
    IEnumerable<Country> sortedQuery =
        from country in countries
        orderby country.Area
        select country;

    //也可以生成匿名类型.也叫投影
    var queryNameAndPop =
        from country in countries
        select new
        {
            Name = country.Name,
            Pop = country.Population
        };

into
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

在 select 或 group 子句中使用 into 关键字创建存储查询的临时标识符

::

   // percentileQuery is an IEnumerable<IGrouping<int, Country>>
    var percentileQuery =
        from country in countries

        //let是创建一个临时变量用于计算,1_000 "_"下划线不会使数字改变而是更加直观清晰
        let percentile = (int)country.Population / 1_000

        //into 将分组后的数据放进一个新的列表,并且可以用来执行后续where等操作
        group country by percentile into countryGroup

        //这里Key是由分组得来的(自动生成).由percentile分的组，那么Key也就是percentile.
        where countryGroup.Key >= 20
        orderby countryGroup.Key
        select countryGroup;

    // grouping is an IGrouping<int, Country>
    foreach (var grouping in percentileQuery)
    {
        Console.WriteLine(grouping.Key);
        foreach (var country in grouping)
        {
            Console.WriteLine(country.Name + ":" + country.Population);
        }
    } 

let与into的区别，let只是创建一个临时变量。而into是创建了一个可继续用于查询规则的集合.

where
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
和sql一样，条件查询.

::

    IEnumerable<City> queryCityPop =
        from city in cities
        where city.Population is < 15_000_000 and > 10_000_000
        select city;


orderby
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
排序,可以有多个排序属性. ascending 为默认 , descending

::

    IEnumerable<Country> querySortedCountries =
        from country in countries
        //按照Area和Population排序,Population为次要排序
        orderby country.Area, country.Population descending
        select country;

join
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
链接另一个表.和from类似

::

    var categoryQuery =
        from cat in categories
        //链接到了products表
        join prod in products on cat equals prod.Category
        select new
        {
            Category = cat,
            Name = prod.Name
        };

let 
^^^^^^^^^^^^^^^^^
储存一个变量.

::

    string[] names = ["Svetlana Omelchenko", "Claire O'Donnell", "Sven Mortensen", "Cesar Garcia"];
    IEnumerable<string> queryFirstNames =
        from name in names
        let firstName = name.Split(' ')[0]
        select firstName;

    foreach (var s in queryFirstNames)
    {
        Console.Write(s + " ");
    }

    //Output: Svetlana Claire Sven Cesar

子查询
^^^^^^^^^^^^^^^^^^^^^

::

    //查询哪个年级平均分最高
    var queryGroupMax =
        from student in students
        group student by student.Year into studentGroup
        select new
        {
            Level = studentGroup.Key,
            HighestScore = (
                from student2 in studentGroup
                select student2.ExamScores.Average()
            ).Max()
        };

最后得到的是外层select的new class,里面的select返回给了HighestScore.

方法语句
------------------

用扩展方法的方式实现的.

::

    // var is used for convenience in these queries
    double average = numbers1.Average();
    var concatenationQuery = numbers1.Concat(numbers2);
    var largeNumbersQuery = numbers2.Where(c => c > 15);


LINQ和C#
----------------------------------------

混合使用查询和方法语法
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    // Query #7.

    // Using a query expression with method syntax
    var numCount1 = (
        from num in numbers1
        where num is > 3 and < 7
        select num
    ).Count();

    // Better: Create a new variable to store
    // the method call result
    IEnumerable<int> numbersQuery =
        from num in numbers1
        where num is > 3 and < 7
        select num;

    var numCount2 = numbersQuery.Count();


在运行时动态指定谓词
^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

    void FilterByYearType(bool oddYear)
    {
        IEnumerable<Student> studentQuery = oddYear
            ? (from student in students
            where student.Year is GradeLevel.FirstYear or GradeLevel.ThirdYear
            select student)
            : (from student in students
            where student.Year is GradeLevel.SecondYear or GradeLevel.FourthYear
            select student);
        var descr = oddYear ? "odd" : "even";
        Console.WriteLine($"The following students are at an {descr} year level:");
        foreach (Student name in studentQuery)
        {
            Console.WriteLine($"{name.LastName}: {name.ID}");
        }
    }

在查询表达式中处理异常
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

在查询外部捕获处理更为容易。

或在foreach中捕获.

LINQ 查询操作中的类型关系
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

查询变量的类型由select决定。

    如果select没有额外处理，那么就是原理的类型。

    如果使用了select投影，那么就是投影的属性

    如果select new{} ，那么就用var 来接收匿名类型

作为数据的表达式
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    //直接用方法返回查询
    IEnumerable<string> QueryMethod1(int[] ints) =>
        from i in ints
        where i > 4
        select i.ToString();

    //用out返回查询
    void QueryMethod2(int[] ints, out IEnumerable<string> returnQ) =>
        returnQ = from i in ints
                where i < 4
                select i.ToString();

    int[] nums = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 ];

    var myQuery1 = QueryMethod1(nums);

