## 插件

 MagicPython：语法高亮，用于代替默认的高亮规则。默认的规则对python内置函数竟然不高亮，这一点忍不了。注意颜色主题需要选择Dark+(default dark)，这时插件才起作用。

https://blog.csdn.net/u011459278/article/details/51841481

## launch.json

### 环境

```
"env": {"PYTHONPATH": "${workspaceRoot}"}
```

可以防止debug时，`sys.path.append('../../..')`不起作用找不到模块。



## 取消文件自动定位到侧边栏

打开设置File-Preference-Settings，搜索reveal，滑到Auto Reveal，选择false。

![image-20201115110804664](../../assets/image-20201115110804664.png)

## C++调试环境

```bash
# 安装
sudo apt-get update
sudo apt-get install gcc
sudo apt-get install g++
sudo apt-get install gdb
```



launch.json

```bash
// https://code.visualstudio.com/docs/cpp/launch-json-reference
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",  // 配置名称，将会在启动配置的下拉菜单中显示
            "type": "cppdbg",   // 配置类型，cppdbg对应cpptools提供的调试功能
            "request": "launch",  // 请求配置类型，可以为launch（启动）或attach（附加）
            "program": "${fileDirname}/${fileBasenameNoExtension}",  // 将要进行调试的程序的路径
            "args": [],  // 程序调试时传递给程序的命令行参数
            "stopAtEntry": false,  // 设为true时程序将暂停在程序入口处，相当于在main上打断点
            "cwd": "${workspaceFolder}",  // 调试程序时的工作目录，此为工作区文件夹；改成${fileDirname}可变为文件所在目录
            "environment": [],  // 环境变量
            "externalConsole": true,  // 使用单独的cmd窗口，与其它IDE一致；为false时使用内置终端
            "internalConsoleOptions": "neverOpen", // 如果不设为neverOpen，调试时会跳到“调试控制台”选项卡，你应该不需要对gdb手动输命令吧？
            "MIMode": "gdb",  // 指定连接的调试器，可以为gdb或lldb。但我没试过lldb
            "miDebuggerPath": "gdb", // 调试器路径，Windows下后缀不能省略，Linux下则不要
            "setupCommands": [
                {  // 模板自带，好像可以更好地显示STL容器的内容，具体作用自行Google
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "Compile" // 调试会话开始前执行的任务，一般为编译程序。与tasks.json的label相对应
        }
    ]
}
```

task.json

```bash
// https://code.visualstudio.com/docs/editor/tasks
{
    "version": "2.0.0",
    "tasks": [{
        "label": "Compile", // 任务名称，与launch.json的preLaunchTask相对应
        "command": "gcc",   // 要使用的编译器，C++用g++
        "args": [
            "${file}",
            "-o",    // 指定输出文件名，不加该参数则默认输出a.exe，Linux下默认a.out
            "${fileDirname}/${fileBasenameNoExtension}",
            "-g",    // 生成和调试有关的信息
            "-m64", // 不知为何有时会生成16位应用而无法运行，加上此条可强制生成64位的
            "-Wall", // 开启额外警告
            "-static-libgcc",     // 静态链接libgcc，一般都会加上
            "-fexec-charset=GBK", // 生成的程序使用GBK编码，不加这条会导致Win下输出中文乱码；繁体系统改成BIG5
            // "-std=c11", // 要用的语言标准，根据自己的需要修改。c++可用c++14
        ], // 编译的命令，其实相当于VSC帮你在终端中输了这些东西
        "type": "process", // process是把预定义变量和转义解析后直接全部传给command；shell相当于先打开shell再输入命令，所以args还会经过shell再解析一遍
        "group": {
            "kind": "build",
            "isDefault": true // 不为true时ctrl shift B就要手动选择了
        },
        "presentation": {
            "echo": true,
            "reveal": "always", // 执行任务时是否跳转到终端面板，可以为always，silent，never。具体参见VSC的文档
            "focus": false,     // 设为true后可以使执行task时焦点聚集在终端，但对编译C/C++来说，设为true没有意义
            "panel": "shared"   // 不同的文件的编译信息共享一个终端面板
        },
        "problemMatcher":"$gcc" // 捕捉编译时终端里的报错信息到问题面板中，修改代码后需要重新编译才会再次触发
        // 本来有Lint，再开problemMatcher就有双重报错，但MinGW的Lint效果实在太差了；用Clang可以注释掉
    }]
}
```

多文件编译：

少量的多文件编译，使用命令行

```bash
gcc 源文件1.c 源文件2.c 头文件1.h
```

如果进行大量的多文件编译，可以学习如何写makefile或者使用cmake。然后把tasks的命令改成调用make等。

这些和VSC无关，用其他IDE或者是手动编译也会遇到差不多的问题。

> 如果需要debug，在编译时候需要用到gcc的-g参数：
> 如：
>
> ```bash
> gcc -o test.o -g -c test.c
> gcc -o config.o -g -c config.c
> gcc -o main test.o config.o
> ```

