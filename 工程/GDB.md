# GDB

<https://github.com/hellogcc/100-gdb-tips>

### 信息显示

#### [显示gdb版本信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/show-version.md)

使用gdb时，如果想查看gdb版本信息，可以使用“`show version`”命令:

#### [显示gdb版权相关信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/show-copying-warranty.md)

使用gdb时，如果想查看gdb版权相关信息，可以使用“`show copying`”命令:

#### [启动时不显示提示信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/start-gdb-silently.md)

如果不想显示 gdb 启动信息，则可以使用`-q`选项把提示信息关掉:

#### [退出时不显示提示信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/quit-gdb-silently.md)

如果不想显示退出提示信息，则可以在gdb中使用如下命令把提示信息关掉:

```
(gdb) set confirm off
```

也可以把这个命令加到.gdbinit文件里

#### [输出信息多时不会暂停输出](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-pagination-off.md)

* 有时当gdb输出信息较多时，gdb会暂停输出，并会打印“`---Type <return> to continue, or q <return> to quit---`”这样的提示信息，如下面所示：

```
 81 process 2639102      0xff04af84 in __lwp_park () from /usr/lib/libc.so.1
 80 process 2573566      0xff04af84 in __lwp_park () from /usr/lib/libc.so.1
---Type <return> to continue, or q <return> to quit---Quit
```

解决办法是使用“`set pagination off`”或者“`set height 0`”命令。这样gdb就会全部输出，中间不会暂停。

### 函数

#### [列出函数的名字](https://github.com/hellogcc/100-gdb-tips/blob/master/src/info-function.md)

使用gdb调试时，使用“`info functions`”命令可以列出可执行文件的所有函数名称。

#### [是否进入带调试信息的函数](https://github.com/hellogcc/100-gdb-tips/blob/master/src/step-and-next-function.md)

* 使用gdb调试遇到函数时，使用step命令（缩写为s）可以进入函数（函数必须有调试信息）
* 可以使用next命令（缩写为n）不进入函数，gdb会等函数执行完，再显示下一行要执行的程序代码
* 单步执行一个汇编指令 stepi（缩写为 si)
* 单步一条机器指令，不进入函数 nexti（缩写为 ni）

next和nexti(即n和ni)是下一条，不进入函数内部，比如说在某一行发生了函数调用，next/nexti就继续到下一行。next是在源码层面的下一行，而nexti就是机器指令层面的，单步到下一个机器指令。

step和stepi(即s和si)就是单步步入，进入函数内部，比如说在某一行发生了函数调用，step/stepi就会进入函数体内部，把函数体执行一遍，再返回执行下一条指令。同理，step是在源码层面的操作指令，stepi是在机器指令层面的。

#### [进入不带调试信息的函数](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-step-mode-on.md)

默认情况下，gdb不会进入不带调试信息的函数。以上面代码为例：

```
(gdb) n
15              printf("%d,%d,%d,%d\n", st.a, st.b, st.c, st.d);
(gdb) s
1,2,3,4
16              return 0;
```

可以看到由于printf函数不带调试信息，所以“s”命令（s是“step”缩写）无法进入printf函数。

可以执行“set step-mode on”命令，这样gdb就不会跳过没有调试信息的函数：

```
(gdb) set step-mode on
(gdb) n
15              printf("%d,%d,%d,%d\n", st.a, st.b, st.c, st.d);
(gdb) s
0x00007ffff7a993b0 in printf () from /lib64/libc.so.6
(gdb) s
0x00007ffff7a993b7 in printf () from /lib64/libc.so.6
```

可以看到gdb进入了printf函数，接下来可以使用调试汇编程序的办法去调试函数。

#### [退出正在调试的函数](https://github.com/hellogcc/100-gdb-tips/blob/master/src/finish-and-return.md)

当单步调试一个函数时，如果不想继续跟踪下去了，可以有两种方式退出。

第一种用“`finish`”命令，这样函数会继续执行完，并且打印返回值，然后等待输入接下来的命令。以上面代码为例：

```
(gdb) n
17          a = func();
(gdb) s
func () at a.c:5
5               int i = 0;
(gdb) n
7               i += 2;
(gdb) fin
find    finish
(gdb) finish
Run till exit from #0  func () at a.c:7
0x08050978 in main () at a.c:17
17          a = func();
Value returned is $1 = 20
```

可以看到当不想再继续跟踪`func`函数时，执行完“`finish`”命令，gdb会打印结果：“`20`”，然后停在那里。

