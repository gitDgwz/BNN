Socket
==============

ip 和 端口号
电脑 和 应用程序

协议
tcp 三次握手

udp 广播

~~~~~~~~~~~~~~~~~

Socket监管

创建socket 需要三个参数,IPV4协议，stream，TCP

然后需要bind Endpoint（IP和端口）

IP由静态类得到，端口自己写

然后设置listen最大数

socket.accept()接收并返回一个新的socket作为通信Socket

socket.receive()用来接收信息


