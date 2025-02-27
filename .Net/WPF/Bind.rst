Bind
============

数据绑定


目标为控件，绑定源为对象.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

有四种绑定

1. 直接设置DataContext， ``Binding Person.Name``
2. 指明某个源头. ``Binding Source={StaticResource RedBrush},Path=Color}`` Path为绑定的属性，因为上一种方式没有别的设置所以可以省略
   StaticResource直接在<Window.Resources>中设置
3. ElementName. ``Binding ElementName=panel,Path=Margin`` 绑定了panel控件的Margin属性
4. RelativeSource, ``{Binding RelativeSource={RelativeSource Mode=FindAncestor,AncestorType=StackPanel},Path=Margin}"/>`` 相对绑定，寻找父类











~~~~~~~~~~~~~~~~~~~~~~

https://www.wpfsoft.com/binding 有详细讲解