# Linux命令56问

<https://www.nowcoder.com/discuss/813169>

<https://gitee.com/Stard/xkzz-study/blob/master/docs/Linux%E5%91%BD%E4%BB%A456%E9%97%AE.md>

目录如下，请善用CTRL+F查找

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934326496/image-20211126214357811.png)

## 1. 查看整机系统性能 TOP的指令？

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093640/image-20200326162329550.png)

- 使用top命令的话，重点关注的是 %CPU、%MEM 、load average 三个指标

- 在这个命令下，按1的话，可以看到每个CPU的占用情况

- uptime：系统性能命令的精简版

- 参考：<https://www.jb51.net/article/135852.htm>

- 第一行是任务队列信息(系统运行状态及平均负载)，与uptime命令结果相同
- up部分的字段信息代表了当前系统的运行时间，即未重启时间，时间越长系统越稳定
- load average 任务队列的平均长度
  - 单核情况下，1.0为满负荷，超过1为超负荷，理想值为0.7
  - 多核情况下，CPU核数*0.7=理想负荷

- 第二行是tasks任务进程相关信息
- 包括了进程总数、正在运行的进程数、睡眠进程数、停止进程数和僵尸进程数(zombie)

- 第三行是CPU相关信息，如果是多核CPU，按数字1可显示各核CPU信息，此时1行将转为Cpu核数行，数字1可以来回切换
  - us 用户空间占用CPU百分比，例如：Cpu(s): 12.7%us
  - sy 内核空间占用CPU百分比，例如：8.4%sy
  - ni 用户进程空间内改变过优先级的进程占用CPU百分比，例如：0.0%ni
  - id 空闲CPU百分比，例如：77.1%id
  - wa 等待输入输出的CPU时间百分比，例如：0.0%wa
  - hi CPU服务于硬件中断所耗费的时间总额，例如：0.0%hi
  - si CPU服务软中断所耗费的时间总额，例如：1.8%si
  - st Steal time 虚拟机被hypervisor偷去的CPU时间（如果当前处于一个hypervisor下的vm，实际上hypervisor也是要消耗一部分CPU处理时间的）

- 第四行是内存相关信息（Mem: 12196436k total, 12056552k used, 139884k free, 64564k buffers）
  - 用作内核缓存的内存量，例如：64564k buffers

- 第五行是Swap 交换分区相关信息（Swap: 2097144k total, 151016k used, 1946128k free, 3120236k cached）
  - 缓冲的交换区总量，3120236k cached

- 进程信息

  - 在top命令中按f按可以查看显示的列信息，按对应字母来开启/关闭列，大写字母表示开启，小写字母表示关闭。带*号的是默认列。

    - 按f进入列表后，通过空格来选定要显示哪些列

    A: PID = (Process Id) 进程Id；
    E: USER = (User Name) 进程所有者的用户名；
    H: PR = (Priority) 优先级
    I: NI = (Nice value) nice值。负值表示高优先级，正值表示低优先级
    O: VIRT = (Virtual Image (kb)) 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
    **Q: RES = (Resident size (kb)) 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA**
    **T: SHR = (Shared Mem size (kb)) 共享内存大小，单位kb**
    W: S = (Process Status) 进程状态。D=不可中断的睡眠状态,R=运行,S=睡眠,T=跟踪/停止,Z=僵尸进程
    K: %CPU = (CPU usage) 上次更新到现在的CPU时间占用百分比
    N: %MEM = (Memory usage (RES)) 进程使用的物理内存百分比
    M: TIME+ = (CPU Time, hundredths) 进程使用的CPU时间总计，单位1/100秒
    b: PPID = (Parent Process Pid) 父进程Id
    c: RUSER = (Real user name)
    d: UID = (User Id) 进程所有者的用户id
    f: GROUP = (Group Name) 进程所有者的组名
    g: TTY = (Controlling Tty) 启动进程的终端名。不是从终端启动的进程则显示为 ?
    j: P = (Last used cpu (SMP)) 最后使用的CPU，仅在多CPU环境下有意义
    p: SWAP = (Swapped size (kb)) 进程使用的虚拟内存中，被换出的大小，单位kb
    l: TIME = (CPU Time) 进程使用的CPU时间总计，单位秒
    r: CODE = (Code size (kb)) 可执行代码占用的物理内存大小，单位kb
    s: DATA = (Data+Stack size (kb)) 可执行代码以外的部分(数据段+栈)占用的物理内存大小，单位kb
    u: nFLT = (Page Fault count) 页面错误次数
    v: nDRT = (Dirty Pages count) 最后一次写入到现在，被修改过的页面数
    y: WCHAN = (Sleeping in Function) 若该进程在睡眠，则显示睡眠中的系统函数名
    z: Flags = (Task Flags <sched.h>) 任务标志，参考 sched.h
    X: COMMAND = (Command name/line) 命令名/命令行