详情参见[gdb手册](https://sourceware.org/gdb/onlinedocs/gdb/Continuing-and-Stepping.html)

第二种用“`return`”命令，这样函数不会继续执行下面的语句，而是直接返回。也可以用“`return expression`”命令指定函数的返回值。仍以上面代码为例：

```
(gdb) n
17          a = func();
(gdb) s
func () at a.c:5
5               int i = 0;
(gdb) n
7               i += 2;
(gdb) n
8               i *= 10;
(gdb) re
record              remove-inferiors    return              reverse-next        reverse-step
refresh             remove-symbol-file  reverse-continue    reverse-nexti       reverse-stepi
remote              restore             reverse-finish      reverse-search
(gdb) return 40
Make func return now? (y or n) y
#0  0x08050978 in main () at a.c:17
17          a = func();
(gdb) n
18          printf("%d\n", a);
(gdb)
40
19          return 0;
```

可以看到“`return`”命令退出了函数并且修改了函数的返回值。

详情参见[gdb手册](https://sourceware.org/gdb/onlinedocs/gdb/Returning.html#Returning)

#### [直接执行函数](https://github.com/hellogcc/100-gdb-tips/blob/master/src/call-func.md)

使用gdb调试程序时，可以使用“`call`”或“`print`”命令直接调用函数执行

#### [打印函数堆栈帧信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/info-frame.md)

使用gdb调试程序时，可以使用“`i frame`”命令（`i`是`info`命令缩写）显示函数堆栈帧信息。

#### [打印尾调用堆栈帧信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-debug-entry-values.md)

```c
#include<stdio.h>
void a(void)
{
        printf("Tail call frame\n");
}

void b(void)
{
        a();
}

void c(void)
{
        b();
}

int main(void)
{
        c();
        return 0;
}
```

当一个函数最后一条指令是调用另外一个函数时，开启优化选项的编译器常常以最后被调用的函数返回值作为调用者的返回值，这称之为“尾调用（Tail call）”。以上面程序为例，编译程序（使用‘-O’）：

```
gcc -g -O -o test test.c
```

查看`main`函数汇编代码：

```
(gdb) disassemble main
Dump of assembler code for function main:
0x0000000000400565 <+0>:     sub    $0x8,%rsp
0x0000000000400569 <+4>:     callq  0x400536 <a>
0x000000000040056e <+9>:     mov    $0x0,%eax
0x0000000000400573 <+14>:    add    $0x8,%rsp
0x0000000000400577 <+18>:    retq
```

可以看到`main`函数直接调用了函数`a`，根本看不到函数`b`和函数`c`的影子。

在函数`a`入口处打上断点，程序停止后，打印堆栈帧信息：

```
(gdb) i frame
Stack level 0, frame at 0x7fffffffe590:
 rip = 0x400536 in a (test.c:4); saved rip = 0x40056e
 called by frame at 0x7fffffffe5a0
 source language c.
 Arglist at 0x7fffffffe580, args:
 Locals at 0x7fffffffe580, Previous frame's sp is 0x7fffffffe590
 Saved registers:
  rip at 0x7fffffffe588
```

看不到尾调用的相关信息。

可以设置“`debug entry-values`”选项为非0的值，这样除了输出正常的函数堆栈帧信息以外，还可以输出尾调用的相关信息：

```
(gdb) set debug entry-values 1
(gdb) b test.c:4
Breakpoint 1 at 0x400536: file test.c, line 4.
(gdb) r
Starting program: /home/nanxiao/test

Breakpoint 1, a () at test.c:4
4       {
(gdb) i frame
tailcall: initial:
Stack level 0, frame at 0x7fffffffe590:
 rip = 0x400536 in a (test.c:4); saved rip = 0x40056e
 called by frame at 0x7fffffffe5a0
 source language c.
 Arglist at 0x7fffffffe580, args:
 Locals at 0x7fffffffe580, Previous frame's sp is 0x7fffffffe590
 Saved registers:
  rip at 0x7fffffffe588
```

可以看到输出了“`tailcall: initial:`”信息。

#### [选择函数堆栈帧](https://github.com/hellogcc/100-gdb-tips/blob/master/src/select-frame.md)

用gdb调试程序时，当程序暂停后，可以用“`frame n`”命令选择函数堆栈帧，其中`n`是层数。以上面程序为例：

```
(gdb) b test.c:5
Breakpoint 1 at 0x40053d: file test.c, line 5.
(gdb) r
Starting program: /home/nanxiao/test

Breakpoint 1, func1 (a=10) at test.c:5
5               return 2 * a;
(gdb) bt
#0  func1 (a=10) at test.c:5
#1  0x0000000000400560 in func2 (a=10) at test.c:11
#2  0x0000000000400586 in func3 (a=10) at test.c:18
#3  0x000000000040059e in main () at test.c:24
(gdb) frame 2
#2  0x0000000000400586 in func3 (a=10) at test.c:18
18              c = 2 * func2(a);
```

可以看到程序断住后，最内层的函数帧为第`0`帧。执行`frame 2`命令后，当前的堆栈帧变成了`fun3`的函数帧。

也可以用“`frame addr`”命令选择函数堆栈帧，其中`addr`是堆栈地址。仍以上面程序为例：

```
(gdb) frame 2
#2  0x0000000000400586 in func3 (a=10) at test.c:18
18              c = 2 * func2(a);
(gdb) i frame
Stack level 2, frame at 0x7fffffffe590:
 rip = 0x400586 in func3 (test.c:18); saved rip = 0x40059e
 called by frame at 0x7fffffffe5a0, caller of frame at 0x7fffffffe568
 source language c.
 Arglist at 0x7fffffffe580, args: a=10
 Locals at 0x7fffffffe580, Previous frame's sp is 0x7fffffffe590
 Saved registers:
  rbp at 0x7fffffffe580, rip at 0x7fffffffe588
(gdb) frame 0x7fffffffe568
#1  0x0000000000400560 in func2 (a=10) at test.c:11
11              c = 2 * func1(a);
```

使用“`i frame`”命令可以知道`0x7fffffffe568`是`func2`的函数堆栈帧地址，使用“`frame 0x7fffffffe568`”可以切换到`func2`的函数堆栈帧。

#### [向上或向下切换函数堆栈帧](https://github.com/hellogcc/100-gdb-tips/blob/master/src/up-down-select-frame.md)

用gdb调试程序时，当程序暂停后，可以用“`up n`”或“`down n`”命令向上或向下选择函数堆栈帧，其中`n`是层数

### 断点

#### [在匿名空间设置断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/break-anonymous-namespace.md)

在gdb中，如果要对namespace Foo中的foo函数设置断点，可以使用如下命令：

```
(gdb) b Foo::foo
```

如果要对匿名空间中的bar函数设置断点，可以使用如下命令：

```
(gdb) b (anonymous namespace)::bar
```

#### [在程序地址上打断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/break-on-address.md)

当调试汇编程序，或者没有调试信息的程序时，经常需要在程序地址上打断点，方法为`b *address`

#### [在程序入口处打断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/break-on-entry.md)

当调试没有调试信息的程序时，直接运行`start`命令是没有效果的：

```
(gdb) start
Function "main" not defined.
```

如果不知道main在何处，那么可以在程序入口处打断点。先通过`readelf`或者进入gdb，执行`info files`获得入口地址，然后：

```
(gdb) b *0x400440
(gdb) r
```

#### [在文件行号上打断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/break-on-linenum.md)

这个比较简单，如果要在当前文件中的某一行打断点，直接`b linenum`即可，例如：

```
(gdb) b 7
```

也可以显式指定文件，`b file:linenum`例如：

```
(gdb) b file.c:6
Breakpoint 1 at 0x40053b: file.c:6. (2 locations)
(gdb) i breakpoints 
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   <MULTIPLE>         
1.1                         y     0x000000000040053b in print_a at a/file.c:6
1.2                         y     0x000000000040054b in print_b at b/file.c:6
```

可以看出，gdb会对所有匹配的文件设置断点。你可以通过指定（部分）路径，来区分相同的文件名：

```
(gdb) b a/file.c:6
```

注意：通过行号进行设置断点的一个弊端是，如果你更改了源程序，那么之前设置的断点就可能不是你想要的了。

#### [保存已经设置的断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/save-breakpoints.md)

在gdb中，可以使用如下命令将设置的断点保存下来：

```
(gdb) save breakpoints file-name-to-save
```

下此调试时，可以使用如下命令批量设置保存的断点：

```
(gdb) source file-name-to-save
(gdb) info breakpoints 
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000005a7af0 in gdb_main at /home/xmj/project/binutils-trunk/gdb/main.c:1061
2       breakpoint     keep y   0x00000000005a6bd0 in captured_main at /home/xmj/project/binutils-trunk/gdb/main.c:310
3       breakpoint     keep y   0x00000000005a68b0 in captured_command_loop at /home/xmj/project/binutils-trunk/gdb/main.c:261
```

#### [设置临时断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-tbreak.md)

在使用gdb时，如果想让断点只生效一次，可以使用“tbreak”命令（缩写为：tb）。以上面程序为例：

```
(gdb) tb a.c:15
Temporary breakpoint 1 at 0x400500: file a.c, line 15.
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     del  y   0x0000000000400500 in main at a.c:15
(gdb) r
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:15
15              printf("%d,%d,%d,%d\n", st.a, st.b, st.c, st.d);
(gdb) i b
No breakpoints or watchpoints.
```

首先在文件的第15行设置临时断点，当程序断住后，用“i b”（"info breakpoints"缩写）命令查看断点，发现断点没有了。也就是断点命中一次后，就被删掉了。

#### [设置条件断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-condition-break.md)

gdb可以设置条件断点，也就是只有在条件满足时，断点才会被触发，命令是“`break … if cond`”。以上面程序为例：

```
(gdb) start
Temporary breakpoint 1 at 0x4004cc: file a.c, line 5.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:5
5                       int i = 0;
(gdb) b 10 if i==101
Breakpoint 2 at 0x4004e3: file a.c, line 10.
(gdb) r
Starting program: /data2/home/nanxiao/a

Breakpoint 2, main () at a.c:10
10                                      sum += i;
(gdb) p sum
$1 = 5050
```

可以看到设定断点只在`i`的值为`101`时触发，此时打印`sum`的值为`5050`。

#### [忽略断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/ignore-break.md)

在设置断点以后，可以忽略断点，命令是“`ignore bnum count`”：意思是接下来`count`次编号为`bnum`的断点触发都不会让程序中断，只有第`count + 1`次断点触发才会让程序中断。以上面程序为例：

```
(gdb) b 10
Breakpoint 1 at 0x4004e3: file a.c, line 10.
(gdb) ignore 1 5
Will ignore next 5 crossings of breakpoint 1.
(gdb) r
Starting program: /data2/home/nanxiao/a

Breakpoint 1, main () at a.c:10
10                                      sum += i;
(gdb) p i
$1 = 6
```

可以看到设定忽略断点前`5`次触发后，第一次断点断住时，打印`i`的值是`6`。如果想让断点下次就生效，可以将`count`置为`0`：“`ignore 1 0`”。

### 观察点

#### [设置观察点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-watchpoint.md)

gdb可以使用“`watch`”命令设置观察点，也就是当一个变量值发生变化时，程序会停下来。以上面程序为例:

```
(gdb) start
Temporary breakpoint 1 at 0x4005a8: file a.c, line 19.
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".

Temporary breakpoint 1, main () at a.c:19
19              pthread_create(&t1, NULL, thread1_func, "Thread 1");
(gdb) watch a
Hardware watchpoint 2: a
(gdb) r
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
[New Thread 0x7ffff782c700 (LWP 8813)]
[Switching to Thread 0x7ffff782c700 (LWP 8813)]
Hardware watchpoint 2: a

Old value = 0
New value = 1
thread1_func (p_arg=0x4006d8) at a.c:11
11                      sleep(10);
(gdb) c
Continuing.
Hardware watchpoint 2: a

Old value = 1
New value = 2
thread1_func (p_arg=0x4006d8) at a.c:11
11                      sleep(10);
```

可以看到，使用“`watch a`”命令以后，当`a`的值变化：由`0`变成`1`，由`1`变成`2`，程序都会停下来。 此外也可以使用“`watch *(data type*)address`”这样的命令，仍以上面程序为例:

```
(gdb) p &a
$1 = (int *) 0x6009c8 <a>
(gdb) watch *(int*)0x6009c8
Hardware watchpoint 2: *(int*)0x6009c8
(gdb) r
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
[New Thread 0x7ffff782c700 (LWP 15431)]
[Switching to Thread 0x7ffff782c700 (LWP 15431)]
Hardware watchpoint 2: *(int*)0x6009c8

Old value = 0
New value = 1
thread1_func (p_arg=0x4006d8) at a.c:11
11                      sleep(10);
(gdb) c
Continuing.
Hardware watchpoint 2: *(int*)0x6009c8

Old value = 1
New value = 2
thread1_func (p_arg=0x4006d8) at a.c:11
11                      sleep(10);
```

先得到`a`的地址：`0x6009c8`，接着用“`watch *(int*)0x6009c8`”设置观察点，可以看到同“`watch a`”命令效果一样。 观察点可以通过软件或硬件的方式实现，取决于具体的系统。但是软件实现的观察点会导致程序运行很慢，使用时需注意。参见[gdb手册](https://sourceware.org/gdb/onlinedocs/gdb/Set-Watchpoints.html).

如果系统支持硬件观测的话，当设置观测点是会打印如下信息： Hardware watchpoint num: expr

如果不想用硬件观测点的话可如下设置： set can-use-hw-watchpoints

**查看断点**

列出当前所设置了的所有观察点： info watchpoints

watch 所设置的断点也可以用控制断点的命令来控制。如 disable、enable、delete等

#### [设置观察点只针对特定线程生效](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-watchpoint-on-specified-thread.md)

gdb可以使用“`watch expr thread threadnum`”命令设置观察点只针对特定线程生效，也就是只有编号为`threadnum`的线程改变了变量的值，程序才会停下来，其它编号线程改变变量的值不会让程序停住。以上面程序为例:

```
(gdb) start
Temporary breakpoint 1 at 0x4005d4: file a.c, line 28.
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".

Temporary breakpoint 1, main () at a.c:28
28              pthread_create(&t1, NULL, thread1_func, "Thread 1");
(gdb) n
[New Thread 0x7ffff782c700 (LWP 25443)]
29              pthread_create(&t2, NULL, thread2_func, "Thread 2");
(gdb)
[New Thread 0x7ffff6e2b700 (LWP 25444)]
31              sleep(1000);
(gdb) i threads
  Id   Target Id         Frame
  3    Thread 0x7ffff6e2b700 (LWP 25444) 0x00007ffff7915911 in clone () from /lib64/libc.so.6
  2    Thread 0x7ffff782c700 (LWP 25443) 0x00007ffff78d9bcd in nanosleep () from /lib64/libc.so.6
* 1    Thread 0x7ffff7fe9700 (LWP 25413) main () at a.c:31
(gdb) wa a thread 2
Hardware watchpoint 2: a
(gdb) c
Continuing.
[Switching to Thread 0x7ffff782c700 (LWP 25443)]
Hardware watchpoint 2: a

Old value = 1
New value = 3
thread1_func (p_arg=0x400718) at a.c:11
11                      sleep(10);
(gdb) c
Continuing.
Hardware watchpoint 2: a

Old value = 3
New value = 5
thread1_func (p_arg=0x400718) at a.c:11
11                      sleep(10);
(gdb) c
Continuing.
Hardware watchpoint 2: a

Old value = 5
New value = 7
thread1_func (p_arg=0x400718) at a.c:11
11                      sleep(10);
```

可以看到，使用“`wa a thread 2`”命令（`wa`是`watch`命令的缩写）以后，只有`thread1_func`改变`a`的值才会让程序停下来。
需要注意的是这种针对特定线程设置观察点方式只对硬件观察点才生效

#### [设置读观察点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-read-watchpoint.md)

gdb可以使用“`rwatch`”命令设置读观察点，也就是当发生读取变量行为时，程序就会暂停住。以上面程序为例:

```
(gdb) start
Temporary breakpoint 1 at 0x4005f3: file a.c, line 19.
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".

Temporary breakpoint 1, main () at a.c:19
19              pthread_create(&t1, NULL, thread1_func, "Thread 1");
(gdb) rw a
Hardware read watchpoint 2: a
(gdb) c
Continuing.
[New Thread 0x7ffff782c700 (LWP 5540)]
[Switching to Thread 0x7ffff782c700 (LWP 5540)]
Hardware read watchpoint 2: a

Value = 0
0x00000000004005c6 in thread1_func (p_arg=0x40071c) at a.c:10
10                      printf("%d\n", a);
(gdb) c
Continuing.
0
Hardware read watchpoint 2: a

Value = 0
0x00000000004005c6 in thread1_func (p_arg=0x40071c) at a.c:10
10                      printf("%d\n", a);
(gdb) c
Continuing.
0
Hardware read watchpoint 2: a

Value = 0
0x00000000004005c6 in thread1_func (p_arg=0x40071c) at a.c:10
10                      printf("%d\n", a);
```

可以看到，使用“`rw a`”命令（`rw`是`rwatch`命令的缩写）以后，每次访问`a`的值都会让程序停下来。
需要注意的是`rwatch`命令只对硬件观察点才生效

#### [设置读写观察点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-read-write-watchpoint.md)

gdb可以使用“`awatch`”命令设置读写观察点，也就是当发生读取变量或改变变量值的行为时，程序就会暂停住。以上面程序为例:

```
(gdb) aw a
Hardware access (read/write) watchpoint 1: a
(gdb) r
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
[New Thread 0x7ffff782c700 (LWP 16938)]
[Switching to Thread 0x7ffff782c700 (LWP 16938)]
Hardware access (read/write) watchpoint 1: a

Value = 0
0x00000000004005c6 in thread1_func (p_arg=0x40076c) at a.c:10
10                      a++;
(gdb) c
Continuing.
Hardware access (read/write) watchpoint 1: a

Old value = 0
New value = 1
thread1_func (p_arg=0x40076c) at a.c:11
11                      sleep(10);
(gdb) c
Continuing.
[New Thread 0x7ffff6e2b700 (LWP 16939)]
[Switching to Thread 0x7ffff6e2b700 (LWP 16939)]
Hardware access (read/write) watchpoint 1: a

Value = 1
0x00000000004005f2 in thread2_func (p_arg=0x400775) at a.c:19
19                      printf("%d\n", a);;
(gdb) c
Continuing.
1
[Switching to Thread 0x7ffff782c700 (LWP 16938)]
Hardware access (read/write) watchpoint 1: a

Value = 1
0x00000000004005c6 in thread1_func (p_arg=0x40076c) at a.c:10
10                      a++;
```

可以看到，使用“`aw a`”命令（`aw`是`awatch`命令的缩写）以后，每次读取或改变`a`的值都会让程序停下来。
需要注意的是`awatch`命令只对硬件观察点才生效

### CatchPoint

* [让catchpoint只触发一次](https://github.com/hellogcc/100-gdb-tips/blob/master/src/tcatch.md)
* [为fork调用设置catchpoint](https://github.com/hellogcc/100-gdb-tips/blob/master/src/catch-fork.md)
* [为vfork调用设置catchpoint](https://github.com/hellogcc/100-gdb-tips/blob/master/src/catch-vfork.md)
* [为exec调用设置catchpoint](https://github.com/hellogcc/100-gdb-tips/blob/master/src/catch-exec.md)
* [为系统调用设置catchpoint](https://github.com/hellogcc/100-gdb-tips/blob/master/src/catch-syscall.md)
* [通过为ptrace调用设置catchpoint破解anti-debugging的程序](https://github.com/hellogcc/100-gdb-tips/blob/master/src/catch-ptrace.md)

### 打印

* [打印ASCII和宽字符字符串](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-ascii-and-wide-string.md)
* [打印STL容器中的内容](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-STL-container.md)
* [打印大数组中的内容](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-large-array.md)
* [打印数组中任意连续元素值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-consecutive-array-elements.md)
* [打印数组的索引下标](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-array-indexes.md)
* [格式化打印数组](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-formatted-array.md)

#### [打印函数局部变量的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-local-variables.md)

如果要打印函数局部变量的值，可以使用“bt full”命令（bt是backtrace的缩写）。首先我们在函数fun_a里打上断点，当程序断住时，显示调用栈信息：

```
(gdb) bt
#0  fun_a () at a.c:6
#1  0x000109b0 in fun_b () at a.c:12
#2  0x000109e4 in fun_c () at a.c:19
#3  0x00010a18 in fun_d () at a.c:26
#4  0x00010a4c in main () at a.c:33
```

接下来，用“bt full”命令显示各个函数的局部变量值：

```
(gdb) bt full
#0  fun_a () at a.c:6
        a = 0
#1  0x000109b0 in fun_b () at a.c:12
        b = 1
#2  0x000109e4 in fun_c () at a.c:19
        c = 2
#3  0x00010a18 in fun_d () at a.c:26
        d = 3
#4  0x00010a4c in main () at a.c:33
        var = -1
```

也可以使用如下“bt full n”，意思是从内向外显示n个栈桢，及其局部变量，例如：

```
(gdb) bt full 2
#0  fun_a () at a.c:6
        a = 0
#1  0x000109b0 in fun_b () at a.c:12
        b = 1
(More stack frames follow...)
```

而“bt full -n”，意思是从外向内显示n个栈桢，及其局部变量，例如：

```
(gdb) bt full -2
#3  0x00010a18 in fun_d () at a.c:26
        d = 3
#4  0x00010a4c in main () at a.c:33
        var = -1
```

如果只是想打印当前函数局部变量的值，可以使用如下命令：

```
(gdb) info locals
a = 0
```

#### [打印进程内存信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-process-memory.md)

用gdb调试程序时，如果想查看进程的内存映射信息，可以使用“i proc mappings”命令（i是info命令缩写），例如:

```
(gdb) i proc mappings
process 27676 flags:
PR_STOPPED Process (LWP) is stopped
PR_ISTOP Stopped on an event of interest
PR_RLC Run-on-last-close is in effect
PR_MSACCT Microstate accounting enabled
PR_PCOMPAT Micro-state accounting inherited on fork
PR_FAULTED : Incurred a traced hardware fault FLTBPT: Breakpoint trap

Mapped address spaces:

    Start Addr   End Addr       Size     Offset   Flags
     0x8046000  0x8047fff     0x2000 0xfffff000 -s--rwx
     0x8050000  0x8050fff     0x1000          0 ----r-x
     0x8060000  0x8060fff     0x1000          0 ----rwx
    0xfee40000 0xfef4efff   0x10f000          0 ----r-x
    0xfef50000 0xfef55fff     0x6000          0 ----rwx
    0xfef5f000 0xfef66fff     0x8000   0x10f000 ----rwx
    0xfef67000 0xfef68fff     0x2000          0 ----rwx
    0xfef70000 0xfef70fff     0x1000          0 ----rwx
    0xfef80000 0xfef80fff     0x1000          0 ---sr--
    0xfef90000 0xfef90fff     0x1000          0 ----rw-
    0xfefa0000 0xfefa0fff     0x1000          0 ----rw-
    0xfefb0000 0xfefb0fff     0x1000          0 ----rwx
    0xfefc0000 0xfefeafff    0x2b000          0 ----r-x
    0xfeff0000 0xfeff0fff     0x1000          0 ----rwx
    0xfeffb000 0xfeffcfff     0x2000    0x2b000 ----rwx
    0xfeffd000 0xfeffdfff     0x1000          0 ----rwx
```

首先输出了进程的flags，接着是进程的内存映射信息。

此外，也可以用"i files"（还有一个同样作用的命令：“i target”）命令，它可以更详细地输出进程的内存信息，包括引用的动态链接库等等，例如：

```
(gdb) i files
Symbols from "/data1/nan/a".
Unix /proc child process:
    Using the running image of child Thread 1 (LWP 1) via /proc.
    While running this, GDB does not access memory from...
Local exec file:
    `/data1/nan/a', file type elf32-i386-sol2.
    Entry point: 0x8050950
    0x080500f4 - 0x08050105 is .interp
    0x08050108 - 0x08050114 is .eh_frame_hdr
    0x08050114 - 0x08050218 is .hash
    0x08050218 - 0x08050418 is .dynsym
    0x08050418 - 0x080507e6 is .dynstr
    0x080507e8 - 0x08050818 is .SUNW_version
    0x08050818 - 0x08050858 is .SUNW_versym
    0x08050858 - 0x08050890 is .SUNW_reloc
    0x08050890 - 0x080508c8 is .rel.plt
    0x080508c8 - 0x08050948 is .plt
    ......
 0xfef5fb58 - 0xfef5fc48 is .dynamic in /usr/lib/libc.so.1
    0xfef5fc80 - 0xfef650e2 is .data in /usr/lib/libc.so.1
    0xfef650e2 - 0xfef650e2 is .bssf in /usr/lib/libc.so.1
    0xfef650e8 - 0xfef65be0 is .picdata in /usr/lib/libc.so.1
    0xfef65be0 - 0xfef666a7 is .data1 in /usr/lib/libc.so.1
    0xfef666a8 - 0xfef680dc is .bss in /usr/lib/libc.so.1
```

#### [打印静态变量的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-static-variables.md)

在gdb中，如果直接打印静态变量，则结果并不一定是你想要的：

```
$ gcc -g main.c static-1.c static-2.c
$ gdb -q ./a.out
(gdb) start
(gdb) p var
$1 = 2

$ gcc -g main.c static-2.c static-1.c
$ gdb -q ./a.out
(gdb) start
(gdb) p var
$1 = 1
```

你可以显式地指定文件名（上下文）：

```
(gdb) p 'static-1.c'::var
$1 = 1
(gdb) p 'static-2.c'::var
$2 = 2
```

#### [打印变量的类型和所在文件](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-variable-info.md)

在gdb中，可以使用如下命令查看变量的类型：

```
(gdb) whatis he
type = struct child
```

如果想查看详细的类型信息：

```
(gdb) ptype he
type = struct child {
    char name[10];
    enum {boy, girl} gender;
}
```

如果想查看定义该变量的文件：

```
(gdb) i variables he
All variables matching regular expression "he":

File variable.c:
struct child he;

Non-debugging symbols:
0x0000000000402030  she
0x00007ffff7dd3380  __check_rhosts_file
```

哦，gdb会显示所有包含（匹配）该表达式的变量。如果只想查看完全匹配给定名字的变量：

```
(gdb) i variables ^he$
All variables matching regular expression "^he$":

File variable.c:
struct child he;
```

注：`info variables`不会显示局部变量，即使是static的也没有太多的信息。

#### [打印内存的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/examine-memory.md)

gdb中使用“`x`”命令来打印内存的值，格式为“`x/nfu addr`”。含义为以`f`格式打印从`addr`开始的`n`个长度单元为`u`的内存值。参数具体含义如下：
a）n：输出单元的个数。
b）f：是输出格式。比如`x`是以16进制形式输出，`o`是以8进制形式输出,等等。
c）u：标明一个单元的长度。`b`是一个`byte`，`h`是两个`byte`（halfword），`w`是四个`byte`（word），`g`是八个`byte`（giant word）。

以上面程序为例：
（1） 以16进制格式打印数组前`a`16个byte的值：

```
(gdb) x/16xb a
0x7fffffffe4a0: 0x00    0x01    0x02    0x03    0x04    0x05    0x06    0x07
0x7fffffffe4a8: 0x08    0x09    0x0a    0x0b    0x0c    0x0d    0x0e    0x0f
```

（2） 以无符号10进制格式打印数组`a`前16个byte的值：

```
(gdb) x/16ub a
0x7fffffffe4a0: 0       1       2       3       4       5       6       7
0x7fffffffe4a8: 8       9       10      11      12      13      14      15
```

（3） 以2进制格式打印数组前16个`a`byte的值：

```
(gdb) x/16tb a
0x7fffffffe4a0: 00000000        00000001        00000010        00000011        00000100        00000101        00000110        00000111
0x7fffffffe4a8: 00001000        00001001        00001010        00001011        00001100        00001101        00001110        00001111
```

（4） 以16进制格式打印数组`a`前16个word（4个byte）的值：

```
(gdb) x/16xw a
0x7fffffffe4a0: 0x03020100      0x07060504      0x0b0a0908      0x0f0e0d0c
0x7fffffffe4b0: 0x13121110      0x17161514      0x1b1a1918      0x1f1e1d1c
0x7fffffffe4c0: 0x23222120      0x27262524      0x2b2a2928      0x2f2e2d2c
0x7fffffffe4d0: 0x33323130      0x37363534      0x3b3a3938      0x3f3e3d3c
```

#### [打印源代码行](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-source-lines.md)

如上所示，在gdb中可以使用`list`（简写为l）命令来显示源代码以及行号。`list`命令可以指定行号，函数：

```
(gdb) l 24
(gdb) l main
```

还可以指定向前或向后打印：

```
(gdb) l -
(gdb) l +
```

还可以指定范围：

```
(gdb) l 1,10
```

* [每行打印一个结构体成员](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-print-pretty-on.md)
* [按照派生类型打印对象](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-derived-type.md)
* [指定程序的输入输出设备](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-io-tty.md)
* [使用“$\_”和“$\__”变量](https://github.com/hellogcc/100-gdb-tips/blob/master/src/use-$_-$__-variables.md)
* [打印程序动态分配内存的信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-malloc-memory.md)

#### [打印调用栈帧中变量的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-frame-variables.md)

在gdb中，如果想查看调用栈帧中的变量，可以先切换到该栈帧中，然后打印：

```
(gdb) b func1
(gdb) r
(gdb) bt
#0  func1 (a=10) at frame.c:5
#1  0x0000000000400560 in func2 (a=10) at frame.c:12
#2  0x0000000000400582 in func3 (a=10) at frame.c:18
#3  0x0000000000400596 in main () at frame.c:23
(gdb) f 1
(gdb) p b
(gdb) f 2
(gdb) p b
```

也可以不进行切换，直接打印：

```
(gdb) p func2::b
$1 = 2
(gdb) p func3::b
$2 = 3
```

同样，对于C++的函数名，需要使用单引号括起来，比如：

```
(gdb) p '(anonymous namespace)::SSAA::handleStore'::n->pi->inst->dump()
```

### 多进程/线程

#### [调试已经运行的进程](https://github.com/hellogcc/100-gdb-tips/blob/master/src/attach-process.md)

调试已经运行的进程有两种方法：一种是gdb启动时，指定进程的ID：gdb program processID（也可以用-p或者--pid指定进程ID，例如：gdb program -p=10210）。以上面代码为例，用“ps”命令已经获得进程ID为10210：

```
bash-3.2# gdb -q a 10210
Reading symbols from /data/nan/a...done.
Attaching to program `/data/nan/a', process 10210
[New process 10210]
Retry #1:
Retry #2:
Retry #3:
Retry #4:
Reading symbols from /usr/lib/libc.so.1...(no debugging symbols found)...done.
[Thread debugging using libthread_db enabled]
[New LWP    3        ]
[New LWP    2        ]
[New Thread 1 (LWP 1)]
[New Thread 2 (LWP 2)]
[New Thread 3 (LWP 3)]
Loaded symbols for /usr/lib/libc.so.1
Reading symbols from /lib/ld.so.1...(no debugging symbols found)...done.
Loaded symbols for /lib/ld.so.1
[Switching to Thread 1 (LWP 1)]
0xfeeeae55 in ___nanosleep () from /usr/lib/libc.so.1
(gdb) bt
#0  0xfeeeae55 in ___nanosleep () from /usr/lib/libc.so.1
#1  0xfeedcae4 in sleep () from /usr/lib/libc.so.1
#2  0x080509ef in main () at a.c:17
```

如果嫌每次ps查看进程号比较麻烦，请尝试如下脚本

```
# 保存为xgdb.sh（添加可执行权限）
# 用法 xgdb.sh a 
prog_bin=$1
running_name=$(basename $prog_bin)
pid=$(/sbin/pidof $running_name)
gdb attach $pid
```

另一种是先启动gdb，然后用“attach”命令“附着”在进程上：

```
bash-3.2# gdb -q a
Reading symbols from /data/nan/a...done.
(gdb) attach 10210
Attaching to program `/data/nan/a', process 10210
[New process 10210]
Retry #1:
Retry #2:
Retry #3:
Retry #4:
Reading symbols from /usr/lib/libc.so.1...(no debugging symbols found)...done.
[Thread debugging using libthread_db enabled]
[New LWP    3        ]
[New LWP    2        ]
[New Thread 1 (LWP 1)]
[New Thread 2 (LWP 2)]
[New Thread 3 (LWP 3)]
Loaded symbols for /usr/lib/libc.so.1
Reading symbols from /lib/ld.so.1...(no debugging symbols found)...done.
Loaded symbols for /lib/ld.so.1
[Switching to Thread 1 (LWP 1)]
0xfeeeae55 in ___nanosleep () from /usr/lib/libc.so.1
(gdb) bt
#0  0xfeeeae55 in ___nanosleep () from /usr/lib/libc.so.1
#1  0xfeedcae4 in sleep () from /usr/lib/libc.so.1
#2  0x080509ef in main () at a.c:17
```

如果不想继续调试了，可以用“detach”命令“脱离”进程：

```
(gdb) detach
Detaching from program: /data/nan/a, process 10210
(gdb) bt
No stack.
```

#### [调试子进程](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-follow-fork-mode-child.md)

在调试多进程程序时，gdb默认会追踪父进程。例如：

```
(gdb) start
Temporary breakpoint 1 at 0x40055c: file a.c, line 8.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:8
8               pid = fork();
(gdb) n
9               if (pid < 0)
(gdb) hello world

13              else if (pid > 0)
(gdb)
15                      exit(0);
(gdb)
[Inferior 1 (process 12786) exited normally]
```

可以看到程序执行到第15行：父进程退出。

如果要调试子进程，要使用如下命令：“set follow-fork-mode child”，例如：

```
(gdb) set follow-fork-mode child
(gdb) start
Temporary breakpoint 1 at 0x40055c: file a.c, line 8.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:8
8               pid = fork();
(gdb) n
[New process 12241]
[Switching to process 12241]
9               if (pid < 0)
(gdb)
13              else if (pid > 0)
(gdb)
17              printf("hello world\n");
(gdb)
hello world
18              return 0;
```

可以看到程序执行到第17行：子进程打印“hello world”。

#### [同时调试父进程和子进程](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-detach-on-fork.md)

在调试多进程程序时，gdb默认只会追踪父进程的运行，而子进程会独立运行，gdb不会控制。以上面程序为例：

```
(gdb) start
Temporary breakpoint 1 at 0x40055c: file a.c, line 7.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:7
7           pid = fork();
(gdb) n
8           if (pid < 0)
(gdb) Child

12          else if (pid > 0)
(gdb)
14              printf("Parent\n");
(gdb)
Parent
15              exit(0);
```

可以看到当单步执行到第8行时，程序打印出“Child” ，证明子进程已经开始独立运行。

如果要同时调试父进程和子进程，可以使用“`set detach-on-fork off`”（默认`detach-on-fork`是`on`）命令，这样gdb就能同时调试父子进程，并且在调试一个进程时，另外一个进程处于挂起状态。仍以上面程序为例：

```
(gdb) set detach-on-fork off
(gdb) start
Temporary breakpoint 1 at 0x40055c: file a.c, line 7.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:7
7           pid = fork();
(gdb) n
[New process 1050]
8           if (pid < 0)
(gdb)
12          else if (pid > 0)
(gdb) i inferior
  Num  Description       Executable
  2    process 1050      /data2/home/nanxiao/a
* 1    process 1046      /data2/home/nanxiao/a
(gdb) n
14              printf("Parent\n");
(gdb) n
Parent
15              exit(0);
(gdb)
[Inferior 1 (process 1046) exited normally]
(gdb)
The program is not being run.
(gdb) i inferiors
  Num  Description       Executable
  2    process 1050      /data2/home/nanxiao/a
* 1    <null>            /data2/home/nanxiao/a
(gdb) inferior 2
[Switching to inferior 2 [process 1050] (/data2/home/nanxiao/a)]
[Switching to thread 2 (process 1050)]
#0  0x00007ffff7af6cad in fork () from /lib64/libc.so.6
(gdb) bt
#0  0x00007ffff7af6cad in fork () from /lib64/libc.so.6
#1  0x0000000000400561 in main () at a.c:7
(gdb) n
Single stepping until exit from function fork,
which has no line number information.
main () at a.c:8
8           if (pid < 0)
(gdb)
12          else if (pid > 0)
(gdb)
17          printf("Child\n");
(gdb)
Child
18          return 0;
(gdb)
```

在使用“`set detach-on-fork off`”命令后，用“`i inferiors`”（`i`是`info`命令缩写）查看进程状态，可以看到父子进程都在被gdb调试的状态，前面显示“*”是正在调试的进程。当父进程退出后，用“`inferior infno`”切换到子进程去调试。

这个命令目前Linux支持，其它很多操作系统都不支持，使用时请注意。参见[gdb手册](https://sourceware.org/gdb/onlinedocs/gdb/Forks.html)

此外，如果想让父子进程都同时运行，可以使用“`set schedule-multiple on`”（默认`schedule-multiple`是`off`）命令，仍以上述代码为例：

```
(gdb) set detach-on-fork off
(gdb) set schedule-multiple on
(gdb) start
Temporary breakpoint 1 at 0x40059c: file a.c, line 7.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:7
7           pid = fork();
(gdb) n
[New process 26597]
Child
```

可以看到打印出了“Child”，证明子进程也在运行了。

#### [查看线程信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-threads.md)

用gdb调试多线程程序，可以用“i threads”命令（i是info命令缩写）查看所有线程的信息，以上面程序为例（运行平台为Linux，CPU为X86_64）:

```
  (gdb) i threads
  Id   Target Id         Frame
  3    Thread 0x7ffff6e2b700 (LWP 31773) 0x00007ffff7915911 in clone () from /lib64/libc.so.6
  2    Thread 0x7ffff782c700 (LWP 31744) 0x00007ffff78d9bcd in nanosleep () from /lib64/libc.so.6
* 1    Thread 0x7ffff7fe9700 (LWP 31738) main () at a.c:18
```

第一项（Id）：是gdb标示每个线程的唯一ID：1，2等等。
第二项（Target Id）：是具体系统平台用来标示每个线程的ID，不同平台信息可能会不同。 像当前Linux平台显示的就是： Thread 0x7ffff6e2b700 (LWP 31773)。
第三项（Frame）：显示的是线程执行到哪个函数。
前面带“*”表示的是“current thread”，可以理解为gdb调试多线程程序时，选择的一个“默认线程”。

再以Solaris平台（CPU为X86_64）为例，可以看到显示信息会略有不同：

```
(gdb) i threads
[New Thread 2 (LWP 2)]
[New Thread 3 (LWP 3)]
  Id   Target Id         Frame
  6    Thread 3 (LWP 3)  0xfeec870d in _thr_setup () from /usr/lib/libc.so.1
  5    Thread 2 (LWP 2)  0xfefc9661 in elf_find_sym () from /usr/lib/ld.so.1
  4    LWP    3          0xfeec870d in _thr_setup () from /usr/lib/libc.so.1
  3    LWP    2          0xfefc9661 in elf_find_sym () from /usr/lib/ld.so.1
* 2    Thread 1 (LWP 1)  main () at a.c:18
  1    LWP    1          main () at a.c:18
```

也可以用“i threads [Id...]”指定打印某些线程的信息，例如：

```
  (gdb) i threads 1 2
  Id   Target Id         Frame
  2    Thread 0x7ffff782c700 (LWP 12248) 0x00007ffff78d9bcd in nanosleep () from /lib64/libc.so.6
* 1    Thread 0x7ffff7fe9700 (LWP 12244) main () at a.c:18
```

使用"thread thread-id"实现不同线程之间的切换，查看指定线程的堆栈信息

```
(gdb) thread 2
[Switching to thread 2 (Thread 0x7ffff782c700 (LWP 12248))]...
```

使用"thread apply [thread-id-list] [all] args"可以在多个线程上执行命令，例如：`thread apply all bt`可以查看所有线程的堆栈信息。

```
(gdb) thread apply all bt
```

#### [打印所有线程的堆栈信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-all-threads-bt.md)

gdb可以使用“`thread apply all bt`”命令打印所有线程的堆栈信息。以上面程序为例:

```
(gdb) thread apply all bt

Thread 3 (Thread -1210868832 (LWP 26975)):
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
#1  0xb7dcc77f in sleep () from /lib/libc.so.6
#2  0x08048575 in thread1_func ()
#3  0xb7e871eb in start_thread () from /lib/libpthread.so.0
#4  0xb7e007fe in clone () from /lib/libc.so.6

Thread 2 (Thread -1219257440 (LWP 26976)):
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
#1  0xb7dcc77f in sleep () from /lib/libc.so.6
#2  0x08048575 in thread1_func ()
#3  0xb7e871eb in start_thread () from /lib/libpthread.so.0
#4  0xb7e007fe in clone () from /lib/libc.so.6

Thread 1 (Thread -1210866000 (LWP 26974)):
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
#1  0xb7dcc77f in sleep () from /lib/libc.so.6
#2  0x08048547 in main ()
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
```

可以看到，使用“`thread apply all bt`”命令以后，会对所有的线程实施backtrace命令。`thread apply [thread-id-list] [all] args` 也可以对指定的线程ID列表进行执行：

```
(gdb) thread apply 1-2 bt

Thread 1 (Thread -1210866000 (LWP 26974)):
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
#1  0xb7dcc77f in sleep () from /lib/libc.so.6
#2  0x08048547 in main ()

Thread 2 (Thread -1219257440 (LWP 26976)):
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
#1  0xb7dcc77f in sleep () from /lib/libc.so.6
#2  0x08048575 in thread1_func ()
#3  0xb7e871eb in start_thread () from /lib/libpthread.so.0
#4  0xb7e007fe in clone () from /lib/libc.so.6
#0  0xb7dcc96c in __gxx_personality_v0 () from /lib/libc.so.6
```

#### [在Solaris上使用maintenance命令查看线程信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/maint-info-sol-threads.md)

#### [不显示线程启动和退出信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/show-print-thread-events.md)

#### [只允许一个线程运行](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-scheduler-locking-on.md)

用gdb调试多线程程序时，一旦程序断住，所有的线程都处于暂停状态。此时当你调试其中一个线程时（比如执行“`step`”，“`next`”命令），所有的线程都会同时执行。以上面程序为例:

```
(gdb) b a.c:9
Breakpoint 1 at 0x400580: file a.c, line 9.
(gdb) r
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
[New Thread 0x7ffff782c700 (LWP 17368)]
[Switching to Thread 0x7ffff782c700 (LWP 17368)]

Breakpoint 1, thread1_func (p_arg=0x400718) at a.c:9
9                       a++;
(gdb) p b
$1 = 0
(gdb) s
10                      sleep(1);
(gdb) s
[New Thread 0x7ffff6e2b700 (LWP 17369)]
11              }
(gdb)

Breakpoint 1, thread1_func (p_arg=0x400718) at a.c:9
9                       a++;
(gdb)
10                      sleep(1);
(gdb) p b
$2 = 3
```

`thread1_func`更新全局变量`a`的值，`thread2_func`更新全局变量`b`的值。我在`thread1_func`里`a++`语句打上断点，当断点第一次命中时，打印`b`的值是`0`，在单步调试`thread1_func`几次后，`b`的值变成`3`，证明在单步调试`thread1_func`时，`thread2_func`也在执行。
如果想在调试一个线程时，让其它线程暂停执行，可以使用“`set scheduler-locking on`”命令：

```
(gdb) b a.c:9
Breakpoint 1 at 0x400580: file a.c, line 9.
(gdb) r
Starting program: /data2/home/nanxiao/a
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib64/libthread_db.so.1".
[New Thread 0x7ffff782c700 (LWP 19783)]
[Switching to Thread 0x7ffff782c700 (LWP 19783)]

Breakpoint 1, thread1_func (p_arg=0x400718) at a.c:9
9                       a++;
(gdb) set scheduler-locking on
(gdb) p b
$1 = 0
(gdb) s
10                      sleep(1);
(gdb)
11              }
(gdb)

Breakpoint 1, thread1_func (p_arg=0x400718) at a.c:9
9                       a++;
(gdb)
10                      sleep(1);
(gdb)
11              }
(gdb) p b
$2 = 0
```

可以看到在单步调试`thread1_func`几次后，`b`的值仍然为`0`，证明在在单步调试`thread1_func`时，`thread2_func`没有执行。

此外，“`set scheduler-locking`”命令除了支持`off`和`on`模式外（默认是`off`），还有一个`step`模式。含义是：当用"`step`"命令调试线程时，其它线程不会执行，但是用其它命令（比如"`next`"）调试线程时，其它线程也许会执行。

#### [使用“$_thread”变量](https://github.com/hellogcc/100-gdb-tips/blob/master/src/use-$_thread-variable.md)

#### [一个gdb会话中同时调试多个程序](https://github.com/hellogcc/100-gdb-tips/blob/master/src/add-copy-inferiors.md)

gdb支持在一个会话中同时调试多个程序。以上面程序为例，首先调试`a`程序：

```
root@bash:~$ gdb a
GNU gdb (Ubuntu 7.7-0ubuntu3) 7.7
Copyright (C) 2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from a...done.
(gdb) start
Temporary breakpoint 1 at 0x400568: file a.c, line 10.
Starting program: /home/nanxiao/a
```

接着使用“`add-inferior [ -copies n ] [ -exec executable ]`”命令加载可执行文件`b`。其中`n`默认为1：

```
(gdb) add-inferior -copies 2 -exec b
Added inferior 2
Reading symbols from b...done.
Added inferior 3
Reading symbols from b...done.
(gdb) i inferiors
  Num  Description       Executable
  3    <null>            /home/nanxiao/b
  2    <null>            /home/nanxiao/b
* 1    process 1586      /home/nanxiao/a
(gdb) inferior 2
[Switching to inferior 2 [<null>] (/home/nanxiao/b)]
(gdb) start
Temporary breakpoint 2 at 0x400568: main. (3 locations)
Starting program: /home/nanxiao/b

Temporary breakpoint 2, main () at b.c:24
24              printf("%d\n", func3(10));
(gdb) i inferiors
  Num  Description       Executable
  3    <null>            /home/nanxiao/b
* 2    process 1590      /home/nanxiao/b
  1    process 1586      /home/nanxiao/a
```

可以看到可以调试`b`程序了。

另外也可用“`clone-inferior [ -copies n ] [ infno ]`”克隆现有的`inferior`，其中`n`默认为1，`infno`默认为当前的`inferior`：

```
(gdb) i inferiors
  Num  Description       Executable
  3    <null>            /home/nanxiao/b
* 2    process 1590      /home/nanxiao/b
  1    process 1586      /home/nanxiao/a
(gdb) clone-inferior -copies 1
Added inferior 4.
(gdb) i inferiors
  Num  Description       Executable
  4    <null>            /home/nanxiao/b
  3    <null>            /home/nanxiao/b
* 2    process 1590      /home/nanxiao/b
  1    process 1586      /home/nanxiao/a
```

可以看到又多了一个`b`程序。

#### [打印程序进程空间信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/maint-info-program-space.md)

使用gdb调试多个进程时，可以使用“`maint info program-spaces`”打印当前所有被调试的进程信息。以上面程序为例：

```
[root@localhost nan]# gdb a
GNU gdb (GDB) 7.8.1
......
Reading symbols from a...done.
(gdb) start
Temporary breakpoint 1 at 0x4004f9: file a.c, line 10.
Starting program: /home/nan/a 

Temporary breakpoint 1, main () at a.c:10
10              func(1, 2);
(gdb) add-inferior -exec b
Added inferior 2
Reading symbols from b...done.
(gdb) i inferiors b
Args must be numbers or '$' variables.
(gdb) i inferiors
  Num  Description       Executable        
  2    <null>            /home/nan/b       
* 1    process 15753     /home/nan/a       
(gdb) inferior 2
[Switching to inferior 2 [<null>] (/home/nan/b)]
(gdb) start
Temporary breakpoint 2 at 0x4004f9: main. (2 locations)
Starting program: /home/nan/b 

Temporary breakpoint 2, main () at b.c:24
24              printf("%d\n", func3(10));
(gdb) i inferiors
  Num  Description       Executable        
* 2    process 15902     /home/nan/b       
  1    process 15753     /home/nan/a       
(gdb) clone-inferior -copies 2
Added inferior 3.
Added inferior 4.
(gdb) i inferiors
  Num  Description       Executable        
  4    <null>            /home/nan/b       
  3    <null>            /home/nan/b       
* 2    process 15902     /home/nan/b       
  1    process 15753     /home/nan/a       
(gdb) maint info program-spaces
  Id   Executable        
  4    /home/nan/b       
        Bound inferiors: ID 4 (process 0)
  3    /home/nan/b       
        Bound inferiors: ID 3 (process 0)
* 2    /home/nan/b       
        Bound inferiors: ID 2 (process 15902)
  1    /home/nan/a       
        Bound inferiors: ID 1 (process 15753)
```

可以看到执行“`maint info program-spaces`”命令后，打印出当前有4个`program-spaces`（编号从1到4）。另外还有每个`program-spaces`对应的程序，`inferior`编号及进程号。

#### [使用“$_exitcode”变量](https://github.com/hellogcc/100-gdb-tips/blob/master/src/use-$_exitcode.md)

### core dump 文件

#### [为调试进程产生core dump文件](https://github.com/hellogcc/100-gdb-tips/blob/master/src/generate-core-dump-file.md)

在用gdb调试程序时，我们有时想让被调试的进程产生core dump文件，记录现在进程的状态，以供以后分析。可以用“generate-core-file”命令来产生core dump文件：

```
(gdb) help generate-core-file
Save a core file with the current state of the debugged process.
Argument is optional filename.  Default filename is 'core.<process_id>'.

(gdb) start
Temporary breakpoint 1 at 0x8050c12: file a.c, line 9.
Starting program: /data1/nan/a
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Temporary breakpoint 1, main () at a.c:9
9           change_var();
(gdb) generate-core-file
Saved corefile core.12955
```

也可使用“gcore”命令：

```
(gdb) help gcore
Save a core file with the current state of the debugged process.
Argument is optional filename.  Default filename is 'core.<process_id>'.
(gdb) gcore
Saved corefile core.13256
```

#### [加载可执行程序和core dump文件](https://github.com/hellogcc/100-gdb-tips/blob/master/src/load-executable-and-coredump-file.md)

例子程序访问了一个空指针，所以程序会crash并产生core dump文件。用gdb调试core dump文件，通常用这个命令形式：“gdb path/to/the/executable path/to/the/coredump”，然后gdb会显示程序crash的位置：

```
bash-3.2# gdb -q /data/nan/a /var/core/core.a.22268.1402638140
Reading symbols from /data/nan/a...done.
[New LWP 1]
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
Core was generated by `./a'.
Program terminated with signal 11, Segmentation fault.
#0  0x0000000000400cdb in main () at a.c:6
6               *p = 0;
```

有时我们想在gdb启动后，动态加载可执行程序和core dump文件，这时可以用“file”和“core”（core-file命令缩写）命令。“file”命令用来读取可执行文件的符号表信息，而“core”命令则是指定core dump文件的位置：

```
bash-3.2# gdb -q
(gdb) file /data/nan/a
Reading symbols from /data/nan/a...done.
(gdb) core /var/core/core.a.22268.1402638140
[New LWP 1]
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
Core was generated by `./a'.
Program terminated with signal 11, Segmentation fault.
#0  0x0000000000400cdb in main () at a.c:6
6               *p = 0;
```

可以看到gdb同样显示程序crash的位置。

### 汇编

* [设置汇编指令格式](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-disassembly-flavor.md)

#### [在函数的第一条汇编指令打断点](https://github.com/hellogcc/100-gdb-tips/blob/master/src/break-on-first-assembly-code.md)

通常给函数打断点的命令：“b func”（b是break命令的缩写），不会把断点设置在汇编指令层次函数的开头，例如：

```
(gdb) b main
Breakpoint 1 at 0x8050c12: file a.c, line 9.
(gdb) r
Starting program: /data1/nan/a
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Breakpoint 1, main () at a.c:9
9           change_var();
(gdb) disassemble
Dump of assembler code for function main:
   0x08050c0f <+0>:     push   %ebp
   0x08050c10 <+1>:     mov    %esp,%ebp
=> 0x08050c12 <+3>:     call   0x8050c00 <change_var>
   0x08050c17 <+8>:     mov    $0x0,%eax
   0x08050c1c <+13>:    pop    %ebp
   0x08050c1d <+14>:    ret
End of assembler dump.
```

可以看到程序停在了第三条汇编指令（箭头所指位置）。如果要把断点设置在汇编指令层次函数的开头，要使用如下命令：“b *func”，例如：

```
(gdb) b *main
Breakpoint 1 at 0x8050c0f: file a.c, line 8.
(gdb) r
Starting program: /data1/nan/a
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Breakpoint 1, main () at a.c:8
8       int main(void){
(gdb) disassemble
Dump of assembler code for function main:
=> 0x08050c0f <+0>:     push   %ebp
   0x08050c10 <+1>:     mov    %esp,%ebp
   0x08050c12 <+3>:     call   0x8050c00 <change_var>
   0x08050c17 <+8>:     mov    $0x0,%eax
   0x08050c1c <+13>:    pop    %ebp
   0x08050c1d <+14>:    ret
End of assembler dump.
```

可以看到程序停在了第一条汇编指令（箭头所指位置）

#### [自动反汇编后面要执行的代码](https://github.com/hellogcc/100-gdb-tips/blob/master/src/disassemble-next-line.md)

如果要在任意情况下反汇编后面要执行的代码：

```
(gdb) set disassemble-next-line on
```

如果要在后面的代码没有源码的情况下才反汇编后面要执行的代码：

```
(gdb) set disassemble-next-line auto
```

关闭这个功能：

```
(gdb) set disassemble-next-line off
```

#### [将源程序和汇编指令映射起来](https://github.com/hellogcc/100-gdb-tips/blob/master/src/map-source-code-and-assembly.md)

可以用“disas /m fun”（disas是disassemble命令缩写）命令将函数代码和汇编指令映射起来，以上面代码为例：

```
(gdb) disas /m main
Dump of assembler code for function main:
11      int main(void) {
   0x00000000004004c4 <+0>:     push   %rbp
   0x00000000004004c5 <+1>:     mov    %rsp,%rbp
   0x00000000004004c8 <+4>:     push   %rbx
   0x00000000004004c9 <+5>:     sub    $0x18,%rsp

12              ex_st st = {1, 2, 3, 4};
   0x00000000004004cd <+9>:     movl   $0x1,-0x20(%rbp)
   0x00000000004004d4 <+16>:    movl   $0x2,-0x1c(%rbp)
   0x00000000004004db <+23>:    movl   $0x3,-0x18(%rbp)
   0x00000000004004e2 <+30>:    movl   $0x4,-0x14(%rbp)

13              printf("%d,%d,%d,%d\n", st.a, st.b, st.c, st.d);
   0x00000000004004e9 <+37>:    mov    -0x14(%rbp),%esi
   0x00000000004004ec <+40>:    mov    -0x18(%rbp),%ecx
   0x00000000004004ef <+43>:    mov    -0x1c(%rbp),%edx
   0x00000000004004f2 <+46>:    mov    -0x20(%rbp),%ebx
   0x00000000004004f5 <+49>:    mov    $0x400618,%eax
   0x00000000004004fa <+54>:    mov    %esi,%r8d
   0x00000000004004fd <+57>:    mov    %ebx,%esi
   0x00000000004004ff <+59>:    mov    %rax,%rdi
   0x0000000000400502 <+62>:    mov    $0x0,%eax
   0x0000000000400507 <+67>:    callq  0x4003b8 <printf@plt>

14              return 0;
   0x000000000040050c <+72>:    mov    $0x0,%eax

15      }
   0x0000000000400511 <+77>:    add    $0x18,%rsp
   0x0000000000400515 <+81>:    pop    %rbx
   0x0000000000400516 <+82>:    leaveq
   0x0000000000400517 <+83>:    retq

End of assembler dump.
```

可以看到每一条C语句下面是对应的汇编代码。

如果只想查看某一行所对应的地址范围，可以：

```
(gdb) i line 13
Line 13 of "foo.c" starts at address 0x4004e9 <main+37> and ends at 0x40050c <main+72>. 
```

如果只想查看这一条语句对应的汇编代码，可以使用“`disassemble [Start],[End]`”命令：

```
(gdb) disassemble 0x4004e9, 0x40050c
Dump of assembler code from 0x4004e9 to 0x40050c:
   0x00000000004004e9 <main+37>:        mov    -0x14(%rbp),%esi
   0x00000000004004ec <main+40>:        mov    -0x18(%rbp),%ecx
   0x00000000004004ef <main+43>:        mov    -0x1c(%rbp),%edx
   0x00000000004004f2 <main+46>:        mov    -0x20(%rbp),%ebx
   0x00000000004004f5 <main+49>:        mov    $0x400618,%eax
   0x00000000004004fa <main+54>:        mov    %esi,%r8d
   0x00000000004004fd <main+57>:        mov    %ebx,%esi
   0x00000000004004ff <main+59>:        mov    %rax,%rdi
   0x0000000000400502 <main+62>:        mov    $0x0,%eax
   0x0000000000400507 <main+67>:        callq  0x4003b8 <printf@plt>
End of assembler dump.
```

#### [显示将要执行的汇编指令](https://github.com/hellogcc/100-gdb-tips/blob/master/src/display-instruction-pc.md)

使用gdb调试汇编程序时，可以用“`display /i $pc`”命令显示当程序停止时，将要执行的汇编指令。以上面程序为例：

```
(gdb) start
Temporary breakpoint 1 at 0x400488: file a.c, line 9.
Starting program: /data2/home/nanxiao/a

Temporary breakpoint 1, main () at a.c:9
9           change_var();
(gdb) display /i $pc
1: x/i $pc
=> 0x400488 <main+4>:   mov    $0x0,%eax
(gdb) si
0x000000000040048d      9           change_var();
1: x/i $pc
=> 0x40048d <main+9>:   callq  0x400474 <change_var>
(gdb)
change_var () at a.c:4
4       void change_var(){
1: x/i $pc
=> 0x400474 <change_var>:       push   %rbp
```

可以看到打印出了将要执行的汇编指令。此外也可以一次显示多条指令：

```
(gdb) display /3i $pc
2: x/3i $pc
=> 0x400474 <change_var>:       push   %rbp
   0x400475 <change_var+1>:     mov    %rsp,%rbp
   0x400478 <change_var+4>:     movl   $0x64,0x2003de(%rip)        # 0x600860 <global_var>
```

可以看到一次显示了`3`条指令。

取消显示可以用`undisplay`命令。

#### [打印寄存器的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-registers.md)

用gdb调试程序时，如果想查看寄存器的值，可以使用“i registers”命令（i是info命令缩写），例如:

```
(gdb) i registers
rax            0x7ffff7dd9f60   140737351884640
rbx            0x0      0
rcx            0x0      0
rdx            0x7fffffffe608   140737488348680
rsi            0x7fffffffe5f8   140737488348664
rdi            0x1      1
rbp            0x7fffffffe510   0x7fffffffe510
rsp            0x7fffffffe4c0   0x7fffffffe4c0
r8             0x7ffff7dd8300   140737351877376
r9             0x7ffff7deb9e0   140737351956960
r10            0x7fffffffe360   140737488348000
r11            0x7ffff7a68be0   140737348275168
r12            0x4003e0 4195296
r13            0x7fffffffe5f0   140737488348656
r14            0x0      0
r15            0x0      0
rip            0x4004cd 0x4004cd <main+9>
eflags         0x206    [ PF IF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
gs             0x0      0
```

以上输出不包括浮点寄存器和向量寄存器的内容。使用“i all-registers”命令，可以输出所有寄存器的内容：

```
(gdb) i all-registers
 rax            0x7ffff7dd9f60   140737351884640
 rbx            0x0      0
 rcx            0x0      0
 rdx            0x7fffffffe608   140737488348680
 rsi            0x7fffffffe5f8   140737488348664
 rdi            0x1      1
 rbp            0x7fffffffe510   0x7fffffffe510
 rsp            0x7fffffffe4c0   0x7fffffffe4c0
 r8             0x7ffff7dd8300   140737351877376
 r9             0x7ffff7deb9e0   140737351956960
 r10            0x7fffffffe360   140737488348000
 r11            0x7ffff7a68be0   140737348275168
 r12            0x4003e0 4195296
 r13            0x7fffffffe5f0   140737488348656
 r14            0x0      0
 r15            0x0      0
 rip            0x4004cd 0x4004cd <main+9>
 eflags         0x206    [ PF IF ]
 cs             0x33     51
 ss             0x2b     43
 ds             0x0      0
 es             0x0      0
 fs             0x0      0
 gs             0x0      0
 st0            0        (raw 0x00000000000000000000)
 st1            0        (raw 0x00000000000000000000)
 st2            0        (raw 0x00000000000000000000)
 st3            0        (raw 0x00000000000000000000)
 st4            0        (raw 0x00000000000000000000)
 st5            0        (raw 0x00000000000000000000)
 st6            0        (raw 0x00000000000000000000)
 st7            0        (raw 0x00000000000000000000)
 ......
```

要打印单个寄存器的值，可以使用“i registers regname”或者“p $regname”，例如：

```
(gdb) i registers eax
eax            0xf7dd9f60       -136470688
(gdb) p $eax
$1 = -136470688
```

#### [显示程序原始机器码](https://github.com/hellogcc/100-gdb-tips/blob/master/src/disassemble-raw-machine-code.md)

使用“disassemble /r”命令可以用16进制形式显示程序的原始机器码。以上面程序为例：

```
(gdb) disassemble /r main
Dump of assembler code for function main:
   0x0000000000400530 <+0>:     55      push   %rbp
   0x0000000000400531 <+1>:     48 89 e5        mov    %rsp,%rbp
   0x0000000000400534 <+4>:     bf e0 05 40 00  mov    $0x4005e0,%edi
   0x0000000000400539 <+9>:     e8 d2 fe ff ff  callq  0x400410 <puts@plt>
   0x000000000040053e <+14>:    b8 00 00 00 00  mov    $0x0,%eax
   0x0000000000400543 <+19>:    5d      pop    %rbp
   0x0000000000400544 <+20>:    c3      retq
End of assembler dump.
(gdb) disassemble /r 0x0000000000400534,+4
Dump of assembler code from 0x400534 to 0x400538:
   0x0000000000400534 <main+4>: bf e0 05 40 00  mov    $0x4005e0,%edi
End of assembler dump.
```

### 改变程序的执行

#### [改变字符串的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/change-string.md)

使用gdb调试程序时，可以用“`set`”命令改变字符串的值，以上面程序为例：

```
(gdb) start
Temporary breakpoint 1 at 0x8050af0: file a.c, line 5.
Starting program: /data1/nan/a 
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Temporary breakpoint 1, main () at a.c:5
5               char p1[] = "Sam";
(gdb) n
6               char *p2 = "Bob";
(gdb) 
8               printf("p1 is %s, p2 is %s\n", p1, p2);
(gdb) set main::p1="Jil"
(gdb) set main::p2="Bill"
(gdb) n
p1 is Jil, p2 is Bill
9               return 0;
```

可以看到执行`p1`和`p2`的字符串都发生了变化。也可以通过访问内存地址的方法改变字符串的值：

```
Starting program: /data1/nan/a 
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Temporary breakpoint 2, main () at a.c:5
5               char p1[] = "Sam";
(gdb) n
6               char *p2 = "Bob";
(gdb) p p1
$1 = "Sam"
(gdb) p &p1
$2 = (char (*)[4]) 0x80477a4
(gdb) set {char [4]} 0x80477a4 = "Ace"
(gdb) n
8               printf("p1 is %s, p2 is %s\n", p1, p2);
(gdb) 
p1 is Ace, p2 is Bob
9               return 0;
```

在改变字符串的值时候，一定要注意内存越界的问题。

#### [设置变量的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-var.md)

在gdb中，可以用“`set var variable=expr`”命令设置变量的值，以上面代码为例：

```
Breakpoint 2, func () at a.c:5
5                   int i = 2;
(gdb) n
7                   return i;
(gdb) set var i = 8
(gdb) p i
$4 = 8
```

可以看到在`func`函数里用`set`命令把`i`的值修改成为`8`。

也可以用“`set {type}address=expr`”的方式，含义是给存储地址在`address`，变量类型为`type`的变量赋值，仍以上面代码为例：

```
Breakpoint 2, func () at a.c:5
5                   int i = 2;
(gdb) n
7                   return i;
(gdb) p &i
$5 = (int *) 0x8047a54
(gdb) set {int}0x8047a54 = 8
(gdb) p i
$6 = 8
```

可以看到`i`的值被修改成为`8`。

另外寄存器也可以作为变量，因此同样可以修改寄存器的值：

```
Breakpoint 2, func () at a.c:5
5                   int i = 2;
(gdb)
(gdb) n
7                   return i;
(gdb)
8               }
(gdb) set var $eax = 8
(gdb) n
main () at a.c:15
15                  printf("%d\n", a);
(gdb)
8
16                  return 0;
```

可以看到因为eax寄存器存储着函数的返回值，所以当把eax寄存器的值改为`8`后，函数的返回值也变成了`8`。

#### [修改PC寄存器的值](https://github.com/hellogcc/100-gdb-tips/blob/master/src/modify-pc-register.md)

PC寄存器会存储程序下一条要执行的指令，通过修改这个寄存器的值，可以达到改变程序执行流程的目的。
上面的程序会输出“`a=2`”，下面介绍一下如何通过修改PC寄存器的值，改变程序执行流程。

```
4               int a =0;
(gdb) disassemble main
Dump of assembler code for function main:
0x08050921 <main+0>:    push   %ebp
0x08050922 <main+1>:    mov    %esp,%ebp
0x08050924 <main+3>:    sub    $0x8,%esp
0x08050927 <main+6>:    and    $0xfffffff0,%esp
0x0805092a <main+9>:    mov    $0x0,%eax
0x0805092f <main+14>:   add    $0xf,%eax
0x08050932 <main+17>:   add    $0xf,%eax
0x08050935 <main+20>:   shr    $0x4,%eax
0x08050938 <main+23>:   shl    $0x4,%eax
0x0805093b <main+26>:   sub    %eax,%esp
0x0805093d <main+28>:   movl   $0x0,-0x4(%ebp)
0x08050944 <main+35>:   lea    -0x4(%ebp),%eax
0x08050947 <main+38>:   incl   (%eax)
0x08050949 <main+40>:   lea    -0x4(%ebp),%eax
0x0805094c <main+43>:   incl   (%eax)
0x0805094e <main+45>:   sub    $0x8,%esp
0x08050951 <main+48>:   pushl  -0x4(%ebp)
0x08050954 <main+51>:   push   $0x80509b4
0x08050959 <main+56>:   call   0x80507cc <printf@plt>
0x0805095e <main+61>:   add    $0x10,%esp
0x08050961 <main+64>:   mov    $0x0,%eax
0x08050966 <main+69>:   leave
0x08050967 <main+70>:   ret
End of assembler dump.
(gdb) info line 6
Line 6 of "a.c" starts at address 0x8050944 <main+35> and ends at 0x8050949 <main+40>.
(gdb) info line 7
Line 7 of "a.c" starts at address 0x8050949 <main+40> and ends at 0x805094e <main+45>.
```

通过“`info line 6`”和“`info line 7`”命令可以知道两条“`a++;`”语句的汇编指令起始地址分别是`0x8050944`和`0x8050949`。

```
(gdb) n
6               a++;
(gdb) p $pc
$3 = (void (*)()) 0x8050944 <main+35>
(gdb) set var $pc=0x08050949
```

当程序要执行第一条“`a++;`”语句时，打印`pc`寄存器的值，看到`pc`寄存器的值为`0x8050944`，与“`info line 6`”命令得到的一致。接下来，把`pc`寄存器的值改为`0x8050949`，也就是通过“`info line 7`”命令得到的第二条“`a++;`”语句的起始地址。

```
(gdb) n
8               printf("a=%d\n", a);
(gdb)
a=1
9               return 0;
```

接下来执行，可以看到程序输出“`a=1`”，也就是跳过了第一条“`a++;`”语句

#### [跳转到指定位置执行](https://github.com/hellogcc/100-gdb-tips/blob/master/src/jump.md)

当调试程序时，你可能不小心走过了出错的地方：

```
(gdb) n
13   fun (i--);
(gdb) 
14   fun (i--);
(gdb) 
15   fun (i--);
(gdb) 
error
17   return 0;
```

看起来是在15行，调用fun的时候出错了。常见的办法是在15行设置个断点，然后从头`run`一次。

如果你的环境支持反向执行，那么更好了。

如果不支持，你也可以直接`jump`到15行，再执行一次：

```
(gdb) b 15
Breakpoint 2 at 0x40056a: file jump.c, line 15.
(gdb) j 15
Continuing at 0x40056a.

Breakpoint 2, main () at jump.c:15
15   fun (i--);
(gdb) s
fun (x=-2) at jump.c:5
5   if (x < 0)
(gdb) n
6     puts ("error");
```

需要注意的是：

1. `jump`命令只改变pc的值，所以改变程序执行可能会出现不同的结果，比如变量i的值
2. 通过（临时）断点的配合，可以让你的程序跳到指定的位置，并停下来

#### [使用断点命令改变程序的执行](https://github.com/hellogcc/100-gdb-tips/blob/master/src/breakpoint-command.md)

这个例子程序可能不太好，只是可以用来演示下断点命令的用法：

```
(gdb) b drawing
Breakpoint 1 at 0x40064d: file win.c, line 6.
(gdb) command 1
Type commands for breakpoint(s) 1, one per line.
End with a line saying just "end".
>silent
>set variable n = 0
>continue
>end
(gdb) r
Starting program: /home/xmj/tmp/a.out 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Your number is 6
You win $3000!
[Inferior 1 (process 4134) exited normally]
```

可以看到，当程序运行到断点处，会自动把变量n的值修改为0，然后继续执行。

如果你在调试一个大程序，重新编译一次会花费很长时间，比如调试编译器的bug，那么你可以用这种方式在gdb中先实验性的修改下试试，而不需要修改源码，重新编译。

#### [修改被调试程序的二进制文件](https://github.com/hellogcc/100-gdb-tips/blob/master/src/patch-program.md)

gdb不仅可以用来调试程序，还可以修改程序的二进制代码。

缺省情况下，gdb是以只读方式加载程序的。可以通过命令行选项指定为可写：

```
$ gcc -write ./a.out
(gdb) show write
Writing into executable and core files is on.
```

也可以在gdb中，使用命令设置并重新加载程序：

```
(gdb) set write on
(gdb) file ./a.out
```

接下来，查看反汇编：

```
(gdb) disassemble /mr drawing 
Dump of assembler code for function drawing:
5 {
   0x0000000000400642 <+0>: 55 push   %rbp
   0x0000000000400643 <+1>: 48 89 e5 mov    %rsp,%rbp
   0x0000000000400646 <+4>: 48 83 ec 10 sub    $0x10,%rsp
   0x000000000040064a <+8>: 89 7d fc mov    %edi,-0x4(%rbp)

6   if (n != 0)
   0x000000000040064d <+11>: 83 7d fc 00 cmpl   $0x0,-0x4(%rbp)
   0x0000000000400651 <+15>: 74 0c je     0x40065f <drawing+29>

7     puts ("Try again?\nAll you need is a dollar, and a dream.");
   0x0000000000400653 <+17>: bf e0 07 40 00 mov    $0x4007e0,%edi
   0x0000000000400658 <+22>: e8 b3 fe ff ff callq  0x400510 <puts@plt>
   0x000000000040065d <+27>: eb 0a jmp    0x400669 <drawing+39>

8   else
9     puts ("You win $3000!");
   0x000000000040065f <+29>: bf 12 08 40 00 mov    $0x400812,%edi
   0x0000000000400664 <+34>: e8 a7 fe ff ff callq  0x400510 <puts@plt>

10 }
   0x0000000000400669 <+39>: c9 leaveq 
   0x000000000040066a <+40>: c3 retq   

End of assembler dump.
```

修改二进制代码（注意大小端和指令长度）：

```
(gdb) set variable *(short*)0x400651=0x0ceb
(gdb) disassemble /mr drawing 
Dump of assembler code for function drawing:
5 {
   0x0000000000400642 <+0>: 55 push   %rbp
   0x0000000000400643 <+1>: 48 89 e5 mov    %rsp,%rbp
   0x0000000000400646 <+4>: 48 83 ec 10 sub    $0x10,%rsp
   0x000000000040064a <+8>: 89 7d fc mov    %edi,-0x4(%rbp)

6   if (n != 0)
   0x000000000040064d <+11>: 83 7d fc 00 cmpl   $0x0,-0x4(%rbp)
   0x0000000000400651 <+15>: eb 0c jmp    0x40065f <drawing+29>

7     puts ("Try again?\nAll you need is a dollar, and a dream.");
   0x0000000000400653 <+17>: bf e0 07 40 00 mov    $0x4007e0,%edi
   0x0000000000400658 <+22>: e8 b3 fe ff ff callq  0x400510 <puts@plt>
   0x000000000040065d <+27>: eb 0a jmp    0x400669 <drawing+39>

8   else
9     puts ("You win $3000!");
   0x000000000040065f <+29>: bf 12 08 40 00 mov    $0x400812,%edi
   0x0000000000400664 <+34>: e8 a7 fe ff ff callq  0x400510 <puts@plt>

10 }
   0x0000000000400669 <+39>: c9 leaveq 
   0x000000000040066a <+40>: c3 retq   

End of assembler dump.
```

可以看到，条件跳转指令“je”已经被改为无条件跳转“jmp”了。

退出，运行一下：

```
$ ./a.out 
Your number is 2
You win $3000!
```

### 信号

[查看信号处理信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/info-signals.md)

[信号发生时是否暂停程序](https://github.com/hellogcc/100-gdb-tips/blob/master/src/stop-signal.md)

[信号发生时是否打印信号信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/print-signal.md)

[信号发生时是否把信号丢给程序处理](https://github.com/hellogcc/100-gdb-tips/blob/master/src/pass-signal.md)

[给程序发送信号](https://github.com/hellogcc/100-gdb-tips/blob/master/src/send-signal.md)

[使用“$_siginfo”变量](https://github.com/hellogcc/100-gdb-tips/blob/master/src/use-$_siginfo-variable.md)

### 共享库

#### [显示共享链接库信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/info_sharedlibrary.md)

使用"`info sharedlibrary regex`"命令可以显示程序加载的共享链接库信息，其中`regex`可以是正则表达式，意为显示名字符合`regex`的共享链接库。如果没有`regex`，则列出所有的库。以上面程序为例:

```
(gdb) start
Temporary breakpoint 1 at 0x109f0: file a.c, line 5.
Starting program: /export/home/nan/a
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Temporary breakpoint 1, main () at a.c:5
5                       char a[1026] = {0};
(gdb) info sharedlibrary
From        To          Syms Read   Shared Object Library
0xff3b44a0  0xff3e3490  Yes (*)     /usr/lib/ld.so.1
0xff3325f0  0xff33d4b4  Yes         /usr/local/lib/libhiredis.so.0.11
0xff3137f0  0xff31a9f4  Yes (*)     /lib/libsocket.so.1
0xff215fd4  0xff28545c  Yes (*)     /lib/libnsl.so.1
0xff0a3a20  0xff14fedc  Yes (*)     /lib/libc.so.1
0xff320400  0xff3234c8  Yes (*)     /platform/SUNW,UltraAX-i2/lib/libc_psr.so.1
(*): Shared library is missing debugging information.
```

可以看到列出所有加载的共享链接库信息，带“`*`”表示库缺少调试信息。

另外也可以使用正则表达式：

```
(gdb) i sharedlibrary hiredi*
From        To          Syms Read   Shared Object Library
0xff3325f0  0xff33d4b4  Yes         /usr/local/lib/libhiredis.so.0.11
```

可以看到只列出了一个库信息。

### 脚本

* [配置gdb init文件](https://github.com/hellogcc/100-gdb-tips/blob/master/src/config-gdbinit.md)
* [按何种方式解析脚本文件](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-script-extension.md)
* [保存历史命令](https://github.com/hellogcc/100-gdb-tips/blob/master/src/save-history-commands.md)

### 源文件

* [设置源文件查找路径](https://github.com/hellogcc/100-gdb-tips/blob/master/src/directory.md)
* [替换查找源文件的目录](https://github.com/hellogcc/100-gdb-tips/blob/master/src/substitute-path.md)

### 图形化界面

* [进入和退出图形化调试界面](https://github.com/hellogcc/100-gdb-tips/blob/master/src/tui-mode.md)
* [显示汇编代码窗口](https://github.com/hellogcc/100-gdb-tips/blob/master/src/layout-asm.md)
* [显示寄存器窗口](https://github.com/hellogcc/100-gdb-tips/blob/master/src/layout-regs.md)
* [调整窗口大小](https://github.com/hellogcc/100-gdb-tips/blob/master/src/winheight.md)

### 其他

* [命令行选项的格式](https://github.com/hellogcc/100-gdb-tips/blob/master/src/option-format.md)
* [支持预处理器宏信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/preprocessor-macro.md)
* [保留未使用的类型](https://github.com/hellogcc/100-gdb-tips/blob/master/src/keep-unused-types.md)

#### [使用命令的缩写形式](https://github.com/hellogcc/100-gdb-tips/blob/master/src/use-short-command.md)

在gdb中，你不用必须输入完整的命令，只需命令的（前）几个字母即可。规则是，只要这个缩写不会和其它命令有歧义（注，是否有歧义，这个规则从文档上看不出，看起来需要查看gdb的源代码，或者在实际使用中进行总结）。也可以使用tab键进行命令补全。

| 全称        | 缩写  | 作用                                                         |
| ----------- | ----- | ------------------------------------------------------------ |
| break       | b     | 打断点                                                       |
| tbreak      | tb    | 临时断点                                                     |
| continue    | c     | 从当前停止的位置开始继续运行                                 |
| ignore      | ig    | 忽略某个断点指定的次数。例：ignore 4 23 忽略断点4的23次运行，在第24次的时候中断 |
| delete      | d     | 删除断点                                                     |
| step        | s     | 单步到下一个不同的源代码行（包括进入函数）                   |
| next        | n     | 单步到程序源代码的下一行，不进入函数                         |
| stepi       | si    | 单步执行一个汇编指令                                         |
| nexti       | ni    | 单步一条机器指令，不进入函数                                 |
| jump        | j     |                                                              |
| run         | r     | 直接开始运行程序，直到断点位置停下或者程序结束               |
| start       |       | 命令敲击后，则程序从main函数的起始位置停下，开始逐步调试     |
| finish      | fin   | 继续执行，直到函数返回                                       |
| until       | u     |                                                              |
| quit        | q     | 退出调试                                                     |
| awatch      | aw    | 读写观察点，需要硬件观察点支持                               |
| watch       | wa    | 观察点                                                       |
| rwatch      | rw    | 读观察点，需要硬件观察点支持                                 |
| frame       | f     |                                                              |
| backtrace   | bt    | 查看函数调用的栈帧关系，查看程序运行调用栈信息               |
| info        | i     | info [name] 查看name信息                                     |
| print       | p     | 打印变量数据                                                 |
| x           |       |                                                              |
| display     |       | 在断点的停止的地方，显示指定的表达式的值。(显示变量)         |
| list        | l     | 显示调试行附近代码                                           |
| set         |       | 设置变量值                                                   |
| disassemble | disas |                                                              |
| directory   | dir   |                                                              |
| winheight   | win   |                                                              |

另外，如果直接按回车键，会重复执行上一次的命令

* [在gdb中执行shell命令和make](https://github.com/hellogcc/100-gdb-tips/blob/master/src/run-shell-command.md)
* [在gdb中执行cd和pwd命令](https://github.com/hellogcc/100-gdb-tips/blob/master/src/run-cd-pwd.md)
* [设置命令提示符](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-prompt.md)

#### [设置被调试程序的参数](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-program-args.md)

可以在gdb启动时，通过选项指定被调试程序的参数，例如：

```
gdb -args ./a.out a b c
```

也可以在gdb中，通过命令来设置，例如：

```
(gdb) set args a b c
(gdb) show args
Argument list to give program being debugged when it is started is "a b c".
```

也可以在运行程序时，直接指定：

```
(gdb) r a b
Starting program: /home/xmj/tmp/a.out a b
(gdb) show args
Argument list to give program being debugged when it is started is "a b".
(gdb) r
Starting program: /home/xmj/tmp/a.out a b 
```

可以看出，参数已经被保存了，下次运行时直接运行`run`命令，即可。

有意的是，如果我接下来，想让参数为空，该怎么办？是的，直接：

```
(gdb) set args
```

#### [设置被调试程序的环境变量](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-program-env.md)

在gdb中，可以通过命令`set env varname=value`来设置被调试程序的环境变量。对于上面的例子，网上可以搜到一些解决方法，其中一种方法就是设置LD_PRELOAD环境变量：

```
set env LD_PRELOAD=/lib/x86_64-linux-gnu/libpthread.so.0
```

[得到命令的帮助信息](https://github.com/hellogcc/100-gdb-tips/blob/master/src/help.md)

#### [记录执行gdb的过程](https://github.com/hellogcc/100-gdb-tips/blob/master/src/set-logging.md)

用gdb调试程序时，可以使用“`set logging on`”命令把执行gdb的过程记录下来，方便以后自己参考或是别人帮忙分析。默认的日志文件是“`gdb.txt`”，也可以用“`set logging file file`”改成别的名字。以上面程序为例：

```
(gdb) set logging file log.txt
(gdb) set logging on
Copying output to log.txt.
(gdb) start
Temporary breakpoint 1 at 0x8050abe: file a.c, line 6.
Starting program: /data1/nan/a 
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Temporary breakpoint 1, main () at a.c:6
6               char str1[] = "abcd";
(gdb) n
7               wchar_t str2[] = L"abcd";
(gdb) x/s str1
0x804779f:      "abcd"
(gdb) n       
9               return 0;
(gdb) x/ws str2
0x8047788:      U"abcd"
(gdb) q
A debugging session is active.

        Inferior 1 [process 9931    ] will be killed.

Quit anyway? (y or n) y
```

执行完后，查看log.txt文件：

```
bash-3.2# cat log.txt 
Temporary breakpoint 1 at 0x8050abe: file a.c, line 6.
Starting program: /data1/nan/a 
[Thread debugging using libthread_db enabled]
[New Thread 1 (LWP 1)]
[Switching to Thread 1 (LWP 1)]

Temporary breakpoint 1, main () at a.c:6
6               char str1[] = "abcd";
7               wchar_t str2[] = L"abcd";
0x804779f:      "abcd"
9               return 0;
0x8047788:      U"abcd"
A debugging session is active.

        Inferior 1 [process 9931    ] will be killed.

Quit anyway? (y or n)
```

可以看到log.txt详细地记录了gdb的执行过程。

此外“`set logging overwrite on`”命令可以让输出覆盖之前的日志文件；而 “`set logging redirect on`”命令会让gdb的日志不会打印在终端。

## 其他问题

### gdb 调试基本流程

1. 带着调试选项编译(加-g)，构建调试对象a.out（$g++ -Wall -o2 -g ./a.cpp，如果使用到TSD等还需要加编译选项-lpthread）
2. 启动gdb（开始运行指定程序并调试$gdb ./aout、attach到正在运行的进程并调试$gdb -p `pidof a.out`，gdb -tui分屏显示源代码）
3. source ./mygdbinit (载入gdb命令脚本文件mygdbinit，等效$gdb --commonds=./.mygdbinit)
4. 开始gdb（run：直接运行、start：设置main断点再运行，start 10 20 30：发送3个参数给执行程序的main函数）
5. 设置断点（一般<函数、行、地址、下行、偏移量、汇编机器指令行等>断点、内存断点、条件断点等:b，watch，awatch，rwatch，注意区别：b fun和b *fun的不同）
6. 显示所有断点（info b）
7. 删除断点（delete 断点编号、clear 详细断点、delete:删除所有断点，等）
8. 显示栈/栈帧（bt、bt N、bt -N、bt full、bt full N、bt full -N等，注：一个进程对应call-stack调用栈，一个函数对应stack-frame栈帧，显示栈帧：info frame，）
9. 显示汇编代码（显示指定函数的反汇编代码：disas main、disas funname，）
10. 显示值（pc命令行、ebp栈底指针、esp栈顶指针，以及各种寄存器值，struct结构变量值、Json结构变量值、一般变量variable值，class/struc结构对象指针this，再特殊的结构变量可以自己写gdb命令脚本来显示等）
11. 显示指定程序地址的源代码（list <*addr>：x/i $pc，list*$pc等）
12. 显示指定行号函数的源代码（list filename:lineNum，list filename:funname，多文件时必须加文件名）
13. 列出指定区域的代码（list line1Num, line2Num）
14. 显示main函数的参数列表（show args）
15. 显示info命令的其他使用(显示寄存器：info reg，显示三个寄存器的value：info r rbp rsp rip，显示当前函数的栈帧信息:info frame，显示当前调用文件的信息：info source，显示当前运行进程的信息：info proc，显示所有gdb设置：info set，显示当前所有的线程：info threads，显示所有的type名：info types，显示程序的输入参数列表信息：info args、显示当前代码块内部的局部变量：info local，显示所有自动显示设置：info desplay，info files、info proc mapping等)
16. 显示当前绝对路径（pwd）
17. 显示可执行文件的所有内存段起始范围（info file）
18. addr2line（由代码命令行地址 得到 代码命令行源代码）
19. 改变变量值（set：修改指定variable的值）
20. 修改main函数的参数列表（set args 11 12 13）
21. 继续执行（next、step、nexti、stepi，jump、return、call、continue、finish、until:代码级别执行，汇编级别执行，进入callee内部执行、跳跃式执行finish/until：untile addr执行到"addr"后暂停）
22. 查找gdb命令（complete：列出所有gdb命令，complete a：列出所有a开头的gdb命令等）
23. gdb命令行查看（ctrl-n看次新gdb命令、ctrl-p逐个看gdb的历史命令）
24. 在当前文件查找表达式（search a：在当前文件向下查找指定变量a或者表达式a，注：reverse-search a：是向上查找）
25. 将当前目录下的dir1子目录放入被搜索目录群中（dir dir1）
26. 进入dir1（cd dir1）
27. 讲一个信号发送给正在运行的程序（single 0 等效于 continue）
28. 附着到指定进程（attach 1920、attach `pidof ./other.out`）
29. 切换到指定线程（thread 20167或者t 20167）终止一个正在调试程序（kill命令）

### 断点如何实现

### 写C++代码时有一类错误是 coredump ，很常见，你遇到过吗？怎么调试这个错误？

**什么是 coredump**

coredump是程序由于异常或者bug在运行时异常退出或者终止，在一定的条件下生成的一个叫做core的文件，这个core文件会记录程序在运行时的内存，寄存器状态，内存指针和函数堆栈信息等等。对这个文件进行分析可以定位到程序异常的时候对应的堆栈调用信息。

在Linux下可通过core文件来获取当程序异常退出(如异常信号SIGSEGV, SIGABRT等)时的堆栈信息。core dump叫做核心转储，当程序运行过程中发生异常的那一刻的一个内存快照，操作系统在程序发生异常而异常在进程内部又没有被捕获的情况下，会把进程此刻内存、寄存器状态、运行堆栈等信息转储保存在一个core文件里，叫core dump。core文件是程序非法执行后core dump后产生的文件，该文件是二进制文件，可以使用gdb、elfdump、objdump打开分析里面的具体内容。

**产生core dump的可能原因**

* 内存访问越界
* 多线程程序使用了线程不安全的函数
* 多线程读写的数据未加锁保护
* 非法指针
* 堆栈溢出

**查看操作系统是否开启产生core文件**

输入命令：ulimit -c或ulimit -a， core file size为0，说明系统关闭了core dump，可通过命令：ulimit -c unlimited来打开，结果如下图所示，注：此设置只对当前终端有效，再打开一个新的终端时，输入ulimit -a，会发现core file size还是为0，若想始终有效，可在~/.bashrc文件最后加入命令：ulimit -c unlimited：

在终端通过命令`ulimit -c unlimited`只是临时修改，重启后无效 ，要想永久修改有三种方式：  

* 在/etc/rc.local 中增加一行 ulimit -c unlimited  
* 在/etc/profile 中增加一行 ulimit -c unlimited  
* 在/etc/security/limits.conf最后增加如下两行记录：

```javascript
@root soft core unlimited
@root hard core unlimited
```

**core文件的名称和生成路径**

在Linux下执行程序时如果有提示”core dumped”信息，则会在当前目录下产生一个core文件，若没有产生，则可能core dump没有打开。缺省情况下，内核在core dump 时所产生的 core 文件放在与执行程序相同的目录中，并且文件名固定为core，执行以下命令，可以将文件名修改为core.pid等形式，pid为执行当前程序的进程号，core_uses_pid中原始内容为0：

```
echo "1" > /proc/sys/kernel/core_uses_pid
```

测试代码main.cpp如下：

```c
#include <stdio.h>
#include <iostream>

namespace {

void func() {
 const char* p = "hello";
 delete p;
}

} // namespace

int main()
{
 fprintf(stdout, "test start\n");
 func();
 fprintf(stdout, "test finish\n");
}
```

依次执行如下命令，执行结果如下图所示，会在执行文件同一目录下产生core二进制文件：

```
g++ -g -o main main.cpp
./main
```

![img](https://img-blog.csdnimg.cn/20190724112246931.png)

**如果有多个程序产生core文件，或者同一个程序多次崩溃，就会重复覆盖同一个core文件**。通过修改kernel参数，可以指定内核所生成的core dump文件的文件名，如设置core文件名形式为core_programname_time_pid，所有的core文件存放在/usr/core_log目录下，则在终端输入以下命令：

```
echo /usr/core_log/core_%e_%t_%p >> /proc/sys/kernel/core_pattern
```

core默认的文件名称是core.pid，pid指的是产生段错误的程序的进程号。  默认路径是产生段错误的程序的当前目录。

如果想修改core文件的名称和生成路径，相关的配置文件为：  **/proc/sys/kernel/core_uses_pid：**控制产生的core文件的文件名中是否添加pid作为扩展，如果添加则文件内容为1，否则为0。

**/proc/sys/kernel/core_pattern：**可以设置格式化的core文件保存的位置和文件名，比如原来文件内容是core-%e。  可以这样修改:  echo “/corefile/core-%e-%p-%t” > /proc/sys/kernel/core_pattern  将会控制所产生的core文件会存放到/corefile目录下，产生的文件名为：core-命令名-pid-时间戳。

**以下是参数列表:**  %p - insert pid into filename 添加pid  %u - insert current uid into filename 添加当前uid  %g - insert current gid into filename 添加当前gid  %s - insert signal that caused the coredump into the filename 添加导致产生core的信号  %t - insert UNIX time that the coredump occurred into filename 添加core文件生成时的unix时间  %h - insert hostname where the coredump happened into filename 添加主机名  %e - insert coredumping executable name into filename 添加命令名。  一般情况下，无需修改，按照默认的方式即可。

**gdb 调试 core 文件的步骤**

使用gdb调试core文件来查找程序中出现段错误的位置时，要注意的是可执行程序在编译的时候需要加上-g编译命令选项。

gdb调试core文件的步骤常见的有如下几种，推荐第一种。

**具体步骤一：**  

（1）启动gdb，进入core文件，命令格式：**gdb [exec file] [core file]**。   用法示例：gdb ./test test.core。

（2）在进入gdb后，查找段错误位置：**where或者bt**  用法示例：  

![img](https://ask.qcloudimg.com/raw/yehe-4fad69835728/oy8gvnduzo.png)

  可以定位到源程序中具体文件的具体位置，出现了段错误。

**具体步骤二：**  （1）启动gdb，进入core文件，命令格式：**gdb –core=[core file]**。  用法示例：gdb –core=test.core。

（2）在进入gdb后，指定core文件对应的符号表，命令格式：**file [exec file]** .  用法示例：  

![img](https://ask.qcloudimg.com/raw/yehe-4fad69835728/u0hpsw096g.png)

（3）查找段错误位置：**where或者bt**。  用法示例：  

![img](https://ask.qcloudimg.com/raw/yehe-4fad69835728/4e30ywe402.png)

**具体步骤三：**  

（1）启动gdb，进入core文件，命令格式：**gdb -c [core file]**。  用法示例：gdb -core test.core。  （2）其它步骤同步骤二。

**通过gdb打开core文件**：

形式如下：`$ gdb programname core`，查看core dump时的堆栈信息，可以使用bt或where命令，如下图所示，注：在编译程序时加上-g选项才能够获取到具体的信息：

![img](https://img-blog.csdnimg.cn/2019072411240828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZlbmdiaW5nY2h1bg==,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20190724112424593.png)

**通过`gdb help generate-core-file`产生 core 文件**

依次执行如下命令，执行结果如下图所示，产生的core.pid好像不如上面方法产生的core文件信息详细：

```
gdb main
help generate-core-file
start
generate-core-file
```

![img](https://img-blog.csdnimg.cn/2019072411254752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZlbmdiaW5nY2h1bg==,size_16,color_FFFFFF,t_70)

**core 缺点**

* 因为 core 文件是对当时进程地址空间的镜像，所以 core 文件大小一般都会比较大，这样很占用磁盘空间，而且如果要将文件从服务器上下载到本地分析也会比较耗时。
* 对于缓冲区溢出导致的 coredump ，进程的调用堆栈已经被覆盖破坏了， core 文件显示的堆栈信息往往错误。
* 程序因SIGALRM、SIGPIPE等信号崩溃，则不会产生 core 文件。

**在某些情况下core文件的堆栈调用顺序会被溢出的内容覆盖，这时就没办法使用backtrace命令查看调用堆栈了，这种情况下如何定位问题**

1, GDB失效时手工得到stack；

2, GDB执行用户命令脚本；

调试内存型服务程序的有时会遇到core dump或死锁问题，且gdb或者pstack都无法显示调用栈（call stack）。这是因为线程的调用栈被破坏了，而调用栈存放了函数的返回地址，gdb解析函数返回地址（根据地址查找符号表）失败，gdb也没有进行容错处理，只要有一处地址解析失败就无法展开调用栈。然而幸运的是，调用栈往往只是部分被破坏，RSP堆栈寄存器中保存的值往往也是正确的，可以通过手工的方法恢复。具体做法如下：

```
(gdb) set logging on
Copying output to gdb.txt.
(gdb) x /2000a $rsp
0x426cb890: 0x0 0x4
0x426cb8a0: 0x426cb8c0 0x100
0x426cb8b0: 0x3e8 0x552f59 <_ZN5tbnet16EPollSocketEvent9getEventsEiPNS_7IOEventEi+41>
0x426cb8c0: 0x1823c8a000000011 0x0
0x426cb8d0: 0x0 0x0
0x426cb8e0: 0x0 0x0
...
```

如上图，类似”0x552f59 <_ZN5tbnet16EPollSocketEvent9getEventsEiPNS_7IOEventEi+41>”这样的代码符号看起来是有效的。通过所有看似有效的程序代码符号基本能够得出core dump时的调用栈。

当然，有可能出现core dump线程的调用栈被完全破坏的情况，通过上述方法恢复的信息仍然是无效的。由于每个线程堆栈地址空间的大小为10M，因此，线程之间互相破坏调用堆栈的可能性几乎是不存在的，此时，可以通过其它线程的调用栈分析其行为，往往也能找到线索。如果所有线程的调用栈都“看似被破坏”，那么，往往有两种可能：

a, 可执行程序和core文件对不上，被摆乌龙了，如发现core dump问题的时候可执行程序已经更新到最新版本，老版本没有保存；

b, 磁盘满了或者ulimit设置太小，导致core dump文件信息不全；

如果core文件对不上或者信息不全的问题，还可以通过dmesg命令找到程序core dump时的指令寄存器RIP的值，再通过addr2line获取程序最后执行的代码行。如：

```
[rizhao.ych@OceanBase036040 updateserver]$ dmesg | grep updateserver
updateserver[8099]: segfault at 0000000000000000 rip 0000000000500fbf rsp 000000004c296e30 error 4
[rizhao.ych@OceanBase036040 updateserver]$ addr2line -e updateserver 0000000000500fbf
/home/rizhao/dev/oceanbase/src/common/ob_base_server.cpp:222
```

* -fstack-protector-all (<https://www.ibm.com/developerworks/cn/linux/l-cn-gccstack/index.html>) 该选项旨在通过Canaries检测来判断栈是否被破坏，在破坏前让程序抛异常退出。 当然还有另外一个目的是提高栈溢出攻击的成本。有兴趣的可以了解下原理。通过这种方式来进行堆栈保护
* -g 加上g选项后编译器会做如下额外操作
  * 创建符号表，生成的文件包含符号表内容。生成的core文件也会附带符号表相关内容
  * 关闭优化机制，程序严格按照原来的代码执行，避免定位问题对应代码带来的偏移干扰
* 开启-O0 选项避免参数和函数inline。 生产环境建议开启为-O2 。这里为了方便定位问题，临时调整成该选项。

### 多线程调试

<img src="https://cdn.jsdelivr.net/gh/luogou/cloudimg/data/202203151449773.png" alt="这里写图片描述" style="zoom: 80%;float:left" />

* info threads 查看所有线程，默认分支是主线程

* thread id 切换线程

* bt 查看每个线程的栈帧然后设置断点

* thread apply （n或all） 命令使用thread apply来让一个或是多个线程执行指定的命令。例如让所有的线程打印调用栈信息。

* set scheduler-locking on 锁定只有当前的线程能够执行
