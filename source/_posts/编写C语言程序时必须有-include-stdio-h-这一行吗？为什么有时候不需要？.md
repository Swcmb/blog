---
title: 编写C语言程序时必须有#include<stdio.h>这一行吗？为什么有时候不需要？
date: 2024-11-26 23:19:01
tags:
---

> 编写C语言程序时必须有#include<stdio.h>这一行吗？为什么有时候不需要？
>
> 作者：夏洛克
> 链接：https://www.zhihu.com/question/21287371/answer/2819849667
> 来源：知乎
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



我的回答不限于当前这个问题。由于市面上C语言教程的编排太垃圾而引发的初学者的一系列困惑都可以在这里解决。

我认为给编程入门者的C语言教材，**第一课就应该讲清楚 #include**，由此自然的引出[模块化设计](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=模块化设计&zhida_source=entity)等通用编程思想，展现出从一行代码到一个完整项目的整体印象, 而不是去写无用程序 Hello World。



## 前言：关于编程教材的吐槽：学编程要先学C，学C要先懂编程



市面上C语言教程的编排都很垃圾，完全没有逻辑，无脑陈列细节。包括C语言之父 [Dennis Ritchie](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=Dennis+Ritchie&zhida_source=entity) 的那本《C程序设计语言》。或者说这本就是罪魁祸首?

《[C程序设计语言](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=2&q=C程序设计语言&zhida_source=entity)》就是一本C语言特性介绍手册，明显是给"**初学C语言的编程人员"**设计的，而不是为**编程初学者**设计的。这书的存在本身没问题, 问题在于是它之后所有的编程教材(不限于C语言)都只会按着这样的编排顺序去堆砌细节，没有一个合理的主线逻辑. 像[《C Primer Plus》](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=《C+Primer+Plus》&zhida_source=entity)这些流行教材更是加剧了这种趋势，实际上只是一些更厚更啰嗦的字典罢了。国内的那些垃圾就更不必提了！

于是, C语言作为一门流行的编程入门语言, 竟然使得**“学编程要先学C，学C要先懂编程”** 这个 bug 流传至今。以下是我为解决这个 bug 做的尝试。我相信看了这篇回答可以消解初学者们大部分疑惑，包括本题主的疑惑。

（当然还有第二种方案，那就是先去学 Python 。Python 是真正的 0 基础编程语言，没有上述 Bug. 但是用 Python 入门之后, 还是建议回来学学 C, 学编程是绕不开C语言的。）



## 第一课：#include 与 模块化编程



一个程序就像一台机器，是多个功能模块组合而成的。各模块组合起来后靠一个主体程序( 通常叫做 main )去集中调度。

例如一台简略的C语言玩具车写法如下：

### 1.0版

玩具车.c

```c
void 前进(int 距离){/*具体实现*/}
void 后退(int 距离){/*具体实现*/}
void 左转(int 角度){/*具体实现*/}
void 右转(int 角度){/*具体实现*/}

int main(){

    for (int i=0; i<4; i++)  //循环(4)次
    {
        前进(10);
        右转(90);
    }
    return 0;
}
```



可以观察到，程序由四个辅助功能块：前进，后退，左转，右转，和一个主体功能 main 构成。

主体通过某种流程去调用了4个功能中的2个，实现了让小车走一个 10×10 正方形回到原地的功能。

可以看到，我并没有 #include <stdio.h>，但是程序是完全正确的，不作任何修改就可以编译运行（目前没有任何运行结果，因为所有功能函数都是空的）。



### 2.0版

引入 #include 后，可以进一步优化我们的代码结构。

我们可以把 main 上方的那一大块同类的内容打包起来写在另一个文件里（比如 "遥控器.h"），然后把它与原来的 "玩具车.c" 文件放在同一目录下，如下所示：

遥控器.h

```c
void 前进(int 距离){/*具体实现*/}
void 后退(int 距离){/*具体实现*/}
void 左转(int 角度){/*具体实现*/}
void 右转(int 角度){/*具体实现*/}
```

