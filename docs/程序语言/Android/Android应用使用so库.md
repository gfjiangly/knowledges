### 编译SO库

使用AS创建Android应用，勾选Include C++ support，即 Native应用，AS会为我们生成编译cpp成so库的CMakefile文件。在CMake构建工具没有发布之前，都是ndk-build方式来编译生成so库，但是ndk-build比较繁琐且容易出错，用CMake方式比较简单，AS已集成。一般在x86平台上只能编译出支持x86架构CPU的so库，在ARM平台上只能编译出对应ARM架构ABI的so库，好在NDK可以完成交叉编译。

一种CPU架构，对应一种ABI ，对应一种SO库。在AS的配置文件中，尽量为每种架构都生成一个SO库。



### 调用

SO库的使用本身就是一种最纯粹的动态加载技术，SO库本身不参与APK的编译过程，使用JNI调用SO库里的Native方法的方式看上去也像是一种“硬编程”，Native方法看上去与一般的Java静态方法没什么区别，但是它的具体实现却是可以随时动态更换的（更换SO库就好），这也可以用来实现热修复的方案，与Java方法一旦加载进内存就无法再次更换不同，Native方法不需要重启APP就可以随意更换。

出于安全和生态控制的原因，Google Play市场不允许APP有加载外部可执行文件的行为，所以现在许多APP都偷偷把用于动态加载的可执行文件的后缀名换成“.so”，这样被发现的几率就降低了。

在Android应用中加载so库，一般都需要这个so库是符合jni命名规范的，像这样：

```
Java_全类名_方法名
```

而一般ncnn模型的so库并不是用jni命名，这时候我们就需要自己再次包装这个so库了，再生成符合jni命名规范的so库，在Java代码中正确声明即可使用。

### 调用第三方so库（如CV模型）

1.    对于so库的API符合JNI格式，可以在Java代码中声明它对应的native方法，然后直接调用。

    比如，jni方法名为：` jstring JNICALL Java_com_example_hellojni_stringFromJNI(JNIEnv *env, jobject obj); `   （即前缀 Java+包名+类名+方法名）

    那么这个方法名就是在java中声明的native方法名：`public native String stringFromJNI();`

2.    对于so库的API不符合JNI格式，需要自己编写C/C++源文件包装一下，在该源文件实现自己的JNI格式native函数，再在JNI函数中调用第三方so库中函数，再在java中调用自己实现的JNI格式的native方法。这种方法更加灵活。

不管怎样，使用Java调用，必须正确声明，声明不正确，就找不到SO库中对应的函数了。



### Android系统可运行的CPU

早期的Android系统几乎只支持ARM的CPU架构，不过现在至少支持以下七种不同的CPU架构：ARMv5，ARMv7，x86，MIPS，ARMv8，MIPS64和x86_64。每一种CPU类型都对应一种ABI（Application Binary Interface），“armeabi-v7a”文件夹前面的“armeabi”指的就是ARM这种类型的ABI，后面的“v7a”指的是ARMv7。这7种CPU类型对应的SO库的文件夹名是：armeabi，armeabi-v7a，x86，mips，arm64-v8a，mips64，x86_64。



### SO库需要放在特定的文件夹中

不同类型的移动设备在运行APP时，需要加载自己支持的类型的SO库，通过 **Build.SUPPORTED_ABIS** 可以判断当前设备支持的ABI，不过一般情况下，不需要开发者自己去判断ABI，Android系统在安装APK的时候，不会安装APK里面全部的SO库文件，而是会根据当前CPU类型支持的ABI，从APK里面拷贝最合适的SO库，并保存在APP的内部存储路径的 **libs** 下面。因此SO库需要放在对应CPU架构的文件夹中，否则系统找不到。

SO库其实都是APP运行时加载的，也就是说APP只有在运行的时候才知道SO库文件的存在，这就无法通过静态代码检查或者在编译APP时检查SO库文件是否正常。所以，Android开发对SO库的存放路径有严格的要求。

使用SO库的时候，除了“armeabi-v7a”等文件夹名需要严格按照规定的来自外，SO库要放在项目的哪个文件夹下也要按照套路来，以下是一些总结：

- Android Studio 工程放在 **jniLibs/xxxabi** 目录中（当然也可以通过在build.gradle文件中的设置jniLibs.srcDir属性自己指定）；
- Eclipse 工程放在 **libs/xxxabi** 目录中（这也是使用ndk-build命令生成SO库的默认目录）；
- aar 依赖包中位于 **jni/ABI** 目录中（SO库会自动包含到引用AAR压缩包到APK中）；
- 最终构建出来的APK文件中，SO库存在 **lib/xxxabi** 目录中（也就是说无论你用什么方式构建，只要保证APK包里SO库的这个路径没错就没问题）；
- 通过 PackageManager 安装后，在小于 Android 5.0 的系统中，SO库位于 APP 的 **nativeLibraryPath** 目录中；在大于等于 Android 5.0 的系统中，SO库位于 APP 的 **nativeLibraryRootDir/CPU_ARCH** 目录中；



### 参考

- [https://wongzhenyu.cn/2018/06/30/Android%20NDK%E8%B0%83%E7%94%A8%E4%B8%8D%E7%AC%A6%E5%90%88jni%E8%A7%84%E8%8C%83%E7%9A%84so%E6%96%87%E4%BB%B6/](https://wongzhenyu.cn/2018/06/30/Android NDK调用不符合jni规范的so文件/)