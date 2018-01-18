# Kotlin学习记录
## 参考文章及网站
[Kotlin语言中文站](https://www.kotlincn.net/)   
[为什么我要改用Kotlin](http://blog.csdn.net/androidyue/article/details/72457157)   
[Android名门正娶Kotlin还是纳妾](https://www.jianshu.com/p/786fede1e44f)   
在上面一篇文章中作者例举了其他学习网站我就不一一列举，毕竟大神写的不我好的太多，建议大家多看大神文章   
## 我对Kotlin的认知的过程及原因
我是个Android开发实习生，所以对Android开发的新闻关注比较多，也是因为这样我才了解到Kotlin。
在去年5月18日，安卓团队在谷歌 I/O 2017 大会上宣布 Kotlin 成为官方第一支持语言，因为这件事我才了解到Kotlin这门语言，然后对这门语言进行了解：
### Kotlin“简历”
- 来自于著名的IDE IntelliJ IDEA(Android Studio基于此开发) 软件开发公司 JetBrains(位于东欧捷克)
- 起源来自JetBrains的圣彼得堡团队，名称取自圣彼得堡附近的一个小岛(Kotlin Island)
- 一种基于JVM的静态类型编程语言
### Kotlin特点
1. 兼容性
Kotlin与JDK 6完全兼容，确保Kotlin应用程序可以在较旧的Android设备上运行，无任何问题。   
Kotlin工具在AndroidStudio中完全支持，与Android构建系统兼容，并且在出来的Android Studio3.0预览版中已经完全不需要插件的形式进行Kotlin开发，可以实现Java代码一键转为Kotlin代码了。
2. 性能
Kotlin应用程序运行速度与Java类似，非常类似的字节码结构。   
随着Kotlin对内联函数的支持，使用lambdas的代码通常比使用Java编写的代码运行得更快。   
3. 互操作性
Kotlin可与Java进行100％互操作，允许在Kotlin应用程序中使用所有现有的Android库。   
这包括注释处理，所以Data Binding和Butter Knife也是一样。   
4. 占用空间
Kotlin有一个非常紧凑的运行时库，可以通过使用ProGuard进一步减少。   
在真实的应用程序中，Kotlin运行时只添加了几百种方法，并且小于等于100K的.apk文件的大小。   
5. 编译
Kotlin支持高效的增量编译，因此，对于清理构建来说，增加的构建通常与Java一样快或更快。   
6. 学习
对于Java开发人员，Kotlin入门很容易。包含在Kotlin插件中的自动化Java到Kotlin转换器有助于实现第一步。   
