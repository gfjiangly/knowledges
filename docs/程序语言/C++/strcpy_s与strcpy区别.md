这个主题是想讨论新的增强安全的CRT函数与老的CRT函数的差别。

简言之：增强安全的CRT函数对参数合法性做更多的检查

### strcpy_s

strcpy_s对参数的缓冲区边界做了检查。

```cpp
char * p = new char[6];
strcpy_s(p, 5, "hello, jang");  // 抛出异常
strcpy(p, "hello, jang");  // 直接拷贝。操作了不属于p的内存，破坏其它内存，可能被恶意软件利用

strcpy_s(NULL, "hello, jiang");  // 抛出异常
```

### strcpy实现

```cpp
// 1 函数签名是什么？
// 2 需要返回值吗？
// 3 需要边界检查吗？怎么检查？
// 4 非法输入有哪些？异常或错误怎么通知调用者？
int G_ERROR = 0;
char * strcpy(char * dst, const char * src) {
    if (dst == NULL) return NULL;
    if (src == NULL) return p;
    char * dst_copy = p;
    while ((*(dst++) = *(src++)) == '\0');  // '\0'也要拷贝
    return dst_copy;
}
```

- src指针不需要修改，因此参数应修饰为const；
- 需要返回char*以支持链式表达式
- 可以考虑内存覆盖情况，缓冲区溢出情况在现有函数签名下无法在函数内部考虑
- C++可以使用assert断言，C语言可以使用全局变量标记错误

