常用于替换文本（文件）中字符串。

sed 中单引号和双引号是有区别的。单引号不能解析shell中的变量，需要用双引号才能解析。

如果非要用单引号，需要在 修改变量的地方 放一对双引号，然后在双引号外面再加一对单引号

```bash
export new=good

echo "hello old frank" | sed 's/old/${new}/g'
# 输出：hello ${new} frank

echo "hello old frank" | sed "s/old/${new}/g"
# 输出：hello good frank

echo "hello old frank" |sed 's/old/'"${new}"'/g'
# 输出：hello good frank
```

