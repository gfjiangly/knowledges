在有桌面的Linux系统上：Display Manager (DM)管理X Client，X Client负责将数据发送给用户的X Server显示。通过启动DM（或称登录管理器），即可进行图形界面登录。

Xorg是X11(协议)的一个实现，而X Window System是一个C/S结构的程序，Xorg只是提供了一个X Server，负责底层的操作当你运行一个程序的时候，这个程序会连接到X server上，由X Server接收键盘鼠标输入和负责屏幕输出窗口的移动，窗口标题的样式等等，因此X Server运行在用户端。

在一般的server/client模型中，server总是代表性能比较高，处理很多数据的那个机器，比如你登录一个ftp站点或者是某个网站的时候，server指的是对方的系统。但是在X的server/client模型中恰好相反，这里面server仅仅负责鼠标、键盘、显卡、显示器这些输入输出部件，而x-client却是负责处理数据的。X-server负责管理鼠标、键盘、显卡、显示器，而X-client则负责处理程序的运行。比如说，我桌面上有一个gvim的图标，我现在移动鼠标点击了一下gvim的图标，那么X-server会告诉X-client：“用户移动了鼠标到什么位置，然后单击了一下鼠标的左键”。X-client收到这个信息以后会对这个行为做出反应，经过一番思考，它发现，我这个行为是要打开一个叫gvim的程序，于是X-client会运行gvim，然后告诉X-server：“你在显示器上给用户画出一个像gvim的东西来”。然后，我就在我的屏幕上很高兴地看见了我可爱的gvim编辑器了，当然还有伟大的DejaVu Sans Mono等宽字体。

目前DM主要有：gdm和lightdm。LightDM是2010年开始的新项目，一开始就设计为轻量、小巧，没有GDM那样的历史代码负担，因此不仅完整支持标准，而且轻量、快速。GDM (The GNOME Display Manager)是GNOME显示环境的管理器，并被用来替代原来的X Display Manager。与其竞争者(X3DM,KDM,WDM)不同，GDM是完全重写的，并不包含任何XDM的代码。**服务器上没有必要装DM，DM加一些带图像界面的软件程序可以组成一套类似于Windows的桌面使用。**

综上，DM和X Client运行在服务器端，而X Server运行在用户端（当然也可运行在服务器上，如果服务器连接了显示器）。当然，以上所说，都是运行在Linux系统上程序。其中X Server可运行在Windows上，即可通过X Server在Windows上显示服务器上程序的图像界面。

![img](https://upload-images.jianshu.io/upload_images/5461542-207fcad25ef04fd8.png?imageMogr2/auto-orient/strip|imageView2/2/w/250/format/webp)

之间关系梳理参考：

https://jin-yang.github.io/post/linux-xwindows-introduce.html

https://baike.baidu.com/item/xorg

远程显示服务器程序图像界面

大多数时候我们不希望在服务器上安装图形界面，但有时候有些程序需要图形界面，比如安装Oracle的时候。此时，可以配置让Linux使用远程的X Server进行图形界面显示。

首先要明确的是Linux X Window System的基本原理，X是一个开放的协议规范，当前版本为11，俗称X11。X Window System由客户端和服务端组成，服务端X Server负责图形显示，而客户端库X Client根据系统设置的DISPLAY环境变量，将图形显示请求发送给相应的X Server。

因此，我们只需要在远端开启一个X Server，并在目标机器上相应的设置DISPLAY变量，即可完成图形的远程显示。


使用命令行查看图片：

```bash
eog pic_path
# 或者
display pic_path
```


以下命令仅供含桌面的Linux使用

1 设置默认启动方式（持久)

```bash
sudo systemctl set-default multi-user.target	# 命令行
sudo systemctl set-default graphical.target		# 图形界面
```

2 启用/禁用图形界面（临时）

```bash
sudo service lightdm start	# 启用图像界面
sudo service lightdm stop	# 禁用图形界面
```

3 连接服务器的显示器命令行/图像界面切换：

```bash
Ctrl+Alt+F1-F6	# 进入命令行
Ctrl+Alt+F7		# 进入图像界面
```

4 查看DM服务状态

```bash
# lightdm
systemctl status lightdm.service	# or service lightdm status
# gdm3
systemctl status gdm3.service		# or service gdm3 status
```