- 命令选项
  - -b：以批处理模式操作；
    -c：显示完整的治命令；
    -d：屏幕刷新间隔时间；
    -I：忽略失效过程；
    -s：保密模式；
    -S：累积模式；
    -i<时间>：设置间隔时间；
    -u<用户名>：指定用户名；
    -p<进程号>：指定进程；
    -n<次数>：循环显示的次数。

## 2. 查看CPU性能的指令？vmstat？

- 查看CPU（包含但是不限于）
- 查看额外
  - 查看所有CPU核信息：mpstat -p ALL 2
  - 每个进程使用CPU的用量分解信息：pidstat -u 1 -p 进程编号

命令格式：`vmstat -n 2 3`

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093604/image-20200326162803165.png)

一般vmstat工具的使用是通过两个数字参数来完成的，第一个参数是采样的时间间隔数（单位秒），第二个参数是采样的次数

**procs**

r：运行和等待的CPU时间片的进程数，原则上1核的CPU的运行队列不要超过2，整个系统的运行队列不超过总核数的2倍，否则代表系统压力过大，我们看蘑菇博客测试服务器，能发现都超过了2，说明现在压力过大

b：等待资源的进程数，比如正在等待磁盘I/O、网络I/O等

**cpu**

us：用户进程消耗CPU时间百分比，us值高，用户进程消耗CPU时间多，如果长期大于50%，优化程序

sy：内核进程消耗的CPU时间百分比

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093592/image-20200326164521263.png)

us + sy 参考值为80%，如果us + sy 大于80%，说明可能存在CPU不足，从上面的图片可以看出，us + sy还没有超过百分80，因此说明蘑菇博客的CPU消耗不是很高

id：处于空闲的CPU百分比

wa：系统等待IO的CPU时间百分比

st：来自于一个虚拟机偷取的CPU时间比

## 3. 查看内存使用情况的指令？free？

- 应用程序可用内存数：free -m
- 应用程序可用内存/系统物理内存 > 70% 内存充足
- 应用程序可用内存/系统物理内存 < 20% 内存不足，需要增加内存
- 20% <  应用程序可用内存/系统物理内存 < 70%，表示内存基本够用

free -h：以人类能看懂的方式查看物理内存

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093604/image-20200326170217637.png)

free -m：以MB为单位，查看物理内存

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093596/image-20200326165815071.png)

free -g：以GB为单位，查看物理内存

## 4. 查看硬盘使用情况的指令？df？

**df**

df 查看磁盘分区的使用情况，了解磁盘总量及用量，默认单位为KB

![image.png](https://s2.loli.net/2022/12/12/hf7AbZIKQsdaRYy.png)

**du**

du 命令用于查看文件、目录在磁盘中占用的空间的大小

与ls -h不同之处在于，ls -h是查看文件或目录的实际大小，而du是查看文件或者目录在磁盘中占用的块区的大小。由于块大小为4k，且同一块中只能存放一个文件，因此当文件实际大小不足4k时，du命令的显示结果依然为4k。



iostat

系统慢有两种原因引起的，一个是CPU高，一个是大量IO操作

格式：`iostat -xdk 2 3`

命令参数：

 **-c：** 显示CPU使用情况
 **-d：** 显示磁盘使用情况
 **-N：** 显示磁盘阵列(LVM) 信息
 **-n：** 显示NFS 使用情况
 **-k：** 以 KB 为单位显示
 **-m：** 以 M 为单位显示
 **-t：** 报告每秒向终端读取和写入的字符数和CPU的信息
 **-V：** 显示版本信息
 **-x：** 显示详细信息
 **-p：**[磁盘] 显示磁盘和分区的情况

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093943/image-20200326170522559.png)

磁盘块设备分布：

rkB /s：每秒读取数据量kB；

wkB/s：每秒写入数据量kB；

svctm I/O：请求的平均服务时间，单位毫秒

await I/O：请求的平均等待时间，单位毫秒，值越小，性能越好

util：一秒钟有百分几的时间用于I/O操作。**接近100%时，表示磁盘带宽跑满，需要优化程序或者增加磁盘；**

