signal
============

**无参信号**

声明信号 ``signal someSignal``

连接信号 ``someSignal.connect(callable)``

发出信号 ``emit_signal("someSignal")``

**有参信号**

声明信号 ``signal someSignal(para)``

#.  在发出时候带上参数  

    连接信号 ``someSignal.connect(callable)``

    发出信号 ``emit_signal("someSignal",para)``

#.  在连接时候绑定参数 

    连接信号 ``someSignal.connect(callable.bind(para))``

    发出信号 ``emit_signal("someSignal")``

.. note:: 

    不要两种方法同时使用,会报错,多出一个参数