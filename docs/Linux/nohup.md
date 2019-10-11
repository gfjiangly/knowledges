1.nohup

用途：不挂断地运行命令。

语法：nohup Command [ Arg … ] [　& ]

　　无论是否将 nohup 命令的输出重定向到终端，输出都将附加到当前目录的 nohup.out 文件中。

　　如果当前目录的 nohup.out 文件不可写，输出重定向到 $HOME/nohup.out 文件中。

　　如果没有文件能创建或打开以用于追加，那么 Command 参数指定的命令不可调用。

退出状态：该命令返回下列出口值： 　　

　　126 可以查找但不能调用 Command 参数指定的命令。 　　

　　127 nohup 命令发生错误或不能查找由 Command 参数指定的命令。 　　

　　否则，nohup 命令的退出状态是 Command 参数指定命令的退出状态。



2.&

用途：在后台运行

一般两个一起用

nohup command &

eg:

```bash
`nohup /usr/local/node/bin/node /www/im/chat.js >> /usr/local/node/output.log 2>&1 &`
```



