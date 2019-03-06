# CGAL环境配制

## 所需工具 ## 
系统参数: 
操作系统：win10
IDE：vs20107

### **官网教程**:https://www.cgal.org/download/windows.html#CompilinganExample

(1). 安装CGAL所需的软件：

1. CMAKE(https://cmake.org/download/)
2. Qt(个人使用的是Qt5.9.7)(http://download.qtproject.org/official_releases/qt/)
3. Boost(个人使用的boost_1_69_0(http://www.boost.org/users/download/)
4. CGAL(我下载的是CGAL-4.13)(https://github.com/CGAL/cgal/releases）

## 安装 

(1). CMAKE的安装 
CMAKE按照安装向导 

(2).Qt的安装 

可以在http://download.qt.io/archive/qt/下载到各个版本的Qt，个人选用的是5.9.7版本

Qt的下载需要在官网注册一个账号，之后下载安装即可，安装完成后需要添加环境变量如下(选择自己对应QT的安装路径):

```
E:\QT\5.9.7\msvc2017_64\bin
```

进入VS2017 ->工具->扩展和更新->联机->QT VisualSutdio Tools

![1551789120522](E:\MarkDown\MyNote\Markdown\CGAL\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551789120522.png)

安装完以后选择Qt VS Tools->Qt options->Add添加

Name:msvc2017_64       Path:E:\QT\5.9.7\msvc2017_64

![1551789435779](E:\MarkDown\MyNote\Markdown\CGAL\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551789435779.png)

3.配置Boost

（1）下载地址：https://sourceforge.net/projects/boost/files/boost-binaries/

官网提供的在sourceforge下载exej进行安装boost_1_67_0-msvc-xx.0-32.exe

新建环境变量:

```
BOOST_LIBRARYDIR  =     E:\boost1-69\boost_1_69_0\lib64-msvc-14.1
BOOST_ROOT		  =	    E:\boost1-69
```

在PATH环境变量继续添加

```
PATH   =     E:\boost1-69\boost_1_69_0\lib64-msvc-14.1
```



（2）下载地址：http://www.boost.org/users/download/

boost1_69_1解压后运行bootstrap.bat  等待一段时间，目录下生成bjam.exe，运行即可，等一段时间，等待的时候根据你的电脑性能而定，编译完成后你就会在sedir后面的值的目录中找到编译完成后的库文件。到此，boost 安装完毕。 程序会根据你当前安装的编译环境（vs版本)等自动选择与之相适应的库文件和包含文件等。此过程大概需要20分钟左右。

再PS：boost1_69_0解压后运行bootstrap.bat，若提示cl不是内部或外部命令，则是vs的环境变量没有配置好，我的环境变量还是之前装的VS2015，所以出错，换成VS2017的目录后就好了。

4.安装CGAL和编译CGAl

**安装CGAL：**下载地址：https://github.com/CGAL/cgal/releases

1.**（推荐）**安装方式1：windows用户推荐使用直接安装CGAL-4.13-Setup.exe（安装时可能需要翻墙才行），官方把需要的依赖库都打包在里面了。

2.安装方式2:下载官方的源码编译-->windows平台下这里遇到许多问题，到最后需要一个一个添加依赖库 如GMP库 MPFR库 QT库：

点击configure .等十几秒后，会Error in configuration,project files may be invalid。报错信息显示缺少GMP

![1551791986912](E:\MarkDown\MyNote\Markdown\CGAL\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551791986912.png)

下载MINGW-32后进行安装（安装过程可参照[链接](https://blog.csdn.net/u012629110/article/details/51220727)），安装完mingw32-base mingw32-gcc-g++ mingw32-gmp mingw32-mpfr后-->可在安装MingW底下找到gmp的include头文件和 gmp的dll动态链接库，将GMP_LIBRARIES 等指向对应的文件夹路径，下图是直接通过exe文件安装后的对应GMP库 MPFR库 QT库的指向，反正我是没继续弄下去，尤其是添加MPFR库时一直没找到对应的库文件，后来就放弃了。

![1551881015816](E:\MarkDown\MyNote\Markdown\CGAL\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551881015816.png)



**编译CGAl：**打开CmakeGUI，输入源目录和要生成的目录，然后点击configure选择**vs2017  ，winx64** 同时，若没有错误继续点击Generate，打开生成的CGAL.sln解决方案    生成整个项目，将![img](https://img-blog.csdn.net/20180615163039242?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x5aDI0aw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)添加到Path环境变量



PS:  



参考文章:

1.https://blog.csdn.net/aishuirenjia/article/details/79574099

2.https://blog.csdn.net/jacayang/article/details/45717517

3.https://wenku.baidu.com/view/e29951d9185f312b3169a45177232f60ddcce7c4.html