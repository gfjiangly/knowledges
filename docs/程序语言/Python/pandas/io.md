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

无索引，加参数：header=None



df.to_csv

参数：

index=None  去掉行索引，默认有

encoding="utf-8" 设置保存的编码，read_csv也有这个参数

seq=',' 分隔符，默认是逗号





https://blog.csdn.net/tz_zs/article/details/81137998