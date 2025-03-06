Control
==================


控件分为布局控件(Panel)和内容控件(Control)


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Panel
------------

ZIndex 设置谁先谁后,大的在上.如果不设置，后面声明的控件在上.

**布局控件的Background属性没有值时，是不能引发鼠标相关事件的。**

Grid
^^^^^^^^^^^^^^^

ColumnDefinitionCollection 类型的ColumnDefinitions属性

Grid.ColumnSpan 跨列属性

RowDefinitionCollection 类型的RowDefinitions属性

Grid.RowSpan 跨行属性

ShowGridLines显示网格（但是是虚线,可以使用Border的来显示边框)


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Control
------------------
Control 基类

提供了一个ControlTemplate类型的Template属性,用来给子类实现.

ContentControl
^^^^^^^^^^^^^^^^^^^^^^^^^^

public class ContentControl : Control, IAddChild

Control的子类

有一个DataTemplate类型的ContentTemplate属性(内容模板)





ItemsControl
^^^^^^^^^^^^^^^^^^^^^^^^^^^
ItemsControl类, 集合控件

public class ItemsControl : Control, IAddChild, IGeneratorHost, IContainItemStorage

有ItemCollection类型的Items属性

DataTemplate类型的ItemTemplate属性


~~~~~~~~~~~~~~~~~~~~

Label 标签

TextBlock 只读文本

TextBox 可以编辑文本

PassWordBox 密码框

RadioButton 单选按钮

CheckBox 选择按钮，复选

ComboBox 单选列表

ListBox 每行只能显示一个数据项,可以设置单选和多选

ListView 每行可以显示多个数据项，数据列表，主要为展现数据.

DataGrid 强大的数据表格，可以编辑，选择等待

