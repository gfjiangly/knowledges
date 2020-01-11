 头文件：`#include<vector>` 

### 基本操作

vector是最常用的STL数据结构之一，基本操作有点多。

```C++
vector<int> vec;
// 插入元素
vec.push_back(a);	// 在末尾插入元素a
vec.insert(vec.begin()+i, a);	// 在第i+1个元素前面插入a;

// 删除元素
vec.pop_back();		// 删除末尾元素，无返回值
vec.erase(vec.begin()+2);	// 删除第3个元素

// 大小
vec.size();
```

### 初始化

```C++
vector<int> ilist1;
```

(1): vector<int> ilist1;

默认初始化，vector为空， size为0，表明容器中没有元素，而且 capacity 也返回 0，意味着还没有分配内存空间。这种初始化方式适用于元素个数未知，需要在程序中动态添加的情况。

```C++
vector<int> ilist2(ilist);
vector<int> ilist2  = ilist; 
```

两种方式等价 ，ilist2初始化为ilist的拷贝，ilist必须与ilist2类型相同，也就是同为int的vector类型，ilist2将具有和ilist相同的容量和元素

```C++
vector<int> ilist = {1,2,3.0,4,5,6,7};
vector<int> ilist {1,2,3.0,4,5,6,7};
```

ilist 初始化为列表中元素的拷贝，列表中元素必须与ilist的元素类型相容，本例中必须是与整数类型相容的类型，整形会直接拷贝，其他类型会进行类型转换。

```C++
vector<int> ilist3(ilist.begin()+2,ilist.end()-1);
```

ilist3初始化为两个迭代器指定范围中元素的**拷贝**，范围中的元素类型必须与ilist3 的元素类型相容，在本例中ilist3被初始化为{3,4,5,6}。注意：由于只要求范围中的元素类型与待初始化的容器的元素类型相容，因此迭代器来自不同的容器是可能的，例如，用一个double的list的范围来初始化ilist3是可行的。另外由于构造函数只是读取范围中的元素进行拷贝，因此使用普通迭代器还是const迭代器来指出范围并没有区别。这种初始化方法特别适合于获取一个序列的子序列。

```C++
vector<int> ilist4(7);
```

默认值初始化，ilist4中将包含7个元素，每个元素进行缺省的值初始化，对于int，也就是被赋值为0，因此ilist4被初始化为包含7个0。当程序运行初期元素大致数量可预知，而元素的值需要动态获取的时候，可采用这种初始化方式。

```C++
vector<int> ilist5(7, 3);
```

 指定值初始化，ilist5被初始化为包含7个值为3的int 



### 迭代

```C++
vector<int> vec;
// 通过下标
for (int i = 0; it < vec.size(); ++i) {
    auto elem = vec[i];
}

// 通过迭代器
for (auto it = vec.begin(); it != vec.end(); it++) {
    // 尽量不要在迭代过程删除元素，防止迭代器失效
    cout<<*it <<endl;
}

```



### 清空元素 

```C++
vector<int> v;

// 不收回空间
v.clear();

// 回收内存
v.swap(vector<int>()) ;
```



### 判断某一元素是否存在于vector中

```C++
bool is_in_vector(vector<int> v, int element){
    if (find(v.begin(), v.end(), element) != v.end()) return true;
    else return false;
}
```



### 把一个vector里的元素追加放入另外一个vector

```C++
vector<int> src;
vector<int> dest;
dest.insert(dest.end(), src.begin(), src.end());
```


插到其他位置同理，只需要改变第一个迭代器参数。



