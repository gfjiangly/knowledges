合并多个commit细节较多，单篇参考文章可能叙述不够详细，参考一下几篇文章：

- https://juejin.im/entry/5ae9706d51882567327809d0
- https://segmentfault.com/a/1190000007748862

总结命令如下：

```bash
# git rebase -i  [startpoint]  [endpoint]

# 从HEAD版本开始往过去数3个版本
git rebase -i HEAD~3 

# 指定要合并的其实版本号，3a4226b这个版本不参与合并的
git rebase -i 3a4226b
```

