1. 下载最新版本的code-server安装包，里面包含vscode web版本：https://github.com/cdr/code-server/releases。目前的code-server只支持Linux和Darwin
2. 解压文件&运行。默认监听端口为 8080，需要使用随机密码登录。随机密码太麻烦，传入 PASSWORD 环境变量，来自定义密码。
3. 如果改成80端口可以使用ip直接访问，不需要加端口，http默认80端口，但是监听80端口需要root权限，也就是需要给code-server root权限，这样会导致vscode的terminal使用的是root用户，有一定风险。

```bash
# 下载
wget https://github.com/cdr/code-server/releases/download/v3.5.0/code-server-3.5.0-linux-x86_64.tar.gz
# 解压
tar -xvf code-server-3.5.0-linux-x86_64.tar.gz

# 运行
# ./code-server

# 修改密码和端口号运行
 export PASSWORD="你自己的密码" && nohup ./code-server --host 0.0.0.0 --port 2020 &
```

