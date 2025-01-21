Avalonia 21：事件与消息
===============================

事件和消息
--------------------------------------------

事件::

    //声明
    event EventHandler<Favorite> Update;

    //FavoriteViewModel.cs
    //构造函数
    _favoriteStorage.Update+=FavoriteStorageOnUpdated

    //FavoriteStorageOnUpdated
    private async void FavoriteStorageOnUpdated(object sender, Favorite e)
    {
        var objectToDelete = PoetryFavoriteCollection.FirstOrDefault(p =>
            p.Favorite.PoetryId == e.PoetryId);
        PoetryFavoriteCollection.Remove(objectToDelete);

        if (!e.IsFavorite)
        {
            return;
        }

        var poetryFavorite = new PoetryFavorite
        {
            Poetry = await _poetryStorage.GetPoetryAsync(e.PoetryId),
            Favorite = e
        };
        
        PoetryFavoriteCollection.Insert(0,poetryFavorite);
    }

    //FavoriteStorage.cs
    //声明
    public event EventHandler<Favorite> Updated;

    //调用
    Updated?.Invoke(this,favorite);

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


消息::

    //声明
    public class FavoriteStorageUpdatedMessage : ValueChangedMessage<Favorite>
    {
        public FavoriteStorageUpdatedMessage(Favorite value) : base(value)
        {
        }
    }

    //FavoriteViewModel.cs
    //继承
    public class FavoriteViewModel: ViewModelBase,IRecipient<FavoriteStorageUpdatedMessage>

    //在构造函数里面注册
    WeakReferenceMessenger.Default.Register<FavoriteStorageUpdatedMessage>(this);

    //接收函数
    public async void Receive(FavoriteStorageUpdatedMessage message)
    {
        var e = message.Value;
        var objectToDelete = PoetryFavoriteCollection.FirstOrDefault(p =>
            p.Favorite.PoetryId == e.PoetryId);
        PoetryFavoriteCollection.Remove(objectToDelete);

        if (!e.IsFavorite)
        {
            return;
        }

        var poetryFavorite = new PoetryFavorite
        {
            Poetry = await _poetryStorage.GetPoetryAsync(e.PoetryId),
            Favorite = e
        };
        
        PoetryFavoriteCollection.Insert(0,poetryFavorite);
    }

    //FavoriteStorage.cs
    //发送
    WeakReferenceMessenger.Default.Send(new FavoriteStorageUpdatedMessage(favorite));




列表删除
-------------------------

列表删除外来对象，即使值想等也是两个不同的对象，于是无法删除。

::

    //使用FirstOrDefault查找对应的对象
     PoetryFavoriteCollection.FirstOrDefault(p =>p.Favorite.PoetryId == e.PoetryId);