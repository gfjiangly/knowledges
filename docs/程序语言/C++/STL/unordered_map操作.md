STL中，`map` 对应的数据结构是 **红黑树**。红黑树是一种近似于平衡的二叉查找树，里面的数据是有序的。在红黑树上做查找操作的时间复杂度为 **O(logN)**。而 `unordered_map` 对应 **哈希表**，哈希表的特点就是查找效率高，时间复杂度为常数级别 **O(1)**， 而额外空间复杂度则要高出许多。所以对于需要高效率查询的情况，使用 `unordered_map` 容器。而如果对内存大小比较敏感或者数据存储要求有序的话，则可以用 `map` 容器。 

## 常用操作

```C++
// 头文件
#include <unordered_map>

std::unordered_map<int, std::string> map;
map.insert(std::make_pair(1, "Scala"));
map.insert(std::make_pair(2, "Haskell"));
map.insert(std::make_pair(3, "C++"));
map.insert(std::make_pair(6, "Java"));
map.insert(std::make_pair(14, "Erlang"));

```

其他操作函数基本和 map 相同：

- clear  // 清除 map 中所有元素；
- erase  // 删除 map 中指定位置的元素；
- insert  // 在 map 指定位置添加 pair 类型的元素；
- find // 获取 map 中元素的迭代器；
- begin, end  // map 的正向迭代器的起始位置与终点位置；
  

### find函数

```cpp
iterator find ( const key_type& key );
```

如果key存在，则find返回key对应的迭代器，如果key不存在，则find返回unordered_map::end。因此可以通过

```cpp
map.find(key) == map.end()
```

 来判断，key是否存在于当前的unordered_map中。 

