​	RAII是resource acquisition is initialization的缩写，意为“资源获取即初始化”。它是C++之父Bjarne Stroustrup提出的设计理念，其核心是把资源和对象的生命周期绑定，对象创建获取资源，对象销毁释放资源。在RAII的指导下，C++把底层的资源管理问题提升到了对象生命周期管理的更高层次。

​	说起来，RAII的含义倒也不算复杂。用白话说就是：在类的构造函数中分配资源，在析构函数中释放资源。这样，当一个对象创建的时候，构造函数会自动地被调用；而当这个对象被释放的时候，析构函数也会被自动调用。于是乎，一个对象的生命期结束后将会不再占用资源，资源的使用是安全可靠的。

C++ RAII体现出了简洁、安全、实时的特点：

1.概念简洁性：让资源（包括内存和非内存资源）和对象的生命周期绑定，资源类的设计者只需用在类定义内部处理资源问题，提高了程序的可维护性
2.类型安全性：通过资源代理对象包装资源（指针变量），并利用运算符重载提供指针运算方便使用，但对外暴露类型安全的接口
3.异常安全性：栈语义保证对象析构函数的调用，提高了程序的健壮性
4.释放实时性：和GC相比，RAII达到了和手动释放资源一样的实时性，因此可以承担底层开发的重任

也许你还在惊讶RAII如此简单的时候，关于RAII的主要内容已经介绍完了。简单不意味着简陋，在我看来RAII虽然不像GC一样，是一套具体的机制，但它蕴含的对象与资源关系的哲学深度的理解却使得我对Bjarne Stroustrup肃然起敬！

最后，不得不提醒RAII的理念固然简单，不过在具体实现的时候仍有需要小心的地方。比如对于STL的auto_ptr，可以视为资源的代理对象，auto_ptr对象间的赋值是一个需要特别注意的地方。简单说来资源代理对象间赋值的语义不满足“赋值相等”，其语义是资源管理权的转移。

什么是“赋值相等”呢？比如：

int a;  int b = 10;  a = b; //这句话执行后 a == b 但对于资源代理对象，这是不满足的，比如：

auto_ptr<int> a(null);  auto_ptr<int> b(new int(123));  a = b; //这句话执行后a != b，赋值的语义是b把资源的管理权交给了a 

auto_ptr是这样一种指针：它是“它所指向的对象”的拥有者。这种拥有具有唯一性，即一个对象只能有一个拥有者，严禁一物二主。*当auto_ptr指针被摧毁时，它所指向的对象也将被隐式销毁，即使程序中有异常发生，auto_ptr所指向的对象也将被销毁*。


关于auto_ptr的几种注意事项：
1、auto_ptr不能共享所有权。
2、auto_ptr不能指向数组
3、auto_ptr不能作为容器的成员。
4、不能通过赋值操作来初始化auto_ptr
std::auto_ptr<int> p(new int(42));     //OK
std::auto_ptr<int> p = new int(42);    //ERROR
这是因为auto_ptr 的构造函数被定义为了explicit
5、不要把auto_ptr放入容器

下面便是在C++中实现RAII的典型代码：
class file
{
public:
    file(string const& name) {
        m_fileHandle=fopen(name.cstr());
    }
    ~file() {
        fclose(m_fileHandle);
    }
    //![img](http://www.cppblog.com/Images/dot.gif)
private:
    handle m_fileHandle;
}

很典型的“在构造函数里获取，在析构函数里释放”。如果我写下代码：   
void fun1() ![img](http://www.cppblog.com/Images/dot.gif){
    file myfile("my.txt");
    ![img](http://www.cppblog.com/Images/dot.gif) //操作文件
} //此处销毁对象，调用析构函数，释放资源
当函数结束时，局部对象myfile的生命周期也结束了，析构函数便会被调用，资源会得到释放。而且，如果函数中的代码抛出异常，那么析构函数也会被调用，资源同样会得到释放。所以，在RAII下，不仅仅资源安全，也是异常安全的。

但是，在如下的代码中，资源不是安全的，尽管我们实现了RAII：
void fun2() ![img](http://www.cppblog.com/Images/dot.gif){
    file pfile=new file("my.txt");
    ![img](http://www.cppblog.com/Images/dot.gif) //操作文件
}
因为我们在堆上创建了一个对象（通过new），但是却没有释放它。我们必须运用delete操作符显式地加以释放：
void fun3() ![img](http://www.cppblog.com/Images/dot.gif){
    file pfile=new file("my.txt");
    ![img](http://www.cppblog.com/Images/dot.gif) //操作文件
        delete pfile;
}
否则，非但对象中的资源得不到释放，连对象本身的内存也得不到回收。（将来，C++的标准中将会引入GC（垃圾收集），但正如下面分析的那样，GC依然无法确保资源的安全）。
现在，在fun3()，资源是安全的，但却不是异常安全的。因为一旦函数中抛出异常，那么delete pfile;这句代码将没有机会被执行。

C++领域的诸位大牛们告诫我们：如果想要在没有GC的情况下确保资源安全和异常安全，那么请使用智能指针：
void fun4() ![img](http://www.cppblog.com/Images/dot.gif){
    auto_ptr<file> spfile(new file("my.txt"));
    ![img](http://www.cppblog.com/Images/dot.gif) //操作文件
} //此处，spfile结束生命周期的时候，会释放（delete）对象

​	也就是说，智能指针通过RAII来确保内存资源的安全，也间接地使得对象上的RAII得到实施。不过，这里的RAII并不是十分严格：对象（所占的内存也是资源）的创建（资源获取）是在构造函数之外进行的。广义上，我们也把它划归RAII范畴。但是，Matthew Wilson在《Imperfect C++》一书中，将其独立出来，称其为RRID（Resource Release Is Destruction）。RRID的实施需要在类的开发者和使用者之间建立契约，采用相同的方法获取和释放资源。比如，如果在shared_ptr构造时使用malloc()，便会出现问题，因为shared_ptr是通过delete释放对象的。  