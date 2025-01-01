Avalonia17内容导航与参数传递
==============================

*   dpa.Library Services 添加一个BingImageService
    ::

        里面有三个类

        
        //BingImageService:ITodayImageService
        GetTodayImageAsync()
        转给ITodayImageStorage获得图片

        CheckUpdateAsync()
        获得图片信息，不含二进制，比较过期时间（单纯比较是否过了一天）。
        如果没过期，返回一个包含没过期信息的类
        如果过期，访问Http并且反序列化，比较两张图片的启用时间。
        如果网络图片为旧的则加2个小时过期时间，并且更新图片信息。
        如果网络图片为新则获取URL得到图片。
        并且将二进制保存，并且给ITodayImageStorage储存图片。


        //BingImageOfTheDay
        存在BingImageOfTheDayImage的集合
    

        //BingImageOfTheDayImage
        Bing网页信息的反序列化类


*   dpa Converters 创建ByteArrayToBitmapConverter.cs
    ::

        把结果转为 new Bitmap(new MemoryStream(bytes))

*   ServiceLocator添加 ``ITodayImageService,BingImageService`` , ``ITodayImageStorage,TodayImageStorage`` 依赖注入


*   dpa Converters NegativeConverter.cs
    ::

        取反的Converter



~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Task.Run(); 异步执行











~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
由于过多的代码粘贴影响阅读，从本节开始不粘代码。