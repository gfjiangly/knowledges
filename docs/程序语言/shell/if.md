## -n和-z

if [ str1 = str2 ]　　　　　  当两个串有相同内容、长度时为真 
if [ str1 != str2 ]　　　　　 当串str1和str2不等时为真 
if [ -n str1 ]　　　　　　 当串的长度大于0时为真(串非空) 
if [ -z str1 ]　　　　　　　 当串的长度为0时为真(空串) 
if [ str1 ]　　　　　　　　 当串str1为非空时为真

shell 中利用 -n 来判定字符串非空。

错误用法：

```bash
ARGS=$*
if [ -n $ARGS  ]
then
   print "with argument"
fi
print " without argument"
```

不管传不传参数，总会进入if里面。

原因：因为不加""时该if语句等效于if [ -n ]，shell 会把它当成if [ str1 ]来处理，-n自然不为空，所以为正。

正确用法：需要在$ARGS上加入双引号，即"$ARGS".

```bash
ARGS=$*
if [ -n "$ARGS"  ]
then
   print "with argument"
fi
print " without argument"
```



## -f

判断文件是否存在

```bash
if [ -f $ARNOLD_SHARD_TRAIN/result_cur_files.txt ]; then 
	export use_hdfs_dis=1
else 
	export use_hdfs_dis=0 
fi
echo "use_hdfs_dis $use_hdfs_dis"

```



## -d

判断文件夹是否存在

```bash
if [ -d "/data/mmaction/third_party" ]; then
    cp -R /data/mmaction/third_party .
fi
```



## 大小判断

-lt 小于    -eq 等于    -gt 大于

```bash
if [ $# -lt 2 ]; then
  echo "usage: train_video_classification.sh [business] [arguments_script] [--override_any_arguments if_you_want]"
  exit
fi
```

