ASP.NET Core
===============

Web UI
--------------

**对于新的项目开发，我们推荐 ASP.NET Core Blazor** --微软

* ASP.NET Core Blazor

    全栈 WebUI框架.

* ASP.NET Core Razor Pages

    基于页面的模型.

    对 Razor Pages 的支持是建立在 ASP.NET Core MVC 基础之上的。
    组织 ASP.NET Core 页面，使用起来比 ASP.NET MVC 更简单：

* ASP.NET Core MVC

    模型、视图和控制器

    用户请求被路由到控制器

* 其他

    采用前端 JavaScript 框架的 ASP.NET Core 单页应用程序 (SPA)

    组合多个 Web UI 解决方案：ASP.NET Core MVC 或 Razor Pages 和 Blazor

ASP.NET Core Razor Pages
----------------------------

入门
^^^^^^^^^^^^^^^^



1. 创建模型类
    
    创建Models文件夹，在里面创建模型类Movies.cs

2. 搭建基架  
    
    在Pages中创建Movies文件夹，添加 ``scaffolded item`` 选择Razor(CRUD).

    会自动创建以下: 
    
    Pages/Movies：“创建”、“删除”、“详细信息”、“编辑”和“索引”。
    
    Data/RazorPagesMovieContext.cs

3. 迁移和更新


项目文件
^^^^^^^^^^^^^^^^^

* .cshtml 包含使用Razor语法的C#代码的HTML
* .cshtml.cs 其中包含处理页面事件的C#代码

_Layout.cshtml 文件可配置所有页面通用的 UI 元素.

wwwroot文件夹: 包含静态资产，如 HTML 文件、JavaScript 文件和 CSS 文件.

appsettings.json 包含配置数据, **连接sqlserver的凭证就写在这里**

使用的是LocalDb
    
    SQL Server Express LocalDB

    LocalDB是轻型版的SQL Server Express 数据库引擎，以程序开发为目标.

    LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置.

    默认情况下，LocalDB 数据库在C:\Users\<user>\ 目录下创建  ``*.mdf`` 文件。


    

Web API
---------------

文件:

Properties:
    launchSettings.json,启动配置类,里面能看到端口

Controllers: 
    PizzaController.cs

    *   ``[ApiController]`` 表明是一个ApiController控件,方便创建

    *   ``[Route("[controller]")]`` 路由设置,命名规范,在链接中直接删除Controller便是网址
    
    *   继承于ControllerBase类

    *   有一个默认的无参数构造器

    *   使用RESTful,GET,POST,PUT,DELETE,
            
            ``[HttpGet]`` 无参数Get,返回类型 ActionResult<List<Pizza>>

            ``[HttpGet("{id}")]`` 有参数Get,返回类型 ActionResult<List<Pizza>>

            ``[HttpPost]`` 创建,返回类型 IActionResult<List<Pizza>>,返回类型不
            固定,如 ``CreatedAtAction``, ``BadRequest``, ``NotFound``, ``NoContent``,
            所以使用接口

            ``[HttpPut("{id}")]`` 更新,返回类型 IActionResult<List<Pizza>>

            ``[HttpDelete("{id}")]`` 删除,返回类型 IActionResult<List<Pizza>>

Models:
    Pizza.cs

    Pizza的类

Services:
    PizzaService.cs,Pizza的服务类,处理类,

    里面有对Pizaas增删改查的方法.

ContosoPizza.csproj,项目文件,可以看到框架和依赖

ContosoPizza.http,用来测试REST的(没用过)

Program.cs,项目启动文件


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

httprepl ,测试工具,在项目运行时使用 ``httprepl https://localhost:{port}``或者
``httprepl https://localhost:{port}``,阅读-评估-打印-循环
