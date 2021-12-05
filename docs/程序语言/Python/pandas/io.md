DataFrame 数据的保存和读取

df.to_csv 写入到 csv 文件
pd.read_csv 读取 csv 文件
df.to_json 写入到 json 文件
pd.read_json 读取 json 文件
df.to_html 写入到 html 文件
pd.read_html 读取 html 文件
df.to_excel 写入到 excel 文件
pd.read_excel 读取 excel 文件





 pd.read_csv



## df.to_csv

无索引，加参数：header=False

```python
DataFrame.to_csv(path_or_buf=None, sep=', ', na_rep='', float_format=None, columns=None, header=True,
                 index=True, index_label=None, mode='w', encoding=None, compression=None,
                 quoting=None, quotechar='"', line_terminator='\n', chunksize=None,
                 tupleize_cols=None, date_format=None, doublequote=True,escapechar=None, decimal='.')
```

参数：

- path_or_buf : 文件路径，如果没有指定则将会直接返回字符串的 json
- sep : 输出文件的字段分隔符，默认为 “,”
- na_rep : 用于替换空数据的字符串，默认为''
- float_format : 设置浮点数的格式（几位小数点）
- columns : 要写的列
- header : 是否保存列名，默认为 True ，保存
- index : 是否保存索引，默认为 True ，保存
- index_label : 索引的列标签名







https://blog.csdn.net/tz_zs/article/details/81137998