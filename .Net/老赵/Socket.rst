Socket
==============

ip 和 端口号
电脑 和 应用程序

协议
tcp 三次握手

udp 广播

~~~~~~~~~~~~~~~~~

Socket服务器
-----------------

SocketWactch 监管,专门用来接收连接，不管理通信.

    创建Socket 需要三个参数,IPV4协议，stream，TCP

    ``Socket Server = new(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp)``

    然后需要 ``bind(Endpoint)`` 绑定监听ip和端口请求

    ``Endpoint`` 是网络端点,由ip和port(端口)组成

        ``Endpoint(IPAddress ip,Int port)`` 

        ``IPAddress`` 由静态类得到 ``IPAddress.Any``，端口自己写个数字

    然后设置 ``listen(Int num)`` 最大监听(连接)数

Socket连接
--------------------

Socket.accept() 由SocketWactch接收,服务端接收并返回一个新的Socket作为通信Socket

Socket.acceptAsync() 异步接收连接

Socket.Connect(Endpoint) 由客户端的Socket调用,客户端自己new Socket，填好服务器绑定的的Endpoint.

Socket通信
----------------------------

Socket.send() 发送消息，需要一个byte[],可以用 ``Encoding.UTF8.GetBytes(string)`` 把string转为byte[];

Socket.sendAsync() 异步发送

Socket.receive()用来接收信息,需要一个byte[]，他会把信息填充到byte[]中, ``Encoding.UTF8.GetString(byte[])`` 最后转为字符串

Socket.receiveAsync() 异步接收


Encoding.UTF8
-------------------------

``Encoding.UTF8.GetBytes(string)`` 把string转成byte[]

``Encoding.UTF8.GetBytes(string,intIndex,length)`` 转化string从第几个string开始，转化多少个string

``Encoding.UTF8.GetString(byte[])`` 把byte[]转成string

``Encoding.UTF8.GetString(byte[],intIndex,length)`` 转化byte[]从第几个byte开始，转化多少个byte

~~~~~~~~~~~~~~~~~~~~~
IPAdress.Any指的任意ip，0.0.0.0:5000 的意思是任意IP对5000端口的请求，而不是任意ip的5000端口.