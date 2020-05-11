# SOCKET 介绍

- 应用程序创建一个socket。socket是一个允许通信的设备，两个
应用程序都需要用到它。
- 服务器将自己的socket绑定到一个众所周知的地址上使得客户端能够定位到
它的位置。

使用socket() 系统调用能够创建一个socket，它返回一个用来在后续
系统调用中引用该socket的文件描述符。

fd = socket(domain, type, protocol);

# Domain



