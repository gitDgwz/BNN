Xamarin环境搭建
=========================

.. warning:: 
    android Studio 按照虚拟设备时候会让你安装HAXM,不要安装,因为你安装了它就会告诉你该技术已经不支持,有安全问题,
    所以直接打开windows服务 hyper-v 就可以。
    

*   visual Studio
    
    *   组件:ASP,NET移动,UWP,Net跨平台,VS扩展,Azure.
    *   windows启用开发者模式
    *   visual Studio installer 修改-单个组件-windows SDK 
    *   将Xamarin SDk修改为android SDK (修改路径或者复制过去,工具-选项-Xamarin-android设置-SDK位置)

*   android Studio

    *   修改SDK 和tools 构建工具版本.
    *   安装android模拟器(SDK API要对应)

*   jetBrains tools -Resharper(未完成.智能提示,现在visual Studio自带智能提示,只不过稍微弱一点)