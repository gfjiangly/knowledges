设置默认启动方式（持久)

```bash
sudo systemctl set-default multi-user.target	# 命令行
sudo systemctl set-default graphical.target		# 图形界面
```



启用/禁用图形界面（临时）

```bash
sudo service lightdm start	# 启用图像界面
sudo service lightdm stop	# 禁用图形界面
```



连接服务器的显示器命令行/图像界面切换：

```bash
Ctrl+Alt+F1-F6	# 进入命令行
Ctrl+Alt+F7		# 进入图像界面
```



远程图像界面

大多数时候我们不希望在服务器上安装图形界面，但有时候有些程序需要图形界面，比如安装Oracle的时候。此时，可以配置让Linux使用远程的X Server进行图形界面显示。

首先要明确的是Linux X Window System的基本原理，X是一个开放的协议规范，当前版本为11，俗称X11。X Window System由客户端和服务端组成，服务端X Server负责图形显示，而客户端库X Client根据系统设置的DISPLAY环境变量，将图形显示请求发送给相应的X Server。

因此，我们只需要在远端开启一个X Server，并在目标机器上相应的设置DISPLAY变量，即可完成图形的远程显示。



使用命令行查看图片：

```bash
eog pic_path
# 或者
display pic_path
```

