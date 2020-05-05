C++重载的`[]`只有索引单个字符功能，没有切片功能。

### 插入字符或字符串

string的成员函数insert有以下多种重载：

```cpp
// 在p0处插入n个字符c
string &insert(int p0, int n, char c);  // 较为常用
// 在p0位置插入字符串s
string &insert(int p0, const char *s);
// 在p0位置插入字符串s的前n个字符
string &insert(int p0, const char *s, int n);
// 在p0位置插入字符串s
string &insert(int p0, const string &s);
// 在p0位置插入字符串s从pos开始的连续n个字符
string &insert(int p0, const string &s, int pos, int n);

// 在it处插入字符c，返回插入后迭代器的位置
iterator insert(iterator it, char c);
// 在it处插入从first开始至last-1的所有字符
void insert(iterator it, const_iterator first, const_iteratorlast);
// 在it处插入n个字符c
void insert(iterator it, int n, char c);
```

### 截取子串

```C++
s.substr(pos, n)     // 截取s中从pos开始（包括0）的n个字符的子串，并返回
s.substr(pos)        // 截取s中从从pos开始（包括0）到末尾的所有字符的子串，并返回
```

### 替换子串

```C++
s.replace(pos, n, s1)    // 用s1替换s中从pos开始（包括0）的n个字符的子串
```

### 查找子串

```C++
s.find(s1)               // 查找s中第一次出现s1的位置，并返回（包括0）
s.rfind(s1)              // 查找s中最后次出现s1的位置，并返回（包括0）
s.find_first_of(s1)      // 查找在s1中任意一个字符在s中第一次出现的位置，并返回（包括0）
s.find_last_of(s1)       // 查找在s1中任意一个字符在s中最后一次出现的位置，并返回（包括0）
s.fin_first_not_of(s1)   // 查找s中第一个不属于s1中的字符的位置，并返回（包括0）
s.fin_last_not_of(s1)    // 查找s中最后一个不属于s1中的字符的位置，并返回（包括0）
```

### 打印

cout直接可打印，printf输出乱码，因为printf里面要求是char*类型

```C++
printf("%s", str.c_str());
```

