Control
==================



Control
------------------
Control 基类

提供了一个ControlTemplate类型的Template属性,用来给子类实现.

ContentControl类
^^^^^^^^^^^^^^^^^^^^^^^^^^

public class ContentControl : Control, IAddChild

Control的子类

有一个DataTemplate类型的ContentTemplate属性(内容模板)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
集合控件

ItemsControl
^^^^^^^^^^^^^^^^^^^^^^^^^^^
ItemsControl类

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

DataGrid 强大的数据表格，可以编辑，选择等待

ListView 数据列表，主要为展现数据.
