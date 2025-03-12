Entity Framework Core
==========================

安装
-------------------
``dotnet add package Microsoft.EntityFrameworkCore.SqlServer``

``Install-Package Microsoft.EntityFrameworkCore.SqlServer``

获取工具
^^^^^^^^^^^^^^^^

PMC: ``Install-Package Microsoft.EntityFrameworkCore.Tools`` 项目

CLI: ``dotnet tool install --global dotnet-ef`` 全局, 
``dotnet add package Microsoft.EntityFrameworkCore.Design`` 迁移和设计

使用
----------------------
JetBrains Rider: 项目解决方案右键EntityFrameworkCore，可视化.

Add-Migration 迁移

update-Database 更新数据库

语法
----------------------

OrderBy() 排序
FirstAsync() 第一个


