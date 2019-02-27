# Visual Studio error LNK2038:检测到“RuntimeLibrary”的不匹配项: 值“MDd_DynamicDebug”不匹配值“MTd_StaticDebug”(core.obj 中）

# 1.对于此错误个人理解

**错误:**LNK2038	检测到“RuntimeLibrary”的不匹配项: 值“MDd_DynamicDebug”不匹配值“MTd_StaticDebug”(core.obj 中)	laser	E:\gitCodeRepository\Laser\urg_cpp.lib(Urg_driver.obj)  

![1551258248908](E:\MarkDown\MyNote\Markdown\error LNK2038 检测到RuntimeLibrary的不匹配项 值\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551258248908.png)

**分析:**对应的库文件为 urg_cpp.lib，解决方案文件生成 urg_cpp.lib时运行库为MDd，所以该库为MDd版本，而工程属性->C/C++->代码生成->运行库   配置为多线程调试 (/MTd)

属性冲突，所以其修改为MDd库才能正常编译，引起这种错误的原因是生成库文件时是使用MDd运行时库，在调用库函数的当前工程中使用了非MDd运行时库导致报错（见3.注意）。

# 2.运行时库的介绍

【注:**运行时库** **(Runtime Library)**(源百度百科):在计算机程序设计领域,运行时库是一种被编译器用来实现[编程语言](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80/9845131)[内置函数](https://baike.baidu.com/item/%E5%86%85%E7%BD%AE%E5%87%BD%E6%95%B0/11057473)，以提供该语言程序运行时(执行)支持的一种特殊的计算机程序库。这种库一般包括基本的输入输出或是[内存管理](https://baike.baidu.com/item/%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86/5633616)等支持。运行时库是程序在运行时所需要的库文件，通常运行时库是以LIB或DLL形式提供的。C运行时库诞生于20世纪70年代，当时的程序世界还很单纯，应用程序都是单线程的，多任务或多线程机制在此时还属于新观念。所以这个时期的C运行时库都是单线程的。随着操作系统多线程技术的发展，最初的C运行时库无法满足程序的需求，出现了严重的问题。C运行时库使用了多个全局变量（例如errno）和静态变量，这可能在多线程程序中引起冲突。假设两个线程都同时设置errno，其结果是后设置的errno会将先前的覆盖，用户得不到正确的错误信息。因此，Visual C++提供了两种版本的C运行时库。一个版本供单线程应用程序调用，另一个版本供多线程应用程序调用。

​	通俗的说就是我们的程序运行的时候所依赖的库文件，在Windows平台这些库由微软提供，并且是以2种形式提供：静态库(lib)、动态库(lib+dll)。每个库还都提供debug、release 2个版本。C/C++运行时库从形式上来讲和我们自己开发的静态库、动态库没什么两样，只是它们由微软开发和维护，并提供了一些常用的功能支持（如malloc,free, printf等等），如果我们的程序需要使用这些功能（事实上，只要是C/C++程序就一定会用到运行时库提供的功能），就要链接C/C++运行时库。】

​	**对于MSVC的运行时库（CRT），按照静态/动态链接，可以分为静态版和动态版；按照调试/发布，可以分为调试版本和发布版本；按照单线程/多线程，可以分为单线程版本和多线程版本（但是目前VS中已不提供单线程版本了） ** 

 **静态链接：多线程（/MT）、多线程调试（/MTd）**

 **动态链接：多线程DLL（/MD）、多线程调试DLL（/MDd）**

***MT**："multithread static version ” 意思是多线程静态运行时库，定义了它后，编译器把LIBCMT.lib 安置到OBJ文件中，让链接器使用LIBCMT.lib 处理外部符号，编译器会从运行时库里面选择多线程静态连接库来解释程序中的代码，即链接LIBCMT.lib*库

***MTd**：mutithread+debug，多线程调试运行时库，连接LIBMITD.lib*库

 **MT、MTd**表示采用多线程CRT库的静态lib运行时库。该选项会在编译时，将 运行时库 以静态lib的形式完全嵌入。该选项生成的可执行文件运行时不需要运行时库dll的参加，会获得轻微的性能提升，但最终生成的二进制代码因链入庞大的 运行时库 实现而变得非常臃肿。当某项目以静态链接库的形式嵌入到多个项目，则可能造成运行时库的内存管理有多份，最终将导致致命的“Invalid Address specified to RtlValidateHeap”问题。另外托管C++和[CLI](http://dearymz.blog.163.com/blog/static/2056574200912511547972/)中不再支持/MT和/MTd选项。



***MD**："multithread- and DLL-specific version” ，意思是多线程DLL（动态）运行时库，定义了它后，编译器把 MSVCRT.lib 安置到OBJ文件中，它连接到DLL的方式是静态链接，实际上工作的库是MSVCRT.lib + MSVCR80.DLL，对应动态库为MSVCRT.dll*

***MDd**： MT+DLL+debug，多线程动态调试运行时库，链接MSVCRTD.lib库，对应动态库为MSVCRTD.dll*

**MD、MDd**，表示采用多线程CRT库的动态dll运行时库，会使应用程序使用 运行时库 特定版本的多线程DLL。链接时将按照传统VC链接dll的方式将 运行时库 MSVCRxx.DLL的导入库MSVCRT.lib链接，在运行时要求安装了相应版本的VC 运行时库 可再发行组件包（当然把这些运行时库dll放在应用程序目录下也是可以的）。 因MD和MDd方式不会将 运行时库 链接到可执行文件内部，可有效减少可执行文件尺寸。当多项目以MD方式运作时，其内部会采用同一个堆，内存管理将被简化，[跨模块内存管理](http://dearymz.blog.163.com/blog/static/205657420091179036308/)问题也能得到缓解。



Reusable Library Switch Library Macro(s) Defined

| Single Threaded          | /ML  | LIBC    | (none)                |
| ------------------------ | ---- | ------- | --------------------- |
| Static MultiThread       | /MT  | LIBCMT  | _MT                   |
| Dynamic Link (DLL)       | /MD  | MSVCRT  | _MT and _DLL          |
| Debug Single Threaded    | /MLd | LIBCD   | _DEBUG                |
| Debug Static MultiThread | /MTd | LIBCMTD | _DEBUG and _MT        |
| Debug Dynamic Link (DLL) | /MDd | MSVCRTD | _DEBUG, _MT, and _DLL |

调试版本（ DEBUG版本）下缩写后带d，但发布版本（RELEASE版本）不带。调试与发布的区别在于，发布模式省略了程序的调试信息，简单来说就是删除了调试模式下的用于调试的内容，所以一般情况下，发布模式下生成的可执行文件的大小比调试模式下生成的要小。大型项目中必须要求所有组件和第三方库的运行时库是统一的，否则将会出现[LNK2005](http://msdn.microsoft.com/zh-cn/library/72zdcz6f(VS.80).aspx)井喷。

# 3.注意:

​	**运行时库使用注意事项**

- **不要混合使用运行时库的`静态库版`和`动态库版`。**
- **不要混合使用运行时库的`debug版`和`release版`。**
- **不要混合使用不同版本的运行时库。**

  **在生成第三方库文件（lib或dll）时:在调试模式下，使用调试运行时库：多线程调试（/MTd）、多线程调试DLL（/MDd）,在发布模式下，使用发布运行时库：多线程（/MT）、多线程DLL（/MD）**

​	**若用CMAKE/VS解决方案生成工程文件**时，若CMAKE是用**MT**生成的（查看工程原始目录的CMakeLists.txt)，则它所调用的运行时库为：LIBCMT.lib，若生成的工程的运行时库（Runtime Library)你选择**MD**，则此工程在编译后链接的时候，将会调用动态运行时库：MSVCRT.lib + MSVCR80.DLL，明显，两次对同一个某运行时库里的函数调用的库不同，则会出现重定义的错误。**若此工程生成的是库文件**，则其他工程调用此库时也必须是**MT**。（接下来对此进行验证，承接第1点说的错误）

​	生成urg_cpp.lib解决方案文件配置如下:由下图可见的确在Debug x64下运行库设置为**MDd**![1551270834956](E:\MarkDown\MyNote\Markdown\error LNK2038 检测到RuntimeLibrary的不匹配项 值\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551270834956.png)

再看Release x64版本下下运行库设置为**MD**

![1551270911803](E:\MarkDown\MyNote\Markdown\error LNK2038 检测到RuntimeLibrary的不匹配项 值\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551270911803.png)



​	**MT/MTd方式编译，程序对所有的库都是静态链接的，这种想法是错误的**。它只能决定运行时库是动态链接还是静态链接，对**用户自己写的库或其他第三方库**，其链接方式取决于代码（显示链接动态库Loadlibrary）或所提供的lib文件（为导入库还是静态库），移动程序到别的机器上时，还是要带上所需要的动态库的。对于**MT/MTd**，由于连接运行时库是LIBCMT.lib/LIBCMTD.lib，这两个库是静态库，所以此种方式编译的程序，移到另一台机器上面也可以正常运行。但是对于**MD/MDd**，连接的是动态库，所以如果另一台机器上没有MSVCRT.dll/MSVCRTD.dll时，就提示缺少动态库这样的错误。

​	 推荐解决方案生成库文件时使用/MD和/MDd运行库。







# **PS**:静态库（静态链接库）、动态库（动态链接库）的概念

首先两者都是代码共享的方式。

**静态库**：在链接步骤中，连接器将从库文件取得所需的代码，复制到生成的可执行文件中，这种库称为静态库，其特点是可执行文件中包含了库代码的一份完整拷贝；缺点就是被多次使用就会有多份冗余拷贝。即静态库中的指令都全部被直接包含在最终生成的 EXE 文件中了。在VS中新建生成静态库的工程，编译生成成功后，只产生一个.lib文件

**动态库**：动态链接库是一个包含可由多个程序同时使用的代码和数据的库，DLL不是可执行文件。动态链接提供了一种方法，使进程可以调用不属于其可执行代码的函数。函数的可执行代码位于一个 DLL 中，该 DLL 包含一个或多个已被编译、链接并与使用它们的进程分开存储的函数。在vs中新建生成动态库的工程，编译成功后，产生一个.lib文件和一个.dll文件里面没有代码，代码在DLL中，这种LIB是用在静态调用DLL上的，所以起的作用也是链接中用，链接完成了，LIB也没用了。至于动态调用DLL的话，根本用不上LIB文件。

**静态库中的lib**：该LIB包含函数代码本身（即包括函数的索引，也包括实现），在编译时直接将代码加入程序当中

**动态库中的lib**：该LIB包含了函数所在的DLL文件和文件中函数位置的信息（索引），函数实现代码由运行时加载在进程空间中的DLL提供

**总之，lib是编译时用到的，dll是运行时用到的。如果要完成源代码的编译，只需要lib；如果要使动态链接的程序运行起来，只需要dll**。





参考文章:



1.https://www.cnblogs.com/xzabg/p/5875296.html

2.https://www.cnblogs.com/jieliujas/p/8778389.html

3.https://www.cnblogs.com/TenosDoIt/p/3203137.html

4.https://blog.csdn.net/qq_33757398/article/details/82156956

5.https://blog.csdn.net/china_jeffery/article/details/79555947