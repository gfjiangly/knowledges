安装：sudo apt-get install tmux



#### 新建会话

```bash
tmux # 新建一个无名称的会话
tmux new -s demo # 新建一个名称为demo的会话
```



#### 断开当前会话

会话中操作了一段时间，我希望断开会话同时下次还能接着用，怎么做？此时可以使用detach命令。

```bash
tmux detach # 断开当前会话，会话在后台运行
```

也许你觉得这个太麻烦了，是的，tmux的会话中，我们已经可以使用tmux快捷键了。使用快捷键组合`Ctrl+b` + `d`，三次按键就可以断开当前会话。



#### 进入之前的会话

断开会话后，想要接着上次留下的现场继续工作，就要使用到tmux的attach命令了，语法为`tmux attach-session -t session-name`，可简写为`tmux a -t session-name` 或 `tmux a`。通常我们使用如下两种方式之一即可：

```bash
tmux a # 默认进入第一个会话
tmux a -t demo # 进入到名称为demo的会话
```



#### 关闭会话

会话的使命完成后，一定是要关闭的。我们可以使用tmux的kill命令，kill命令有`kill-pane`、`kill-server`、`kill-session` 和 `kill-window`共四种，其中`kill-session`的语法为`tmux kill-session -t session-name`。如下：

```bash
tmux kill-session -t demo # 关闭demo会话
tmux kill-server # 关闭服务器，所有的会话都将关闭
```



#### 查看所有的会话

管理会话的第一步就是要查看所有的会话，我们可以使用如下命令：

```bash
tmux list-session # 查看所有会话
tmux ls # 查看所有会话，提倡使用简写形式
```

如果刚好处于会话中怎么办？别担心，我们可以使用对应的tmux快捷键`Ctrl+b` + `s`，此时tmux将打开一个会话列表，按上下键(⬆︎⬇︎)或者鼠标滚轮，可选中目标会话，按左右键（⬅︎➜）可收起或展开会话的窗口，选中目标会话或窗口后，按回车键即可完成切换。





参考：

- http://louiszhai.github.io/2017/09/30/tmux/