* rkB/s，wkB/s根据系统应用不同会有不同的值，**但有规律遵循：长期、超大数据读写，肯定不正常，需要优化程序读取。**
* svctm 的值与 await的值**很接近，表示几乎没有I/O等待**，磁盘性能好，**如果await的值远高于svctm的值，则表示I/O队列等待太长，需要优化程序或更换更快磁盘**

## 5. 查看网络IO情况的指令？ifstat？

- 默认本地没有，下载ifstat

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093880/image-20200326171559406.png)

## 6. 查看机器已建立的TCP连接的指令？

- netstat命令
- 其中包含了唯一标识一条连接的四元组

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093988/1603012083908.png)

# Linux常用命令

## 7. 有哪些常用的Linux指令？

- 文件管理：
  - ls
  - cd
  - touch
  - rm
  - mkdir
  - mv 移动文件
  - cp
  - chmod 修改权限
- 进程管理
  - ps  显示进程信息
  - kill  杀死进程
- 系统管理
  - top 系统运行信息
  - free  内存
  - vmstat  cpu信息
- 网络通信
  - ping  测试网络连通性
  - netstat  网络相关信息

## 8. cd命令的作用？

- 回到上一次所在目录

- ```
  cd -
  ```

## 9. mkdir命令的作用？

- 创建多层目录

- ```
  mkdir -p xiyou/dssz/meihouwang
  ```

- rmdir   删除空目录

## 10. cp命令的作用？

- cp [选项] source dest
- -r  递归复制整个文件夹

## 11. rm命令的作用？

- rm [选项]  deleteFile

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093937/1606709194257.png)

## 12. mv命令的作用？

- 移动文件与重命名

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934093954/1606709249960.png)

## 13. cat命令的作用？

- 查看文件内容

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094091/1606709280417.png)

- 一般用于一页能显示完的内容

## 14. more命令的作用？

- 文件内容分屏查看器

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094222/1606719574958.png)

- less的功能类似，不过不是一次性加载整个文件，而是按照需要展示的部分来加载

## 15. echo命令的作用？

- 输出内容到控制台

- 配合参数 -e 能够输出反斜线控制的字符

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094258/1606719677549.png)

## 16. head和tail命令的作用？  

- 显示文件的头部和尾部

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094244/1606719754924.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094256/1606719764876.png)

## 17. >和>>的作用和区别？

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094325/1606719825771.png)

## 18. ln命令的作用？

- 软连接

- 类似于快捷方式

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094386/1606719934509.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094541/1606719948770.png)

## 19. date命令的作用？

- 时间日期类

- date
  - 显示当前时间
- 设置系统当前时间

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094581/1606720105949.png)

## 20. 文件属性了解吗？

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094639/1606720743133.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094647/1606720753151.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094630/1606720764734.png)

## 21. chmod命令的作用？

- 改变权限

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094690/1606720881816.png)

## 22. find命令的作用？

- 查找文件或者目录

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094861/1606721003823.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094871/1606721011447.png)

## 23. grep命令的作用？

- 过滤查找及“|”管道符

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094959/1606721080390.png)

## 24. which命令的作用？

- 查找命令

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934094968/1606721101414.png)

## 25. tar命令的作用

- 打包

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095040/1606721295807.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095029/1606721302262.png)

## 26. df命令的作用？

- 查看磁盘空间使用情况

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095150/1606721623318.png)

## 27. ps命令的作用？

- 查看当前系统进程状态

- 常用选项

- -e 显示所有进程。
  -f 全格式。
  -h 不显示标题。
  -l 长格式。
  -w 宽输出。
  -a 显示终端上的所有进程，包括其他用户的进程。
  -r 只显示正在运行的进程。

  -u 以用户为主的格式来显示程序状况。

  -x 显示所有程序，不以终端机来区分。

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095126/1606721829770.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095258/1606721841457.png)

## 28. kill命令的作用？

- 终止进程

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095270/1606721915115.png)

## 29. top命令的作用？

- 查看系统健康状态

- <https://www.cnblogs.com/loved-wangwei/p/8986287.html>

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095344/image-20210107173156561.png)

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095432/image-20210107173106974.png)

## 30. 如何显示网络统计信息和端口占用情况？

- netstat 命令

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095394/1606722111248.png)

- 参数说明
  - -a
    - 显示所有套接字，包括监听的和未监听的
  - -t
    - 选出TCP套接字
  - -u
    - 选出UDP套接字
  - -l
    - 选出处于listen状态的连接
  - -n
    - 禁止使用端口的别名替代数字，比如说ssh代替22端口
  - -p
    - 显示连接归属的进程信息，可以查看端口被哪个进程占用
  - -i
    - 显示网卡信息

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095474/1606722120361.png)

