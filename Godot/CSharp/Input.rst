Input
====================

事件顺序
----------------------

大致顺序

_Input()->_GuiInput()->_UnhandledInput();

但是只能检测一次,如果要持续检测,还是需要放到Process中.




事件监听
------------------------------------

node

_Input()

_UnhandledInput()

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Control

_GuiInput()

仅仅能监听到鼠标事件(InputEventMouse)，按键无法监听.






事件处理
---------------------------------------

node

``GetViewport().SetInputAsHandled()``

处理事件

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Control使用

``AcceptEvent()``

处理事件


