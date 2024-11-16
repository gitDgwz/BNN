Avalonia9MVVM
======================

本节没有代码

MVVM的原理

当属性赋值时调用 ``SetProperty`` 方法, ``SetProperty`` 方法来自于 ``ObservableObject`` ,
它会比较新老数据是否相等来判断是否发生了改变,如果改变了则触发 ``OnPropertyChanged`` 事件,
并且 ``[CallerMemberName]`` 会自动将调用的成员名作为参数传入.参数会被用来 ``new PropertyChangedEventArgs`` 并且成为存入它的属性。

当事件 ``OnPropertyChanged`` 被接收到时 ``PropertyChangedEventArgs`` 会被以 ``args`` 参数传入，可以用 ``PropertyChangedEventArgs`` 的属性 ``PropertyName`` 来分别具体是哪个属性发生了改变。

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

属性的变化和列表的变化不同

列表发生变化时候会触发 ``CollectionChanged`` 时间 , 来自于 ``ObservableCollection``

有点复杂

目前只有单个添加


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在MVVM中 前端View会自动订阅ViewModel,使用Binding绑定

