## 编译.proto文件

   protoc：protobuf自带的编译工具，将.proto文件生成指定的类  

   –cpp_out：将生成的C++代码文件放到等号后面指定的目录，这里也指定当前目录

```javascript
    通过protoc工具编译.proto文件时，编译器将生成所选择语言的代码，这些代码可以操作在.proto文件中定义的消息类型，包括获取、设置字段值，将消息序列化到一个输出流中，以及从一个输入流中解析消息。对C++来说，编译器会为每个.proto文件生成一个.h文件和一个.cc文件，.proto文件中的每一个消息有一个对应的类。
```



## 在.proto文件中定义消息格式

消息由至少一个字段组合而成，类似于C语言中的结构体，每个字段都有一定的格式：

数据类型 字段名称 = 唯一的编号标签值;

```javascript
syntax = "proto3"; //指定版本信息，不指定会报错

message Person  //message为关键字，作用为定义一种消息类型
{
    string name = 1;    //姓名
    int32 id = 2;       //id
    string email = 3;   //邮件
}
```

proto3 比 proto2 支持更多语言但 更简洁。去掉了一些复杂的语法和特性，更强调约定而弱化语法。如果是首次使用 Protobuf ，建议使用 proto3 