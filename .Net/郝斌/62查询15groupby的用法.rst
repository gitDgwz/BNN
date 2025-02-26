62 查询15_group by的用法【重难点】
==========================================

group by

    分组也是聚合。

    分组是统计组的信息,只能查询聚合信息,不能出现组内的详细信息

    ``select AVG(sal) ,deptno from emp group by deptno``

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

同样可以按照多个信息分组，组合分组,先按deptno分成大组(中间状态)，再以job分成小组(分组结果)

``select AVG(sal) ,deptno,job from emp group by deptno,job``

再组合排序，先排deptno,再排AVG(sal)

``select AVG(sal) ,deptno,job from emp group by deptno,job order by deptno,AVG(sal)``

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

having

    分组之后的过滤, **只能过滤聚合数据**

    ``select deptno,AVG(sal)  from emp group by deptno having avg(sal) >2000``

连接查询

    定义

    分类

        内连接

        外连接

        完全连接

        交叉连接

        自连接

        联合

嵌套查询，oracle中讲

