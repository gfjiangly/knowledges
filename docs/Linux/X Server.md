大多数时候我们不希望在服务器上安装图形界面，但有时候有些程序需要图形界面，比如安装[Oracle](http://www.linuxidc.com/topicnews.aspx?tid=12)的时候。此时，可以配置让Linux使用远程的X Server进行图形界面显示。

首先要明确的是Linux X Window System的基本原理，X是一个开放的协议规范，当前版本为11，俗称X11。X Window System由客户端和服务端组成，服务端X Server负责图形显示，而客户端库X Client根据系统设置的DISPLAY环境变量，将图形显示请求发送给相应的X Server。

因此，我们只需要在远端开启一个X Server，并在目标机器上相应的设置DISPLAY变量，即可完成图形的远程显示。

