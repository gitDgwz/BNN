Bind
============

数据绑定

目标为控件，绑定源为对象.

**只能绑定属性而不是字段,因为属性Set才能触发changed事件**

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

有四种绑定

1. 直接设置DataContext， ``Binding Person.Name``
2. 指明某个源头. ``Binding Source={StaticResource RedBrush},Path=Color}`` Path为绑定的属性，因为上一种方式没有别的设置所以可以省略
   StaticResource直接在<Window.Resources>中设置
3. ElementName. ``Binding ElementName=panel,Path=Margin`` 绑定了panel控件的Margin属性
4. RelativeSource, ``{Binding RelativeSource={RelativeSource Mode=FindAncestor,AncestorType=StackPanel},Path=Margin}"/>`` 相对绑定，寻找父类

**直接在隐藏代码里面设置DataContext在xaml中不会有提示**

INotifyPropertyChanged
--------------------------
作用是属性更改的接口，里面只有一个event

一般自定义一个ObservableObject继承这个接口，自动实现接口成员会自动多出 ``OnPropertyChanged`` 和 ``SetField``
两个方法，

``OnPropertyChanged`` 方便于直接调用事件，因为是event 只能在自己类里面调用，所以要用方法包装一下。直接在外面调用event 会报错
``事件 'PropertyChanged' 只能出现在 += 或 -= 的左侧(除非从类内部使用时)``

``SetField`` 暂时没用上

ObservableCollection
----------------------
ObservableCollection直接继承于Collection<T>, INotifyCollectionChanged, INotifyPropertyChanged，
不需要再写自定义类，并且直接写{get;set;}就行，changed事件已经写好了.



~~~~~~~~~~~~~~~~~~~~~~

IPAddress.Any 

~~~~~~~~~~~~~~~~~~~~~~~~

https://www.wpfsoft.com/binding 有详细讲解