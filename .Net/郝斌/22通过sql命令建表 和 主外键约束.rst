22 通过sql命令建表 和 主外键约束
=======================================

::

    基本语法
    create table dept
    (
        dept_id int primary key, 
        dept_name nvarchar(100) not null,
        dept_address nvarchar(100)
    )


    //constraint用来指定名称的

    create table emp
    ( 
        --不能写成{
        emp_id int constraint pk_emp_id_hahaha primary key,
        emp_name nvarchar(20) not null,
        emp_sex nchar(1),
        dept_id int constraint fk_depk_id_heihei foreign key references dept(dept_id)	
    )

sqlserver 最后一行可以有","
oracle 最后一行不能有 ","

执行的时候左上方可以选择命令执行库


create table 的最后不要写","

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
约束

check 

    check(x>1000 and x<10000>) 等待

insert

    如果不写column_name，那么必须全部赋值.如果某个属性不赋值而且没有默认值那么为null.

    insert into table_name [(column_name)] values(a,b,c,d)

default 默认值

unique 唯一值（不允许重复） , 代理主键.
    
    oracle 可以多个为空.

    sqlserver 可以为空,但只能一个为空.

主键不能为null

not null 不为空，默认可以为空.

外键来自与其他表的主键.

identity 自增

drop table table_name; 删表

主键用int搜索快。所以不要是具体属性.