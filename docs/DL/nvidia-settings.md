此命令可用于查询GPU温度、风扇转速等硬件信息，亦能够控制风扇转速



查询温度：

nvidia-settings -q GPUCoreTemp



使用过程出错：

ERROR: libgtk-3.so.0: cannot open shared object file: No such file or directory
libnvidia-gtk3.so: cannot open shared object file: No such file or directory
libgtk-x11-2.0.so.0: cannot open shared object file: No such file or directory
libnvidia-gtk2.so: cannot open shared object file: No such file or directory


ERROR: A problem occured when loading the GUI library. Please check your installation and library path. You may need to specify this library when calling nvidia-settings. Please run `/usr/bin/nvidia-settings --help` for usage information.



解决办法：安装libgtk-3-0

apt install libgtk-3-0