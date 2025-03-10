async and await
======================

**不是阻止，而是等待**
--------------------------

**async关键字的作用是允许方法内部使用await来非阻塞地等待异步操作完成，而不会改变方法本身的执行顺序**

await Async方法的时候，仍然不会进行下一步，但并不是阻塞状态，而且等待状态.

可以先执行方法，最后在await.

如果一个方法启动了一个异步操作但不返回任何可等待类型（如 Task 或 Task<T>），
那么它的名称应该以 Begin、Start 或其他类似的动词开头，来表明该方法不会直接返回或抛出操作的结果。

如果他返回值不为Task,那么它无法被await.

**在使用UI线程时候(WPF,WinForms)，可能造成死锁，因为UI是个单线程环境**


|   • CPU密集型：对CPU施加压力并需要大量时间才能完成的重型操作。这类操作的例子包括复杂的数学计算和视频或图像压缩。
|   • I/O密集型：严重依赖于外部和远程资源且涉及显著等待时间的操作。例子包括写入/读取数据库和调用Web API。


async/await 推荐方法
---------------------------

适合I/O操作（数据库查询、文件读写、网络请求等）



Task
-----------------------

适合CPU密集型操作（大量计算、图像处理等）

Task t=new Task(Method);
t.Start();
t.Wait(); 不推荐,会阻塞当前线程,可能造成死锁.

Task.WaitAll(t1,t2,t3) 等待任务。

Task.Run(method) 直接异步执行方法。


//在所有任务完成时完成

Task Task.WhenAll(method1,method2,method3);

//任何一个任务完成时候,返回的是完成的那个Task

Task<Task> Task.WhenAny() 

:: 
    
    //finishedTask是Task<Task>
    Task finishedTask = await Task.WhenAny(breakfastTasks);
    //再次await抛出异常
    await finishedTask;
    breakfastTasks.Remove(finishedTask);

//

~~~~~~~~~~~~~~~~~~~~~~~~~~

异步异常

    **异步的异常同样不会阻塞**