## 31. 如何查看某个端口是否被占用？

```
netstat  -nlp  |  grep   xxxxx
```

# 功能实现题

## 32. 如何利用linux的指令来查询一个文件的行数？

- wc [选项] 文件
  - -c 统计字节数
  - -l 统计行数
  - -w 统计字数
  - -m 统计字符数
- 一般选项不加时，默认-lcw，显示结果依次为行数、字节数、字数

## 33. linux下统计一个文件中每个id的出现次数？

- 举例：检查一个文件中“404”出现的次数

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095575/1601088534075.png)

- grep  就是按参数进行过滤
- grep -o 一条数据里面有多个相同，会统计相同的次数
- grep 一条数据里面有多个相同，会统计一次次数
- wc -l  见上，就是统计行数

## 34. Linux 在多个文件中查找字符串？

- 文件不多的情况下

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095624/1601089009261.png)

- 文件多的情况下

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095638/1601089065829.png)

- xargs   将读入的数据重新格式化，默认是将换行和空格替换为空格

![img](https://uploadfiles.nowcoder.com/files/20211126/550958136_1637934095656/1601089140423.png)

## 35. 如何查看占用cpu最多进程？

- 通过以下语句获取每一列字段的含义（即标题行）

```shell
ps aux | head -1
```

- 首先 ps aux能够输出所有的进程
- 然后，grep  -v PID 命令将包含PID的标题行去掉
- 接着， sort  -rn  -k  +3 ，按照第三列数据进行排序，-r是降序排序，-n是按数值进行排序，-k用来指定列
- head -1 获取排序后的第一行数据

```shell
ps aux|grep -v PID|sort -rn -k +3|head
```

## 36. 如何找到 Java 进程中哪个线程占用了大量 CPU 处理时间？

- 首先根据上面一步能定位到PID

- 接下来，利用下面的命令查看线程占用资源的情况

  - -H   #Threads mode 是否开启线程模式，默认是off

    -p   # PID monitoring 只显示某个进程的信息，e.g: top -P 488 只显示进程号为488的进程信息

    -o   # sort 排序，-o fieldname ,指定要排序的字段 ,

```shell
top -H -p [PID]
```

- 对于上述结果，通过ctrl+p对结果按照cpu进行排序，得出哪个线程占用cpu最高(下面以544为例)
- 先将544转为16进制的220
- 接下来将该java进程的堆栈信息输出到一个文件

```shell
jstack [PID] > jstack.txt
```

- 然后在该文件中找到对应的线程ID
  - 利用grep -n可以定位到nid=0x220的行数
  - 然后查询指定行附近的内容(前10行，后20行等)

## 37. 如何找到占用内存最多的进程？

```shell
ps aux|grep -v PID|sort -rn -k +4|head
```

## 38. 磁盘不够了，如何快速找出磁盘占用最大的文件？

```shell
du -h * | sort -rn | head -1
```

- du命令可以查看文件的大小

## 39. 找到大于某个阈值大小的文件？

```shell
find / -type f -size +10G
```

- 查找在/目录下文件大小大于10G的文件

## 40. 查找某个名称或者类型的文件？

```shell
find / -name *.ppt
```

## 41. 查看某个文件的大小？

- <https://www.cnblogs.com/xiaojianblogs/p/6697915.html>

```shell
du -h /usr/local/apache2/logs/access_log
```

- 获取某个文件夹的大小

```shell
du -sh data
```

## 42. 查看某log文件某个字符串的前后5行？

- grep  -n  能够将包含指定项目的行以及对应的行号显示出来

```shell
grep -n "b" a.txt
```

- 查找指定行附近的内容

```shell
# 错误行定位到了8786830 下面命令能查看前20行和后10行
tail -n +8786810 err.log |head -n 30
```

## 43. 找到上述行中的最后一列？

- awk  '{print $NF}'  打印出最后一列
- 'NR>1 {print $NF}' 能跳过第一行

```shell
grep "b" a.txt | awk '{print $NF}'
```

## 44. 如果最后一列是10 20 10 30，那么如何统计每个数字出现的次数，比如输出2~10 1~20 1~30

- awk里面通过""来拼接字符串
- xargs -n1  每行显示一个字符
- uniq -c  去重，并且将出现次数带上

```shell
grep "b" a.txt | awk '{print $NF}' | xargs -n1 | sort | uniq -c | awk '{print $2"~"$1}'
```

## 45. 查找log的前5行，后5行？

```shell
head -n 5 log
tail -n 5 log
```

## 46. 写输出数字 0 到 500 中 7 的倍数(0 7 14 21...)的命令？

```shell
seq 0 7 500
```

seq 用于生成从一个数到另一个数之间的所有整数。

用法：seq [选项]... 尾数
或：seq [选项]... 首数 尾数
或：seq [选项]... 首数 增量 尾数

## 47. 输出文件中某一行数据

```shell
tail -n +5 | head -1
```

- 先找到第5行以后的数据
- 然后通过head输出这些数据中的第一行

## 48. 如何判断IP是否可以访问？

```shell
ping ip
```

## 49. 如何判断某个ip的端口是否可以访问？

```shell
telnet ip port
```

- 后接ctrl+]可以给端口发送数据包

