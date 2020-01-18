## 特性

- 增删。高效。复杂度O(1)
- 查改。O(n)。不支持随机访问， 所以没有 at(pos)和operator[] 

## STL提供的操作

 底层数据结构为双向链表，支持快速增删 

```
insert() 插入一个元素到list中
push_back() 在list的末尾添加一个元素 
push_front() 在list的头部添加一个元素 
erase() 删除一个元素 
pop_back() 删除最后一个元素 
pop_front() 删除第一个元素
clear() 删除所有元素

front() 返回第一个元素
back() 返回最后一个元素 
begin() 返回指向第一个元素的迭代器 
end() 返回末尾的迭代器 

size() 返回list中的元素个数
empty() 如果list是空的则返回true
max_size() 返回list能容纳的最大元素数量

assign() 给list赋值 
get_allocator() 返回list的配置器 
merge() 合并两个list 
rbegin() 返回指向第一个元素的逆向迭代器 
remove() 从list删除元素 
remove_if() 按指定条件删除元素 
rend() 指向list末尾的逆向迭代器 
resize() 改变list的大小 
reverse() 把list的元素倒转 
sort() 给list排序 
splice() 合并两个list 
swap() 交换两个list 
unique() 删除list中重复的元素
```



