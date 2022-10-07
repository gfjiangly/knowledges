官方给的指导: https://learn.microsoft.com/en-us/azure/developer/go/configure-visual-studio-code
会遇到更新go tools失败，需要设置go环境代理: https://goproxy.cn/
命令行下配置:
```bash
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
```
然后重新打开vscode进行更新