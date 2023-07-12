---
layout: blog
title: ComputerSystem
date: 2023-05-24 13:24:32
tags: softwareDevelopment
---

> 盖将自其变者而观之，则天地曾不能以一瞬；自其不变而观之，则物与我皆无尽也。——《前赤壁赋》

## \# 什么是计算机科学？

计算机就是输入 计算 输出，对于常见的计算机设备输入设备就是键盘、鼠标、麦克风、摄像头，输出设备就是音响、屏幕等；计算设备就是CPU等。自下而上，从简单的原理构建出错综复杂的系统，自上而下循着足迹一步一步去解构大系统，这就是计算机科学的巨大魅力。

## \# Hardware about CPU and RAM
<!--more-->
> 计算机科学，是逻辑、抽象、规则、协作的伟大发明，但是归根结底就是规则（协议）。在基础科学的基石上不断推进技术向纵深发展，形成一种文化，将人类的生活不断推向前进。

![computerHardware](computerHardware.jpg)

### \# CPU

CPU和内存是由许多晶体管组成的电子部件，通常称为IC(Integrated Circuit，集成电路)。CPU的内部由寄存器、控制器、运算器和时钟四个部分构成，各部分之间由电流信号相互连通。寄存器可用来暂存指令、数据等处理对象，可以将其看作是内存的一种。根据种类的不同，一个 CPU 内部会有20~100 个寄存器。控制器负责把内存上的指令、数据等读人寄存器并根据指令的执行结果来控制整个计算机。运算器负责运算从内存读入寄存器的数据。时钟负责发出 CPU 开始计时的时钟信号 。

---

内存指的是计算机的主存储器(main memory),简称主存。主存通过控制片等与CPU相连主要负责存储指令和数据。主存由可读写的元素构成，每个字节(1字节 =8位)都带有一个地址编号。CPU 可以通过该地址读取主存中的指令和数据，当然也可以写入数据。

---

我们可以将寄存器大致划分为八类可以看出，寄存器中存储的内容既可以是指令也可以是数据。其中，数据分为“用于运算的数值”和“表示内存地址的数值”两种。数据种类不同，存储该数值的寄存器也不同，CPU 中每个寄存器的功能都是不同的。用于运算的数值放在累加寄存器中存储，表示内存地址的数值则放在基址寄存器和变址寄存器中存储。

---

程序的流程分为顺序执行、条件分支和循环三种。顺序执行是指按照地址内容的顺序执行指令。条件分支是指根据条件执行任意地址的指令。循环是指重复执行同一地址的指令。顺序执行的情况比较简单，每执行一个指令程序计数器的值就自动加 1。但若程序中存在条件分支和循环，机器语言的指令就可以将程序计数器的值设定为任意地址(不是 +1)。这样一来，程序便可以返回到上一个地址来重复执行同一个指令，或者跳转到任意地址。

---
下图一般常见CPU的结构。

![CPU](8bitADD.png)
![CPU2](8bitADD2.png)
![CPU3](CPU.jpg)

|种类|功能|
|---|---|
|累加寄存器( accumulator register ) | 存储执行运算的数据和运算后的数据|
| 标志寄存器(flag register ) | 存储运算处理后的 CPU的状态 |
| 程序计数器( program counter ) | 存储下一条指令所在内存的地址 |
| 基址寄存器( base register ) |存储数据内存的起始地址 |
| 变址寄存器(index register) |存储基址寄存器的相对地址 |
| 通用寄存器( general purpose register ) |存储任意数据 |
| 指令寄存器(instruction register ) |过程序对该寄存器进行读写操作 |
| 栈寄存器(stack register ) |存储栈区域的起始地址 |

### \# 加法器可以衍生出减法器乘法器等

加法器（英语：adder）是一种用于执行加法运算的数位电路部件，是构成电子计算机核心微处理器中算术逻辑单元的基础.

半加器（英语：half adder）的功能是将两个一位二进制数相加。它有两个输出：

- 和：记作 S，来自对应的英语 Sum;
- 进位：记作 C;

全加器（full adder）将两个一位二进制数相加，并根据接收到的低位进位信号，输出和、进位输出。全加器的三个输入信号为两个加数A、B和低位进位Cin

触发器：电路的奇怪之处是:同样是在两个开关都断开的状态下，灯泡有时亮着，有时却不亮。当两个开关都断开时，电路有两个稳定态，这类电路统称为触发器 (Flip-Flop)，
触发器和跷跷板有着很强的相似性。跷跷板也有两个稳定状态，它不会长期停留在不稳定的中间位置。

>逻辑门电路如下例如：异或门,若两个输入的电平相异，则输出为高电平（1）；若两个输入的电平相同，则输出为低电平（0）。

|A  |B  |OR |NAND|XOR|
|---|---|---|--- |---|
| 0 | 0 | 0 | 1  | 0 |
| 0 | 1 | 1 | 1  | 1 |
| 1 | 0 | 1 | 1  | 1 |
| 1 | 1 | 1 | 0  | 0 |

### \# RAM

内存IC中有电源、地址信号、数据信号、控制信号等用于输入输出的大量引脚(IC 的引脚 )，通过为其指定地址(address )，来进行数据的读写。

