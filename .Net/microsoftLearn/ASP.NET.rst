ASP.NET Core
===============

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