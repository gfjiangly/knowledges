1 安装命令行客户端

pip install git+https://github.com/shadowsocks/shadowsocks.git@master

这种方式安装的版本比sudo apt install shadowsocks安装的要新

 

2 创建 Shadowsocks 配置文件

创建一个 /etc/shadowsocks/config.json 文件，格式如下

{

​    "server": "服务器 IP 或是域名",

"server_port": 端口号,

​    "password": "密码",

​    "local_address":  "127.0.0.1",

​    "local_port": 1080,

​    "timeout": 300,

​    "method": "加密方式 (chacha20-ietf-poly1305 / aes-256-cfb)",

​    "fast_open" : false

}

注意老版本（apt方式安装的）不支持chacha20-ietf-poly1305加密方式

 

3启动 Shadowsocks

Python 版客户端命令是 sslocal ， Shadowsocks-libev 客户端命令为 ss-local

sudo /usr/local/bin/sslocal -c /etc/shadowsocks/config.json -d start

*（如果客户端不是安装在/usr/local/bin/**目录下，使用which sslocal**查看其位置）*

-d表示将其作为服务在后台运行，这是要root权限的。

同理，停止ss服务命令是：

sudo /usr/local/bin/sslocal -c /etc/shadowsocks.json -d stop

查询sslocal运行情况，可以使用命令：

ps aux | grep sslocal | grep -v “grep”

 

4 设置全局代理

ss使用的代理协议为socks v5，需要将其转换为http/https代理

使用工具：polipo或privoxy（二选一即可）

polipo：

安装：sudo apt-get install polipo

修改polipo的配置文件/etc/polipo/config：

*logSyslog = true*

*logFile = /var/log/polipo/polipo.log*

*proxyAddress = "0.0.0.0"*

*socksParentProxy = "127.0.0.1:1080"*

*socksProxyType = socks5*

*chunkHighMark = 50331648*

*objectHighMark = 16384*

*serverMaxSlots = 64*

*serverSlots = 16*

*serverSlots1 = 32*

重启polipo服务：sudo /etc/init.d/polipo restart

为终端配置http代理：export http_proxy=<http://127.0.0.1:8123/>

测试能够科学上网：curl [www.google.com](http://www.google.com) or *curl ip.gs(**推荐)*

*注意这一步不能用ping* [*www.google.com*](http://www.google.com)*,* *因为肯定是ping**不通的，代理服务器不转发此报文，之前在此浪费了大量时间。*

 

privoxy：

安装：sudo apt-get install privoxy

修改默认配置：sudo nano /etc/privoxy/config

将第 1336 行的注释去掉，变成这样：

​        forward-socks5t   /               127.0.0.1:1080 .

启动 privoxy：sudo service privoxy start

设置全局 HTTP 和 HTTPS 代理：

privoxy 默认开启的监听端口为 8118

export http_proxy='http://localhost:8118'

export https_proxy='https://localhost:8118'

可以将环境变量写入~/.bashrc

然后：source ~/.bashrc

这样对此用户的每个终端就有效

 

 

5 取消代理设置

unset http_proxy

unset https_proxy

或者重开一个终端

或者注释~/.bashrc中export

​                                                  