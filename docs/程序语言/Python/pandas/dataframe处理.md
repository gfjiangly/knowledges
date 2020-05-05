### 重命名列

两种方式，一种重命名所有列传入一个list。df.columns = ['xxx', 'xxx']。还有一种是重命名特定列，传入一个字典。df.rename(columns={"xxx": "yyy"})



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

#### 筛选行

与numpy类似，使用维度自动扩展和布尔索引，例如：

```python
# 将label列，值为1的所有行筛选出来
df[df["label"] == 1]

# 将label列值为1 并且 num列值大于1 的 所有行筛选出来
df[df["label"] == 1 & df["num"] > 1]
```



筛选列





### 打印

df.head(10)可以返回前10行，df.tail(10)可以返回最后10行