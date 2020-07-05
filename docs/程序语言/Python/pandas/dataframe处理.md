### 重命名列

两种方式，

一种重命名所有列传入一个list。

```python
# 长度必须与df.shape[0]对齐
df.columns = ['xxx1', 'xxx2']
# 这种一般适用于没有列名的，给个列名
```

还有一种是重命名特定列，传入一个字典。

```python
# 这个是真的"重命名"
# 只需要修改自己想改的列名，不必全部改
df = df.rename(columns={"xxx": "yyy"})
```



### 索引

两种基本索引：按列索引和按行索引。

每种索引又可分为按id索引和按名称索引。





行列切片

```python
# 知道列名情况下
# df.loc[index, column_name],选取指定行和列的数据
df.loc[0,'name'] # 'Snow'
df.loc[0:2, ['name','age']] 		 #选取第0行到第2行，name列和age列的数据, 注意这里的行选取是包含下标的。
df.loc[[2,3],['name','age']] 		 #选取指定的第2行和第3行，name和age列的数据
df.loc[df['gender']=='M','name'] 	 #选取gender列是M，name列的数据
df.loc[df['gender']=='M',['name','age']] #选取gender列是M，name和age列的数据
```



```python
df.iloc[0,0]		#第0行第0列的数据，'Snow'
df.iloc[1,2]		#第1行第2列的数据，32
df.iloc[[1,3],0:2]	#第1行和第3行，从第0列到第2列（不包含第2列）的数据
df.iloc[1:3,[1,2]	#第1行到第3行（不包含第3行），第1列和第2列的数据
```



### 筛选

#### 简单筛选

与numpy类似，使用维度自动扩展和布尔索引，例如：

```python
# 将label列，值为1的所有行筛选出来
df[df["label"] == 1]

# 将label列值为1 并且 num列值大于1 的 所有行筛选出来
df[df["label"] == 1 & df["num"] > 1]
```

#### 复杂筛选

主要是通过传入函数实现

```python
def expired_url(url):
    if 'vdna' in url:
        return True
    else:
        return False

# 用expired_url筛选了符合条件(返回True)行
df = df[['vid', 'time']][df['url'].apply(expired_url)]
```



### 拼接

使用concat函数

```python
df = pd.concat([df_user, dummies_sex, dummies_age, dummies_level], axis=1)
```


参数axis=1表示列拼接（左右拼在一起），axis=0表示行拼接（上下拼在一起）。

要保证每个表单的行数是相同的，并且每一行对应的key也是相同的，列拼接才变得有意义



## shuffle

```python
df = df.sample(frac=1).reset_index(drop=True)
```

Here, specifying `drop=True` prevents `.reset_index` from creating a column containing the old index entries.

See: https://stackoverflow.com/questions/29576430/shuffle-dataframe-rows



### 打印

df.head(10)可以返回前10行，df.tail(10)可以返回最后10行