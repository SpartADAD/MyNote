# Visual Studio环境变量使用实例：使用环境变量来组织工程

​	如果你是项目的唯一开发者，并且仅使用一台电脑来开发的话，那么使用绝对路径也没什么大问题。但是如果这个项目是个团队协作项目，或者你需要在好几台电脑之间切换，那么这个包含路径如果写绝对路径就不够灵活了，如果别人的路径配置或别的电脑的路径配置不同，那么要重新修改包含路径。使用环境变量来组织工程的话就只需要添加或者修改环境变量的值（这里建议直接新建一个环境变量，不直接在PATH继续添加）。

# 1.首先添加环境变量

​	控制面板->系统和安全->系统->高级系统设置->环境变量 进行用户变量或系统变量添加编辑

![1551249430079](E:\MarkDown\MyNote\Markdown\Visual Studio使用环境变量\1551249430079.png)

编辑完后环境变量不会立即生效

不注销(WIN+L)或者重启立即使得环境变量生效的办法:

使用WIN+R呼出运行 ->输入cmd，回车->进入DOS命令提示符输入(以LASER_ROOT为例子):

```
set LASER_ROOT=C:
```

关闭DOS窗口。再次打开DOS窗口，输入：

```
echo %LASER_ROOT% 
```

可见环境变量已经生效

不用担心DOS窗口中的修改会影响环境变量的值，DOS窗口中的环境变量只是Windows环境变量的一个副本而已。但是对副本的修改却会引发Windows环境变量的刷新。

PS：对于相同的值，比如用户变量和系统变量都有一个INCLUDE，则用户变量会覆盖系统变量，导致系统变量的路径搜不到，这个需要注意。

# 2.打开或重启VS2017，对包含目录/库目录的绝对路径进行更改

（PS：新增环境变量时VS2017工程项目如果是打开的，需要重启VS，不然可能会出现无法找到环境变量的问题->可以通过 项目->属性->VC++目录->包含目录->宏 当中查看环境变量是否生效）

例如原来包含目录当中填写的是E:\gitCodeRepository\Laser\ 可以改为$(LASER_ROOT)\Laser

```
Example:E:\gitCodeRepository\Laser\  -> $(LASER_ROOT)\Laser
```

也就是$(LASER_ROOT)的取值 等于 E:\gitCodeRepository。

在修改后注意观察包含目录下面一栏的 "计算的值" 当中的路径是否正确，是否是要添加的对应路径

![1551249629167](E:\MarkDown\MyNote\Markdown\Visual Studio使用环境变量\1551249629167.png)

# PS：

1.对于同一个项目组的在通过git同步代码库之后，就需要自己添加包含目录和库目录对应的环境变量。并使得环境变量对应自己的对应路径，使环境变量生效。

2.不直接在PATH当中添加路径 是由于:如下图所示

会出现将E:\gitCodeRepository  、\LaserLaser分开作为两个路径



![1551251887973](E:\MarkDown\MyNote\Markdown\Visual Studio使用环境变量\%5CUsers%5CDAD%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1551251887973.png)













参考博客：:

https://www.jianshu.com/p/46fc016aa3a5

https://blog.csdn.net/elloop/article/details/51010151

https://blog.csdn.net/llq108/article/details/52332611