## 50. 如何用命令行请求web服务器？

```shell
curl https://www.example.com
```

- 不带参数，默认是get请求

## 51. 如何显示某个端口的TCP连接？

```shell
netstat -anp | grep ":8080"
// -t 表示过滤TCP连接
```

## 52. 如何统计处于各个状态的连接个数？

```shell
netstat -anp | awk '{print $6}' | sort | uniq -c | sort -n 
```

## 53. 如何查看进程的占用的文件符情况？

```shell
lsof | grep hello.c
```

- lsof显示的结果，从左往右分别代表：打开该文件的程序名，进程id，用户，文件描述符，文件类型，设备，大小，iNode号，文件名。

```shell
COMMAND   PID                      USER   FD             TYPE        DEVICE SIZE/OFF   NODE   NAME
vi        27940                    hyb    7u      REG               8,15     16384     137573 /home/hyb/.1.txt.swp
```

## 54. less和vim如何查看日志并寻找关键字？

### less

- 基本参数
  - <https://www.cnblogs.com/molao-doing/articles/6541455.html>

```
b 向后翻一页
d 向后翻半页
/字符串：向下搜索“字符串”的功能
？字符串：向上搜索“字符串”的功能
空格键 滚动一行
回车键 滚动一页
q 退出less
n：重复前一个搜索（与 / 或 ？ 有关）
N：反向重复前一个搜索（与 / 或 ？ 有关）
```

## vim

- /+关键字 ，回车即可。此为从文档当前位置向下查找关键字，按n键查找关键字下一个位置，N往回找；
- ?+关键字，回车即可。此为从文档挡圈位置向上查找关键字，按n键向上查找关键字，N往回找；

## 55. vim如何删除游标所在行

- dd删除

## 怎么查询24小时内修改过的文件？

Linux的终端上，没有windows的搜索那样好用的图形界面工具，但find命令确是很强大的。

比如按名字查找一个文件，可以用 find / -name targetfilename 。 唉，如果只知道名字，不知道地点，这样也不失为一个野蛮有效的方法。

按时间查找也有参数 -atime 访问时间 -ctime 改变状态的时间 -mtime修改的时间。但要注意，这里的时间是以24小时为单位的。

查看man手册后使用，你会很迷惑： -mtime n： Files data was last modified n*24 hours ago. 字面上的理解是最后一次修改发生在n个24小时以前的文件，但实际上

**find ./ -mtime 0：返回最近24小时内修改过的文件。**

find ./ -mtime 1 ： 返回的是前48~24小时修改过的文件。而不是48小时以内修改过的文件。

那怎么返回10天内修改过的文件？find还可以支持表达式关系运算，所以可以把最近几天的数据一天天的加起来：

find ./ -mtime 0 -o -mtime 1 -o -mtime 2 ……虽然比较土，但也算是个方法了。

还有没有更好的方法，我也想知道。

另外， -mmin参数-cmin / - amin也是类似的。

# 系统优化

## 56. 系统卡顿，如何排查

- 首先通过  top -c   命令显示当前进程的运行列表
- 然后，按一下P按照CPU使用率进行排序，得到CPU使用率最高的进程(2609)
- 接着，使用   top -Hp 2609   找出这个进程下面的线程，继续按P进行排序
- 然后，可以找到消耗CPU最多的线程
  - 此处需要将线程号转为十六进制   2854->b26
- 然后，导出进程快照，看看线程做了什么
  - `jstack -l 2609 > ./2609.stack`
- 再用grep查看线程在文件中做了什么
  - `cat 2609.stack |grep 'b26' -C 8`
