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

**谁实现OnPropertyChanged，谁的属性改变就会触发，但是如果属性是个类，
那么类里面的属性改变不会触发，只有引用改变才会触发，除非给这个属性再实现OnPropertyChanged**




ObservableCollection
----------------------
ObservableCollection直接继承于Collection<T>, INotifyCollectionChanged, INotifyPropertyChanged，
不需要再写自定义类，并且直接写{get;set;}就行，changed(其实是CollectionChanged)事件已经写好了.

**如果List不用ObservableCollection，而是用INotifyPropertyChanged实现，那么只能监听到整个List的更改，
而List其中的属性更改无法监听，所以还是要使用ObservableCollection**

.. danger:: 

   踩了个坑，我并未将ObservableCollection绑定到集合控件中，而是绑定到了TextBlock，再使用IValueConverter将其值转化为字符串，但是ObservableCollection内容改变的时候并没有触发UI改变。

   这是因为TextBlock不是集合控件: ``<TextBlock Grid.Row="0" Text="{Binding ActionStack,Converter={StaticResource ListStringJoinConverter}}"></TextBlock>`` TextBlock绑定的仍然是整个ObservableCollection,只能监测到整个ObservableCollection的更改。

   应该用集合控件ListBox等等来绑定，它会自动绑定到集合里面的元素。这样ObservableCollection内容改变时，UI将自动改变。


IValueConverter
-------------------
数值转化器，在绑定的时候给数据进行一定的逻辑运算.

写一个自定义Converter类继承 IValueConverter,实现 ``Convert``, ``ConvertBack`` 两个接口，

|  Convert方法成员：输入的value及parameter参数，根据自定义逻辑判断，返回一个object对象给前端XAML使用。
|  ConvertBack方法成员：与Convert相反，将前端输入的数据转换成另一个对象返回给后端的数据源。

一般使用 ``Convert``。

要在Resources声明这个Converter，才能使用.

::

   //local指的是本地的资源，SomeConverter是类,x:key为这个实例的变量名
   //也就是new了一个SomeConverter类，实例名为SomeConverter。
   <Window.Resources>
      <local:SomeConverter x:key="SomeConverter"></local:SomeConverter>
   </Window.Resources>

   Text="{Bingding sometext,Converter={StaticResource SomeConverter}}"




~~~~~~~~~~~~~~~~~~~~~~~~

https://www.wpfsoft.com/binding 有详细讲解