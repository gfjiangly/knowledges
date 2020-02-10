## 一、在当前文件中引入命名空间

这是我们最熟悉的用法，例如：`using namespace std;`



## 二、在子类中使用 using 声明引入基类成员名称（参见C++ primer）

### 2.1 修改父类成员在子类中对外访问权限

在private或者protected继承时，基类成员的访问级别在派生类中更受限，例如：

```cpp
class Base {
public:
	std::size_t size() const { return n; }
protected:
	std::size_t n;
};
class Derived : private Base { . . . };
```

在这一继承层次中，成员函数 size 在 Base 中为 public，但在 Derived 中为 private。为了使 size 在 Derived 中成为 public，可以在 Derived 的 public 部分增加一个 using 声明。如下这样改变 Derived 的定义，可以使 size 成员能够被用户访问，并使 n 能够被 Derived的派生类访问：

```cpp
class Derived : private Base {
public:
	using Base::size;
protected:
	using Base::n;
// ...
};
```

### 2.2 使父类中被子类同名函数隐藏的函数可见

当子类中的成员函数和基类同名时，子类中重定义的成员函数将隐藏基类中的版本，即使函数原型不同也是如此。如果基类中成员函数有多个重载版本，派生类可以重定义所继承的 0 个或多个版本，但是**通过派生类型只能访问派生类中重定义的那些版本**，所以如果派生类想通过自身类型使用所有的重载版本，则派生类必须**要么重定义所有重载版本**，**要么一个也不重定义**。有时类需要仅仅重定义一个重载版本的行为，并且想要继承其他版本的含义，在这种情况下，为了重定义需要特化的某个版本而不得不重定义每一个基类版本，可能会令人厌烦。可以在派生类中为重载成员名称提供 using 声明（为基类成员函数名称而作的 using 声明将该函数的所有重载实例加到派生类的作用域），使派生类不用重定义所继承的每一个基类版本。一个 using 声明只能指定一个名字，不能指定形参表，使用using声明将名字加入作用域之后，派生类只需要重定义本类型确实必须定义的那些函数，对其他版本可以使用继承的定义。

《Effective C++ 第三版》条款52中有个使用using使多个版本operate new在派生类中可见的例子。（P261）



## 三、定义别名

```cpp
template<typename T> using Tlist = std::list<T>;
```
