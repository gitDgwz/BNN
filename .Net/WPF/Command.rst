Command
===============

ICommand

RoutedCommand

RoutedUICommand

~~~~~~~~~~~~~~~~~~~

命令 ICommand

    里面有 ``CanExecute``  ``Execute``两个方法和 ``CanExecuteChanged`` 事件.

命令源 ICommandSource

命令目标 

命令绑定


~~~~~~~~~~~~~~~~~~~~~~~~~~
实际使用

写一个自定类RelayCommand继承于ICommand

::

    public class RelayCommand: ICommand
    {

        public event EventHandler? CanExecuteChanged;

        private Action _action;
        private Action<object> _stringAction;

        public RelayCommand(Action action)
        {
            _action = action;
        }

        public RelayCommand(Action<object> stringAction)
        {
            _stringAction = stringAction;
        }
        
        public bool CanExecute(object? parameter)
        {
            return true;
        }

        public void Execute(object? parameter)
        {
            _action?.Invoke();
            _stringAction?.Invoke(parameter);
        }
    }

其实就是具体实现了一下自带的两个方法和多写了两个Action,两个构造函数.带泛型的Action就是带参数的Command用法