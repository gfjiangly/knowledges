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