玩具车.c

```c
#include "遥控器.h"

int main()
{
    for (int i=0; i<4; i++)  //循环(4)次
    {
        前进(10);
        右转(90);
    }
    return 0;
}
```

玩具车.c 里面有了 #include "遥控器.h" 这句话，编译工具就会在编译过程中自动把 "遥控器.h" 里的内容复制到 "玩具车.c" 中 #include 语句所在的位置。这样一来，从机器的角度，2.0版程序和1.0版完全等价，不会出错；

从人的角度，我们把一组相关的功能作为一个模块打包分离出去了。而 #include 就像是一个组装命令，帮我们把模块组装回来。于是我们可以用模块思想去设计小车的各个组成部分，在不同的文件里写不同的模块，一次只需专注一个部分，写完再组装起来。从此写程序就是造积木和搭积木的游戏！

从生产的角度，我们可以把大问题拆解成一些子模块，可以交给不同的人分别去解决，最后用 #include 组合起来，实现分工与合作。



好了，到这里你应该已经明白是否需要 #include 了, 其实就看你是否需要某个功能模块。

stdio.h 是一个负责向电脑屏幕输入/输出信息的模块，十分常用，所以初学者几乎没有不需要的。

有些模块就是这样, 很常用, 写起来又很麻烦，所以C语言维护者们已经写好一套常用工具，包含 stdio.h 、[stdlib.h](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=stdlib.h&zhida_source=entity)、math.h 等等十多个模块，各有各的用途，称为**“C语言[标准库](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=标准库&zhida_source=entity)”**；它们已经深度内置到编译工具包里了。

\#include "遥控器.h" 这个语句里，双引号里其实不是模块名, 而是模块的文件路径, 因为支持相对路径, 所以同目录下的模块只需写文件名即可。 其它地方的模块可以用绝对路径引入, 类似这样: 

```c
 #include "C:/Users/WHO/Downloads/模块.h"
```

标准库的路径编译器是认识的, 只要用尖括号 <> 表明一个模块属于标准库, 编译器就能找到它.

```c
 #include <stdio.h>
```

### 3.0版

[printf()](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=printf()&zhida_source=entity) 就是来自 stdio.h 的一个功能，可以用它在命令行窗口打出文字。

让我们把它加进小车里，让小车学会向你实时报告运行状态吧。

遥控器.h

```c
#include <stdio.h>

void 前进(int 距离){ printf("前进 %d 米\n",距离); }
void 后退(int 距离){ printf("后退 %d 米\n",距离); }
void 左转(int 角度){ printf("左转 %d 度\n",角度); }
void 右转(int 角度){ printf("前进 %d 度\n",角度); }
```

玩具车.c

```c
#include "遥控器.h"

int main()
{
    printf("出发\n");
    for (int i=0; i<4; i++)  //循环(4)次
    {
        前进(10);
        右转(90);
    }
    printf("结束\n");
    return 0;
}
```

可以看到遥控器需要使用 printf()，所以我们把 #include<stdio.h> 包含到遥控器头部。

而玩具车又 #include 了遥控器，于是层层包含最终汇集到 玩具车.c 中，所以现在也可以在玩具车里使用 printf()。

(能间接包含是C/C++语言的特点, 其它语言情况可能不一样, 比如 Python 的 import )

去编译试试看效果吧！

