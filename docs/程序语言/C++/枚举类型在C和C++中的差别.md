枚举类型是被当作常量来看待的。

C和C++里面常量不太一样：

- C语言的常量和普通变量没什么两样，都是在栈里面，而且是可寻址的（也就是除了不能更改以外和普通变量没有区别） 
- 而C++的常量在编译时被放入符号表里面，如果没有显式要求，其将是不可寻址的



## 参考

- https://blog.csdn.net/beckle_ye/article/details/6279545
- 