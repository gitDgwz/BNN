microsoftLearn 
========================

.. toctree:: 
    :glob:

    *


语义化版本控制
-------------------------------

主版本：最左边的数字。 例如 1.0.0 中的 1。 此数字发生更改意味着代码可能出现中断性变更。 可能需要重写部分代码。

次要版本：中间的数字。 例如 1.2.0 中的 2。 此数字发生更改意味着添加了新功能。 你的代码仍可正常工作。 接受更新通常是安全的。

修补程序版本：最右边的数字。 例如 1.2.3 中的 3。 此数字发生更改意味着应用了一个更改，修复了代码中应正常工作的内容。 接受更新应是安全的。

============ ======================
类型 	        发生的更改
============ ======================
主版本 	        1.0.0 更改为 2.0.0
次要版本 	    1.1.1 更改为 1.2.0
修补程序版本 	1.0.1 更改为 1.0.2
============ ======================


============= ================  ========================================
表示法 	       应用的规则 	        说明
============= ================  ========================================
1.0 	         x >= 1.0 	     最低版本（包含）
(1.0,) 	         x > 1.0     	 最低版本
[1.0] 	         x == 1.0 	     精确的版本匹配
(,1.0] 	          x ≤ 1.0     	 最高版本（包含）
(,1.0) 	         x < 1.0 	     最高版本
[1.0,2.0]       1.0 ≤ x ≤ 2.0 	 精确范围（包含）
(1.0,2.0)      1.0 < x < 2.0 	 精确范围
[1.0,2.0)      1.0 ≤ x < 2.0     混合了最低版本（包含）和最高版本
(1.0) 	        无效 	          无效
============= ================  ========================================

::
        
    <!-- Accepts any version 6.1 and later. -->
    <PackageReference Include="ExamplePackage" Version="6.1" />

    <!-- Accepts any 6.x.y version. -->
    <PackageReference Include="ExamplePackage" Version="6.*" />
    <PackageReference Include="ExamplePackage" Version="[6,7)" />

    <!-- Accepts any later version, but not including 4.1.3. Could be
        used to guarantee a dependency with a specific bug fix. -->
    <PackageReference Include="ExamplePackage" Version="(4.1.3,)" />

    <!-- Accepts any version earlier than 5.x, which might be used to prevent pulling in a later
        version of a dependency that changed its interface. However, we don't recommend this form because determining the earliest version can be difficult. -->
    <PackageReference Include="ExamplePackage" Version="(,5.0)" />

    <!-- Accepts any 1.x or 2.x version, but not 0.x or 3.x and later. -->
    <PackageReference Include="ExamplePackage" Version="[1,3)" />

    <!-- Accepts 1.3.2 up to 1.4.x, but not 1.5 and later. -->
    <PackageReference Include="ExamplePackage" Version="[1.3.2,1.5)" />

