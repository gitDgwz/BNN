WPF
==================

.. toctree:: 
    :glob:

    *


WPF和Avalonia用的技术差不多，惊人的相识.

基础知识
---------------------------

在WPF中 DispatcherObject是最基类.

Content是Windows窗体的内容

WPF中五个经典fulei

    *   DispatcherObject
    *   DependencyObject
    *   Visual
    *   UIElement
    *   FrameworkElement

WPF有两个线程，UI线程和后台线程

    **后台子线程不能直接访问UI线程控件**

    **Dispatcher** 可以让我们在后台线程中安全的访问UI线程中的控件.