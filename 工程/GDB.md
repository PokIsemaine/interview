# GDB

gdb 调式哪些命令（查看frame信息的命令 ?， 打印一个变量的信息 info locals， info r 查看寄存器信息， 单步执行命令 -n, 跳转到一个函数里面 - s, 单步执行一个汇编指令 -si)

gdb如何看函数调用栈某一帧（不会）

gdb查看内存地址的命令

gdb如何查看其他线程

gdb查看所有线程线程栈的命令是什么

gdb 运行报错如何通过 core 文件找到错误

gdb 如何调试

gdb 断点实现

如何加断点，具体 api 调用

如何看函数调用栈

在某些情况下core文件的堆栈调用顺序会被溢出的内容覆盖，这时就没办法使用backtrace命令查看调用堆栈了，这种情况下如何定位问题

代码崩溃怎么解决，常见的原因是什么（core dump）

#### 写C++代码时有一类错误是 coredump ，很常见，你遇到过吗？怎么调试这个错误？

coredump是程序由于异常或者bug在运行时异常退出或者终止，在一定的条件下生成的一个叫做core的文件，这个core文件会记录程序在运行时的内存，寄存器状态，内存指针和函数堆栈信息等等。对这个文件进行分析可以定位到程序异常的时候对应的堆栈调用信息。

- 使用gdb命令对core文件进行调试

以下例子在Linux上编写一段代码并导致segment fault 并产生core文件

```plaintext
mkdir coredumpTest
vim coredumpTest.cpp
```

在编辑器内键入

```plaintext
#include<stdio.h>
int main(){
    int i;
    scanf("%d",i);//正确的应该是&i,这里使用i会导致segment fault
    printf("%d\n",i);
    return 0;
}
```

编译

```plaintext
g++ coredumpTest.cpp -g -o coredumpTest
```

运行

```plaintext
./coredumpTest
```

使用gdb调试coredump

```plaintext
gdb [可执行文件名] [core文件名]
```

### 普通调试

- step和next的区别？

​  next会直接执行到下一句 ,step会进入函数体内部执行

- list

​  查看源码

- set

 设置变量值

- backtrace

 查看函数调用的栈帧关系

- framework

 切换函数栈帧

- info

 查看函数内部局部变量

### 多线程调试

<img src="https://cdn.jsdelivr.net/gh/luogou/cloudimg/data/202203151449773.png" alt="这里写图片描述" style="zoom: 80%;float:left" />

- info threads

 查看所有线程，默认分支是主线程

- thread id

 切换线程

- bt

 查看每个线程的栈帧然后设置断点

- thread apply （n或all） 命令

 使用thread apply来让一个或是多个线程执行指定的命令。例如让所有的线程打印调用栈信息。

- set scheduler-locking on

 锁定只有当前的线程能够执行

### 遇到过程序崩溃的情况吗？怎么调试？gdb咋用的
