## 返回列数：

```python
df.shape[1]
```



## 返回行数：

```python
df.shape[0]
```



## 获取列名

*DataFrame*.columns.values.tolist()



## index

*DataFrame*.index

重置索引

```python
DataFrame.reset_index(level=None, drop=False, inplace=False, col_level=0, col_fill=”) 
# drop为False则索引列会被还原为普通列，否则会丢失
df = df.reset_index(drop=True)

DataFrame.set_index(keys, drop=True, append=False, inplace=False, verify_integrity=False) 
df.set_index('date', inplace=True) # column -> index
```



查看数据类型

```python
df.dtypes
```

