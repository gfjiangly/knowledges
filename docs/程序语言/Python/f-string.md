注意只有3.6及以上版本Python有f-string语法



## 跨多行写法

```python
#!/usr/bin/env python3

name = 'John Doe'
age = 32
occupation = 'gardener'

msg = (
    f'Name: {name}\n'
    f'Age: {age}\n'
    f'Occupation: {occupation}'
)

print(msg)
```

