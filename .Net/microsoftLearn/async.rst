async and await
======================

**不是阻止，而是等待**
--------------------------

await Async方法的时候，仍然不会进行下一步，但并不是阻塞状态，而且等待状态.

可以先执行方法，最后在await.

异步异常
-------------------
异步的异常同样不会阻塞


~~~~~~~~~~~~~~~~~~~~~~~~~~

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

Task.run()