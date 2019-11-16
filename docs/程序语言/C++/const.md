### const char *ptr;*

* 定义一个指向字符常量的指针，这里，ptr是一个指向 char* 类型的常量，所以不能用ptr来修改所指向的内容，换句话说，*ptr的值为const，不能修改。但是ptr的声明并不意味着它指向的值实际上就是一个常量，而只是意味着对ptr而言，这个值是常量。实验如下：ptr指向str，而str不是const，可以直接通过str变量来修改str的值，但是确不能通过ptr指针来修改。

 ![img](https://img-blog.csdn.net/20170809161324271) 



###  char const \*ptr;
​    此种写法和const char *等价 



###  char \* const ptr;

​    定义一个指向字符的指针常数，即const指针，实验得知，不能修改ptr指针，但是可以修改该指针指向的内容。实验如下：

 ![img](https://img-blog.csdn.net/20170809161047098) 