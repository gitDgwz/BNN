Avalonia20异步函数与LINQ
===============================


public ObservableCollection<PoetryFavorite> PoetryFavoriteCollection { get; }= new(); 
为什么他还是可以赋值

因为PoetryFavoriteCollection不可以在赋值，但是后面那个集合可以变，{get;}指的是PoetryFavoriteCollection

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: 

    bilibili东北大学张引，Avalonia 20：异步函数与LINQ,38分钟


::
  
    var poetryFavorites = (await Task.WhenAll(
        favoriteList.Select(
            p => Task.Run(async () => new PoetryFavorite()
            {
                Poetry = await _poetryStorage.GetPoetryAsync(p.PoetryId),
                Favorite = p
            })))).ToArray();


为什么写成这样?

因为Task具有传染性，必须使用await.

但是LINQ传递的是方法，它还是Task；

LINQ官方包内没有异步LINQ，在实验包内，

所以要主动执行方法才能求出Poetry


:: 
    
    //方法传递还是Task 

    public async Task<Poetry> Do
    {
        return await _poetryStorage.GetPoetryAsync(p.PoetryId);
    }