![img](https://picx.zhimg.com/80/v2-707190bb4d8800fec14f54bd1af9b743_720w.webp?source=2c26e567)

（编译教程会在第〇课更新）



## 第〇课：配置编译环境，命令行基础，带参数的 Hello World



### 1. 命令行基础

告诉你2个秘密: 

**秘密1.命令行才是多数软件真实的模样**. 日常所见的各种图形界面软件只不过是隐藏了命令行窗口罢了.

**如果你想入门编程的话, 就要抛弃以电脑桌面为中心的视角, 从现在开始, 把命令行界面看作你的家, 其它的一切都是从这里打开的.**

> 在 Windows 中, 按 WIN+S 搜索 "CMD" 或者 "Powershell" 或者 "终端", 都可打开命令行环境.

**秘密2.软件本身是没有"安装"这个概念的**. 软件放在哪个目录无所谓, 是纯靠文件路径来定位和运行的. 

比如你想打开文件管理器, 标准做法是在命令行中输入完整路径 C:\Windows\explorer.exe 然后回车即可. 任何文件都是这个逻辑, 你可以试试这样打开一个已有的 txt 文件.

但是对于一些需要经常打开的文件，每次都要输入完整路径的话就很麻烦, 有没有更便捷的做法呢? 有的, 那就是"PATH环境变量".

什么是"PATH环境变量"?

> PATH 是一个常用目录汇总表, 用来存放一些软件所在目录; 当一个软件的目录添加进此列表后, 便可省略完整路径, 只需敲出文件名即可运行; 对于一个命令行软件, 添加进了 PATH 就等于**"安装"**了这个软件.

打开环境变量编辑器即可发现, C:\Windows 已经在列表中了:

![img](https://pic1.zhimg.com/80/v2-d7613e65c214e113f66f935d3801b0ed_720w.webp?source=2c26e567)

这就是为什么直接输 explorer.exe 也能打开文件管理器。

又因为 exe 是二进制文件, 属于系统的最基本的文件, 所以可以省略后缀名, 直接输 explorer 也能打开文件管理器.

这时已经没人能看出来它原本是一个文件了, 它成为了一个**命令**.

这就是各种命令的真面目, 几乎每个命令都能在某个路径下找到对应的二进制文件.

- 命令行软件的典型用法：软件名+参数+参数...，以 [aria2](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=aria2&zhida_source=entity)，ffmpeg 为例......
- C语言的编译器 GCC 也是一个命令行软件.

### 2. 配置C/C++编译环境

在Windows上安装GCC编译器

1. 访问 [Releases · niXman/mingw-builds-binaries (github.com)](https://link.zhihu.com/?target=https%3A//github.com/niXman/mingw-builds-binaries/releases)
2. 下载 x86_64-13.1.0-release-win32-seh-ucrt.7z
3. 解压得 mingw64，放到合适的地方（推荐C盘根目录）
4. 把 C:/mingw64/bin 添加到 PATH 环境变量:

[win10如何添加环境变量path-百度经验](https://jingyan.baidu.com/article/49711c61197cadba451b7c6f.html)

5. 执行 gcc -v 看看反应, 没有报错说明安装成功.



### 3. 编译运行你的第一个程序

- 打开命令行, 运行 notepad hello.c (这样就新建了文本文件 hello.c)
- 粘贴如下内容并保存:

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("你好,我是程序 %s\n", argv[0]);
    if (argc > 1)
    {
        printf("太好了,有人来了!\n");
    }
    else
    {
        printf("怎么一个人都没有?\n");
    }

    for (int i = 1; i < argc; i++)
    {
        printf("欢迎你,%s!\n", argv[i]);
    }
    return 0;
}
```

- 回到命令行窗口, 执行：

```bash
gcc *.c -o hello
```

> 命令解释: 
> 调用 [gcc 编译器](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=gcc+编译器&zhida_source=entity), 并传入了这三个参数: "*.c",  "-o", "hello".
> *.c 表示选择当前目录下所有 .c 文件 ( 当然, 本例中你可以写 hello.c )
> -o 表示输出
> hello 表示会输出文件名为 hello ( 在 Windows 环境下生成的是 hello.exe )

成功后，hello.c的同一目录下会生成 hello.exe 文件

运行：

- 无参数运行：

```text
./hello
```

- 有参数运行：

```text
./hello Tom Jerry
```

如果出现乱码, 可能是因为文本默认使用了 UTF-8 编码，可以试试把文本另存为 [GBK 编码](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=GBK+编码&zhida_source=entity)重新编译. 

![img](https://picx.zhimg.com/80/v2-252a8cb70c53a6501079f22aaf539749_720w.webp?source=2c26e567)

或者不改文本编码，直接用下面这个更彻底的方法.

### *4. 把 Windows 切换到 UTF-8 模式，规避一切中文乱码现象

[win10 配置系统默认utf-8编码 - 聆风牧雨 - 博客园](https://www.cnblogs.com/walker-world/p/9548852.html)

这样设置完以后，你的代码文件夹路径，文件名，变量名，函数名都可以包含中文字符，管理代码时不用再小心翼翼的了。

这样做也有缺点，就是一些使用 GBK 编码的老软件/老游戏/老文件可能会出现乱码. 虽然这些老软件大部分已经淘汰了, 但是还是看个人情况自行选择吧。



## 第二课：函数和变量，通用程序模型 

### 函数是什么？

函数是最小的程序单位，是程序的细胞。

所以函数的基本运行逻辑和程序完全一致：输入数据->处理数据->输出结果。



上文中我们直观体会了函数的功能，仔细观察可以发现所有函数的都使用同一个格式：

```c
某种标记 函数名 (输入数据) { 语句 }
```

其实整个程序就是一个大函数： int main( ) {   }

其中开头的标记是在指明函数最终的计算结果是什么, int 表示最终得到一个整数, void 表示不给出任何结果 (这是可以的, 很多函数只需要默默做事情就行, 而不必报告结果).

观察 main 函数可看出，语句之间用英文分号隔开, 语句之间可以换行, 函数中可以调用其它函数...等等.

变量是什么？

变量，是数据的搬运工。



第三课：结构体，类

### 结构体是什么？

...

### 类是什么？

函数实现了操作的模块化；

结构体实现了数据的模块化，

类 = 结构体 + 函数；(C++内容)

所以类实现了统一的模块化。

所以 "面向对象" 不是什么高深的东西，它就是 "模块化思想" 的直接结果。



### 实现模块的无序组合的方法：声明与定义分离



C语言中，当你需要创建一个新的函数供其它函数使用时，需要注意它摆放的顺序。

因为C代码是从上到下顺序解析的。所以一个函数不能调用在它下面出现的函数。

```c
void A()
{ 
    B();
}

void B()
{ 
    A();
}
```

例如上述代码中 函数 A 试图使用函数 B，但在C语言中这是无法做到的，因为这一行之前没有出现过B的定义，所以A并不知道B的存在. 而 B 调用 A 则是可以的.

函数的依赖往往是树状的，花费一些力气可以整理出合理的顺序. 但是网状的依赖则很难通过调整顺序做到，而且面对成百上千个函数, 没人想在这些烦人的细节上浪费精力。

为了实现模块的无序组合，C语言搞出了一套方法，叫做**声明与定义分离**。使得模块只须声明了名字就可以呼叫了，模块内部的具体内容可以后面补上。

比如上面例子，可以改成：

```c
//此代码仅用于举例, 请勿实机运行, 会造成死循环

//声明
void A();
void B();

//定义
void A(){ B() }
void B(){ A() }
```

这样一来, 无论有多少函数 ,只要它存在于声明区, 就可以随便互相调用, 不用考虑顺序. 

知道这个动机以后，不用学就可以知道，结构体和类也一定存在类似的设计。

学C++面向对象时的时候，所谓[虚函数](https://zhida.zhihu.com/search?content_id=542784646&content_type=Answer&match_order=1&q=虚函数&zhida_source=entity)也是声明与定义分离的一种应用。











**本文的一个核心观点就是：先有需求，再有方法。需求出现之前，任何知识都是无用知识，任何工具都是废物。只传授知识, 不讲述动机的老师全都是不合格的老师.**

你之所以需要一个程序，其实是需要它替代你做一些繁重而无脑的事情。典型的例子就是把一件事重复N遍, 所以你需要循环。







## （未完待续）