数组是指多个同样数据类型的数据在内存中连续排列的形式。作为数组元素的各个数据会通过连续的编号被区分开来，这个编号称为索引(index)。指定索引后，就可以对该索引所对应地址的内存进行读写操作而索引和内存地址的变换工作则是由编译器自动实现的。
数组是链表和树结构的基础。

栈和队列，都可以不通过指定地址和索引来对数组的元素进行读写。需要临时保存计算过程中的数据、连接在计算机上的设备或者输人输出的数据时，都可以通过这些方法来使用内存。如果每次保存临时数据都需指定地址和索引，程序就会变得比较麻烦，因此要加以改进
栈和队列的区别在于数据出入的顺序是不同的。在对内存数据进行读写时，栈用的是 LIFO(Last Input First Out），后入先出)方式，而队列用的则是 FIFO( First Input First Out），先入先出)方式。如果我们在内存中预留出栈和队列所需要的空间，并确定好写入和读出的顺序就不用再指定地址和索引了。

下图是内存的组织结构
![RAM2](RAM0001.png)
![RAM3](RAM0002.png)
![RAM4](RAM0003.png)
![RAM1](RAM.png)

## \# 为什么我们需要编程语言？

>因为计算机时走向微型化，集成化我们“不能”使用物理的开关去操作内存，所以我们需要微观的操作方式，因此产生了计算机语言，用于人与机器的沟通，甚至说人与人的沟通也可以。

### \# What's the difference between Compiled language and Interpreted language?
  
- A compiled language is converted into machine code so that the processor can execute it.
- An interpreted language is a language in which the implementations execute instructions directly without earlier compiling a program into machine language.

|计算机语言|特点|
|---|---|
|C语言|C是一种通用的、过程式编程编程语言，支持结构化编程、词法作用域和递归，使用静态类型系统，并且广泛用于系统软件与应用软件的开发。|
|Python|Python是一种广泛使用的解释型、高级和通用的编程语言|
|Java|Java不同于一般的编译语言或解释型语言。它首先将源代码编译成字节码，再依赖各种不同平台上的虚拟机来解释执行字节码，从而具有“一次编写，到处运行”的跨平台特性。|
|Swift|Swift编程语言，支持多编程范式和编译式，面向对象，面向协议。|
|Go|Go是Google开发的一种静态强类型、编译型、并发型，并具有垃圾回收功能的编程语言|
|JS|JavaScript是一门基于原型和头等函数的多范式高级解释型编程语言，它支持面向对象程序设计、指令式编程和函数式编程。|
|C++|The main difference between C and C++ is that C++ support classes and objects, while C does not|

## \# What are Environment Variables?

Environment variables are global system variables accessible by all the processes/users running under the Operating System (OS), such as Windows, macOS and Linux. Environment variables are useful to store system-wide values, for examples,

PATH: the most frequently-used environment variable, which stores a list of directories to search for executable programs.
OS: the operating system.
COMPUTENAME, USERNAME: stores the computer and current user name.
SystemRoot: the system root directory.
(Windows) HOMEDRIVE, HOMEPATH: Current user's home directory.

## \# How to set Windows Environment Variables

 Set/Unset/Change an Environment Variable for the "Current" CMD Session

 ```bash
 
set varname  Display the value of the variable
set varname=value Set or change the value of the variable (Note: no space before and after '=')
set varname=    Delete the variable by setting to empty string (Note: nothing after '=')
set   Display ALL the environment variables





// Append a directory in front of the existing PATH
set PATH=c:\myBin;%PATH%
PATH=c:\myBin;[existing entries]
 ```

## \# How to Add or Change an Environment Variable "Permanently"

   Launch "Control Panel"

## \# How to set (macOS/Linux) Environment Variables

Most of the Unixes (Ubuntu/macOS) use the so-called Bash shell. Under bash shell:

To list all the environment variables, use the command "env" (or "printenv"). You could also use "set" to list all the variables, including all local variables.
To reference a variable, use $varname, with a prefix '$' (Windows uses %varname%).
To print the value of a particular variable, use the command "echo $varname".
To set an environment variable, use the command "export varname=value", which sets the variable and exports it to the global environment (available to other processes). Enclosed the value with double quotes if it contains spaces.
To set a local variable, use the command "varname=value" (or "set varname=value"). Local variable is available within this process only.
To unset a local variable, use command "varname=", i.e., set to empty string (or "unset varname").

## \# How to Set an Environment Variable Permanently in Bash Shell

You can set an environment variable permanently by placing an export command in your Bash shell's startup script "~/.bashrc" (or "~/.bash_profile", or "~/.profile") of your home directory; or "/etc/profile" for system-wide operations. Take note that files beginning with dot (.) is hidden by default. To display hidden files, use command "ls -a" or "ls -al".

For example, to add a directory to the PATH environment variable, add the following line at the end of "~/.bashrc" (or "~/.bash_profile", or "~/.profile"), where ~ denotes the home directory of the current user, or "/etc/profile" for ALL users.

```bash
// Append a directory in front of the existing PATH
export PATH=/usr/local/mysql/bin:$PATH
// Refresh the bash shell
source ~/.bashrc
// or
source ~/.bash_profile
source ~/.profile
```
