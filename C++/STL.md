# STL

## 问题

### 基本认识

* 什么是 STL？
* 常见容器性质总结？
* 容器内部删除一个元素？
* 容器 size 和 capacity 区别，reserve 和 resize 区别
* STL 容器是线程安全的吗？

### 仿函数

* 谈谈仿函数
* STL中仿函数有什么用，和函数指针有什么不同，哪个效率高

### 空间配置器

* allocator 的作用
* STL中的allocator、deallocator
* STL的两级空间配置器
* STL 内存池
* C++的内存管理方式，STL的allocaotr，最新版本默认使用的分配器

### 迭代器

* STL 每种容器的迭代器种类
* STL 迭代器如何实现
* 迭代器失效的情况
* 迭代器：++it、it++哪个好，为什么

### vector

* vector 的实现？

* vector 如何扩容？

* vector 扩容后会做哪些操作vector扩容后把旧空间的数据搬到新空间用拷贝构造还是移动构造<https://blog.csdn.net/qiuguolu1108/article/details/114796903#t4>

* 什么时候用vector,什么时候用list，有什么区别

* 定义了一个vector, 内存是如何管控的， 放在哪里（讲了动态内存， 后面被打断， 想问的是两个阶段的分配地址， 第一个是在连接过程中的重定位， 一个是加载到内存的物理地址

* vector的增加删除都是怎么做的？

* vector如何释放空间？

* `vector<bool>` 有什么问题？

* `vector` 元素类型可以是引用吗，void 呢？

* 线程安全 `vector` 设计

* `vector` 作为函数返回值

* `emplace_back` 和 `push_back`

* 一个 `vector` 内存很大但实际我只用了很小一部分怎么解决

* 函数里的 `vector` 存在堆上还是栈上，为什么？

### （multi）map/set

* map 中[] 与 find 的区别？
* map 插入方式有几种？
* STL中unordered_map 和 map 的区别和应用场景
* （multi）map/set是怎么实现的
* set和map  的区别，multimap和multiset的区别
* 为何 map 和 set的插入效率比其他序列容器高，而且每次 insert 之后，以前保持的 iterator 不会失效
* 为何 map 和 set 不能像 vector 一样有个 reserve 函数来实现预分配

### unordered_map/set

* STL中hash_map扩容发生什么？
* STL中unordered_map和map的区别，hash_map如何解决冲突以及扩容
* unordered_map 的实现
* hashtable中解决冲突有哪些方法？
* hash表的实现，包括STL中的哈希桶长度常数。
* hash表如何rehash，怎么处理其中保存的资源
* 哈希表的桶个数为什么是质数，合数有何不妥？
* 初始哈希表大小多少合适

### priority_queue

* priority_queue 的实现？
* 从效率角度考虑，通过什么 数据 结构可实现优先级队列？

### list/stack/queue/forward_list

* list的实现
* list 为什么不用单向链表而用双向链表，二者有什么区别，size差多少，各自优缺点
* STL中stack和queue的实现
* forward_list 的实现
* STL中list、dqueue、vector 之间的区别。

### deque

* deque 的实现
* deque 和 vector 的区别

## 回答

### 基本认识

#### 什么是 STL

[STL](http://c.biancheng.net/stl/) 是由容器、算法、迭代器、函数对象、适配器、内存分配器这 6 部分构成，其中后面 4 部分是为前 2 部分服务的，它们各自的含义如表 1 所示。

| STL的组成  | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| 容器       | 一些封装[数据结构](http://c.biancheng.net/data_structure/)的模板类，例如 vector 向量容器、list 列表容器等。 |
| 算法       | STL 提供了非常多（大约 100 个）的数据结构算法，它们都被设计成一个个的模板函数，这些算法在 std 命名空间中定义，其中大部分算法都包含在头文件 <algorithm> 中，少部分位于头文件 <numeric> 中。 |
| 迭代器     | 在 [C++](http://c.biancheng.net/cplus/) STL 中，对容器中数据的读和写，是通过迭代器完成的，扮演着容器和算法之间的胶合剂。 |
| 函数对象   | 如果一个类将 () 运算符重载为成员函数，这个类就称为函数对象类，这个类的对象就是函数对象（又称仿函数）。 |
| 适配器     | 可以使一个类的接口（模板的参数）适配成用户指定的形式，从而让原本不能在一起工作的两个类工作在一起。值得一提的是，容器、迭代器和函数都有适配器。 |
| 内存分配器 | 为容器类模板提供自定义的内存申请和释放功能，由于往往只有高级用户才有改变内存分配策略的需求，因此内存分配器对于一般用户来说，并不常用。 |

#### 常见容器性质总结？

* vector 底层数据结构为数组 ，支持快速随机访问
* ist 底层数据结构为双向链表，支持快速增删
* deque 底层数据结构为一个中央控制器和多个缓冲区，详细见STL源码剖析P146，支持首尾（中间不能）快速增删，也支持随机访问。
 deque是一个双端队列(double-ended queue)，也是在堆中保存内容的.它的保存形式如下:[堆1] --> [堆2] -->[堆3] --> ...每个堆保存好几个元素,然后堆和堆之间有指针指向,看起来像是list和vector的结合品.
* stack 底层一般用list或deque实现，封闭头部即可，不用vector的原因应该是容量大小有限制，扩容耗时
* queue 底层一般用list或deque实现，封闭头部即可，不用vector的原因应该是容量大小有限制，扩容耗时（stack和queue其实是适配器,而不叫容器，因为是对容器的再封装）
* priority_queue 的底层数据结构一般为vector为底层容器，堆heap为处理规则来管理底层容器实现
* set 底层数据结构为红黑树，有序，不重复
* multiset 底层数据结构为红黑树，有序，可重复
* map 底层数据结构为红黑树，有序，不重复
* multimap 底层数据结构为红黑树，有序，可重复
* unordered_set 底层数据结构为hash表，无序，不重复
* unordered_multiset 底层数据结构为hash表，无序，可重复
* unordered_map 底层数据结构为hash表，无序，不重复
* unordered_multimap 底层数据结构为hash表，无序，可重复

支持随机访问的容器：string,array,vector,deque

支持在任意位置插入/删除的容器：list,forward_list

支持在尾部插入元素：vector,string,deque

#### 容器内部删除一个元素？

**顺序容器**（序列式容器，比如vector、deque）

erase迭代器不仅使所指向被删除的迭代器失效，而且使被删元素之后的所有迭代器失效(list除外)，所以不能使用erase(it++)的方式，但是erase的返回值是下一个有效迭代器：`It = c.erase(it)`

**关联容器**(关联式容器，比如map、set、multimap、multiset等)

erase迭代器只是被删除元素的迭代器失效，但是返回值是void，所以要采用erase(it++)的方式删除迭代器；`c.erase(it++)`

<https://zhuanlan.zhihu.com/p/450086692>

```cpp
bool badValue(int) { return true; } // 返回x是否为"坏值"
 
int test_item_9()
{
 // 第一种情况：删除 c 中所有值指定值 2021 的元素
 std::vector<int> c1;
 c1.erase(std::remove(c1.begin(), c1.end(), 2021), c1.end()); // 当c1是vector, string或deque时，erase-remove习惯用法是删除特定值的元素的最好办法
 
 std::list<int> c2;
 c2.remove(2021); // 当c2是list时，remove成员函数是删除特定值的元素的最好办法
 
 std::set<int> c3;
 c3.erase(2021); // 当c3是标准关联容器时，erase成员函数是删除特定值元素的最好办法
 
 // 第二种情况：删除判别式(predicate)返回 true 的每一个对象
 c1.erase(std::remove_if(c1.begin(), c1.end(), badValue), c1.end()); // 当c1是vector, string或deque时，这是删除使badValue返回true的对象的最好办法
 
 c2.remove_if(badValue); // 当c2是list时，这是删除使badValue返回true的对象的最好办法
 
 for (std::set<int>::iterator i = c3.begin(); i != c3.end();) {
  if (badValue(*i)) c3.erase(i++); // 对坏值，把当前的i传给erase，递增i是副作用
  else ++i;                        // 对好值，则简单的递增i
 }//当对关联式容器来说
 
 // 第三种情况，每次元素被删除时，还需要都向一个日志(log)文件中写一条信息
 std::ofstream logFile;
 for (std::set<int>::iterator i = c3.begin(); i != c3.end();) {
  if (badValue(*i)) {
   logFile << "Erasing " << *i << '\n'; // 写日志文件
   c3.erase(i++); // 对坏值，把当前的i传给erase，递增i是副作用
  }
  else ++i;              // 对好值，则简单第递增i
 }//当对关联式容器来说
 
 for (std::vector<int>::iterator i = c1.begin(); i != c1.end();) {
  if (badValue(*i)) {
   logFile << "Erasing " << *i << '\n';
   i = c1.erase(i); // 把erase的返回值赋给i，使i的值保持有效
  }
  else ++i;
 }//当对序列式容器来说
 
 return 0;
}
```

总结一下：

* 要删除容器中有特定值的所有对象：如果容器是 vector，string 或deque，则使用 erase-remove 习惯用法；如果容器是list，则使用 list::remove；如果容器是一个标准关联容器，则使用它的 erase [成员函数](https://www.zhihu.com/search?q=成员函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"450086692"})。
* 要删除容器中满足特定判别式(条件)的所有对象：如果容器是vector， string或deque，则使用[erase-remove_if](https://www.zhihu.com/search?q=erase-remove_if&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"450086692"})习惯用法；如果容器是list，则使用list::remove_if；如果容器是一个标准关联容器，则使用remove_copy_if和swap，或者写一个循环来遍历容器中的元素，记住当把迭代器传给erase时，要对它进行后缀递增。
* 要在循环内做某些(除了删除对象之外的)操作：如果容器是一个[标准序列容器](https://www.zhihu.com/search?q=标准序列容器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"450086692"})，则写一个循环来遍历容器中的元素，记住每次调用erase时，要用它的返回值更新迭代器；如果容器是一个标准关联容器，则写一个循环来遍历容器中的元素，记住当把迭代器传给erase时，要对[迭代器](https://www.zhihu.com/search?q=迭代器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"450086692"})做后缀递增。

#### 容器 size 和 capacity 区别，reserve 和 resize 区别

**capacity**：在不分配更多内存的情况下，容器可以保存的最多元素个数

**size**：vector 容器的大小，是它实际所包含的元素个数

**resize(n)**

* 调整容器的长度大小，使其能容纳n个元素。
  * 如果n小于容器的当前的size，则删除多出来的元素。
  * 否则，添加采用值初始化的元素。
* resize(n，t)：多一个参数t，将所有新添加的元素初始化为t。
* resize()函数和容器的size息息相关。调用resize(n)后，容器的size即为n。
* 至于是否影响capacity，取决于调整后的容器的size是否大于capacity。
* 容器调用resize()函数后，所有的空间都已经初始化了，所以可以直接访问
* resize()可以改变有效空间的大小，也有改变默认值的功能。capacity的大小也会随着改变。**resize()可以有多个参数。**
* resize是改变容器的大小，且在创建对象，因此，调用这个函数之后，就可以引用容器内的对象了，因此当加入新的元素时，用operator[]操作符，或者用迭代器来引用元素对象。此时再**调用push_back()函数，是加在这个新的空间后面的。**
* resize()在时间效率上是比reserve()低的。但是在多线程的场景下，用resize再合适不过

 **reserve(n)**

* 预分配n个元素的存储空间。
* reserve()函数和容器的capacity息息相关。
  * 调用reserve(n)后，若容器的capacity<n，则重新分配内存空间，从而使得capacity等于n。
  * 如果capacity>=n呢？capacity无变化。
* reserve()函数预分配出的空间没有被初始化，所以不可访问
* reserve是**直接扩充到已经确定的大小**，可以减少多次开辟、释放空间的问题（优化push_back），就可以提高效率，其次还可以减少多次要拷贝数据的问题。reserve只是保证vector中的空间大小（capacity）最少达到参数所指定的大小n。**reserve()只有一个参数。**
* reserve是容器预留空间，但在空间内不真正创建元素对象，所以在没有添加新的对象之前，不能引用容器内的元素。加入新的元素时，要调用push_back()/insert()函数。

#### STL 容器是线程安全的吗？

**1、线程安全的情况**
多个读取者是安全的。多线程可能同时读取一个容器的内容，这将正确地执行。当然，在读取时不能 有任何写入者操作这个容器；

对不同容器的多个写入者是安全的。多线程可以同时写不同的容器。

**2、线程不安全的情况**
在对同一个容器进行多线程的读写、写操作时；

在每次调用容器的成员函数期间都要锁定该容器；

在每个容器返回的迭代器（例如通过调用begin或end）的生存期之内都要锁定该容器；

在每个在容器上调用的算法执行期间锁定该容器。

### 仿函数

#### 谈谈仿函数

仿函数（functor）又称为函数对象（function object）是一个能行使函数功能的类。仿函数的语法几乎和我们普通的函数调用一样，不过作为仿函数的类，都必须重载operator()运算符，举个例子：

```cpp
class Func{
public:
    void operator() (const string& str) const {
        cout<<str<<endl;
    }
};

Func myFunc;
myFunc("helloworld!");

>>>helloworld!
```

仿函数既能想普通函数一样传入给定数量的参数，还能存储或者处理更多我们需要的有用信息。我们可以举个例子：

假设有一个vector<string>，你的任务是统计长度小于5的string的个数，如果使用count_if函数的话，你的代码可能长成这样：

```cpp
 bool LengthIsLessThanFive(const string& str) {
     return str.length()<5;    
 }
 int res=count_if(vec.begin(), vec.end(), LengthIsLessThanFive);
```

其中count_if函数的第三个参数是一个函数指针，返回一个bool类型的值。一般的，如果需要将特定的阈值长度也传入的话，我们可能将函数写成这样：

```cpp
 bool LenthIsLessThan(const string& str, int len) {
     return str.length()<len;
 }
```

这个函数看起来比前面一个版本更具有一般性，但是他不能满足count_if函数的参数要求：count_if要求的是unary function（仅带有一个参数）作为它的最后一个参数。如果我们使用仿函数，是不是就豁然开朗了呢：

```cpp
class ShorterThan {
 public:
     explicit ShorterThan(int maxLength) : length(maxLength) {}
     bool operator() (const string& str) const {
         return str.length() < length;
     }
 private:
     const int length;
 };
```

#### STL中仿函数有什么用，和函数指针有什么不同，哪个效率高

* 仿函数：在C++标准中采用的名称是函数对象（function objects）。对于重载了()操作符的类，可以实现类似函数调用的过程，所以叫做仿函数，实际上仿函数对象仅仅占用1字节，因为内部没有数据成员，仅仅是一个重载的方法而已。
* 函数指针：函数指针是指向函数的指针变量。在C编译时，每一个函数都有一个入口地址，那么这个指向这个函数的函数指针便指向这个地址。函数指针主要有两个作用：用作回调函数和做函数的参数。

在函数对象的方式中，内联inline有效，而作为函数指针时，一般编译器都不会内联函数指针指向的函数，即使指定了inline，使用函数对象一般是裸函数的1.5倍，最多能快2倍多

### 空间配置器

#### allocator 的作用

 new在内存分配上面有一些局限性，new的机制是将内存分配和对象构造组合在一起，同样的，delete也是将对象析构和内存释放组合在一起的。allocator将这两部分分开进行，allocator申请一部分内存，不进行初始化对象，只有当需要的时候才进行初始化操作。

#### STL的两级空间配置器

1、首先明白为什么需要二级空间配置器？

我们知道动态开辟内存时，要在堆上申请，但若是我们需要

频繁的在堆开辟释放内存，则就会**在堆上造成很多外部碎片**，浪费了内存空间；

每次都要进行调用**malloc、free**函数等操作，使空间就会增加一些附加信息，降低了空间利用率；

随着外部碎片增多，内存分配器在找不到合适内存情况下需要合并空闲块，浪费了时间，大大降低了效率。

于是就设置了二级空间配置器，**当开辟内存<=128bytes时，即视为开辟小块内存，则调用二级空间配置器。**

关于STL中一级空间配置器和二级空间配置器的选择上，一般默认**选择的为二级空间配置器**。 如果大于128字节再转去一级配置器器。

[一级配置器](https://interviewguide.cn/#/Doc/Knowledge/C++/STL模板库/STL模板库?id=一级配置器)

**一级空间配置器**中重要的函数就是allocate、deallocate、reallocate 。 一级空间配置器是以malloc()，free()，realloc()等C函数执行实际的内存配置 。大致过程是：

1、直接allocate分配内存，其实就是malloc来分配内存，成功则直接返回，失败就调用处理函数

2、如果用户自定义了内存分配失败的处理函数就调用，没有的话就返回异常

3、如果自定义了处理函数就进行处理，完事再继续分配试试

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage2@2.6/202104/c++-189-1.png)

[二级配置器](https://interviewguide.cn/#/Doc/Knowledge/C++/STL模板库/STL模板库?id=二级配置器)

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage2@2.6/202104/C++-189-2.png)

1、维护16条链表，分别是0-15号链表，最小8字节，以8字节逐渐递增，最大128字节，你传入一个字节参数，表示你需要多大的内存，会自动帮你校对到第几号链表（如需要13bytes空间，我们会给它分配16bytes大小），在找到第n个链表后查看链表是否为空，如果不为空直接从对应的free_list中拔出，将已经拨出的指针向后移动一位。

2、对应的free_list为空，先看其内存池是不是空时，如果内存池不为空： （1）先检验它剩余空间是否够20个节点大小（即所需内存大小(提升后) *20），若足够则直接从内存池中拿出20个节点大小空间，将其中一个分配给用户使用，另外19个当作自由链表中的区块挂在相应的free_list下，这样下次再有相同大小的内存需求时，可直接拨出。 （2）如果不够20个节点大小，则看它是否能满足1个节点大小，如果够的话则直接拿出一个分配给用户，然后从剩余的空间中分配尽可能多的节点挂在相应的free_list中。 （3）如果连一个节点内存都不能满足的话，则将内存池中剩余的空间挂在相应的free_list中（找到相应的free_list），然后再给内存池申请内存，转到3。 3、内存池为空，申请内存 此时二级空间配置器会使用malloc()从heap上申请内存，（一次所申请的内存大小为2* 所需节点内存大小（提升后）* 20 + 一段额外空间），申请40块，一半拿来用，一半放内存池中。 4、malloc没有成功 在第三种情况下，如果malloc()失败了，说明heap上没有足够空间分配给我们了，这时，二级空间配置器会从比所需节点空间大的free_list中一一搜索，从比它所需节点空间大的free_list中拔除一个节点来使用。如果这也没找到，说明比其大的free_list中都没有自由区块了，那就要调用一级适配器了。

释放时调用deallocate()函数，若释放的n>128，则调用一级空间配置器，否则就直接将内存块挂上自由链表的合适位置。

STL二级空间配置器虽然解决了外部碎片与提高了效率，但它同时增加了一些缺点：

1.因为自由链表的管理问题，它会把我们需求的内存块自动提升为8的倍数，这时若你需要1个字节，它会给你8个字节，即浪费了7个字节，所以它又引入了内部碎片的问题，若相似情况出现很多次，就会造成很多内部碎片；

2.二级空间配置器是在堆上申请大块的狭义内存池，然后用自由链表管理，供现在使用，在程序执行过程中，它将申请的内存一块一块都挂在自由链表上，即不会还给操作系统，并且它的实现中所有成员全是静态的，所以它申请的所有内存只有在进程结束才会释放内存，还给操作系统，由此带来的问题有：1.即我不断的开辟小块内存，最后整个堆上的空间都被挂在自由链表上，若我想开辟大块内存就会失败；2.若自由链表上挂很多内存块没有被使用，当前进程又占着内存不释放，这时别的进程在堆上申请不到空间，也不可以使用当前进程的空闲内存，由此就会引发多种问题。

[一级分配器](https://interviewguide.cn/#/Doc/Knowledge/C++/STL模板库/STL模板库?id=一级分配器)

GC4.9之后就没有第一级了，只有第二级

[二级分配器](https://interviewguide.cn/#/Doc/Knowledge/C++/STL模板库/STL模板库?id=二级分配器)

——default_alloc_template 剖析

有个自动调整的函数：你传入一个字节参数，表示你需要多大的内存，会自动帮你校对到第几号链表（0-15号链表，最小8字节 最大128字节）

allocate函数：如果要分配的内存大于128字节，就转用第一级分配器，否则也就是小于128字节。那么首先判断落在第几号链表，定位到了，先判断链表是不是空，如果是空就需要充值，（调节到8的倍数，默认一次申请20个区块，当然了也要判断20个是不是能够申请到，如果只申请到一个那就直接返回好了，不止一个的话，把第2到第n个挨个挂到当前链表上，第一个返回回去给容器用,n是不大于20的，当然了如果不在1-20之间，那就是内存碎片了，那就先把碎片挂到某一条链表上，然后再重新malloc了，malloc 2*20个块）去内存池去拿或者重新分配。不为空的话

对象构造前的空间配置和对象析构后的空间释放，由<stl_alloc.h>负责，SGI对此的设计哲学如下：

* 向system heap要求空间。
* 考虑多线程状态。
* 考虑内存不足时的应变措施。
* 考虑过多“小型区块”可能造成的内存碎片问题。

考虑小型区块造成的内存破碎问题，SGI设计了双层级配置器：

* 第一级直接使用allocate()调用malloc()、deallocate()调用free()，使用类似new_handler机制解决内存不足（抛出异常），配置无法满足的问题（如果在申请动态内存时找不到足够大的内存块，malloc 和new 将返回NULL 指针，宣告内存申请失败）。
* 第二级视情况使用不同的策略，当配置区块大于128bytes时，调用第一级配置器，当配置区块小于128bytes时，采用内存池的整理方式：配置器维护16个（128/8）自由链表，负责16种小型区块的此配置能力。内存池以malloc配置而得，如果内存不足转第一级配置器处理。

1、第一级配置器详解

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE1LmNuYmxvZ3MuY29tL2Jsb2cvMTA5MjE2NS8yMDE3MDIvMTA5MjE2NS0yMDE3MDIyODIzMDQyODExMC0xMDY1NDA4MTk5LnBuZw?x-oss-process=image/format,png)

2、第二级空间配置器详解
  第二级空间配置器实际上是一个内存池，维护了16个自由链表。自由链表是一个指针数组，有点类似与hash桶，它的数组大小为16，每个数组元素代表所挂的区块大小，比如free *list[0]代表下面挂的是8bytes的区块，free* list[1]代表下面挂的是16bytes的区块…….依次类推，直到free _ list[15]代表下面挂的是128bytes的区块。

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE1LmNuYmxvZ3MuY29tL2Jsb2cvODM1MjM0LzIwMTYwNi84MzUyMzQtMjAxNjA2MDMxOTAyNDI3MTEtNTIyMzI4MjU1LnBuZw?x-oss-process=image/format,png)

3、空间配置器存在的问题

* 自由链表所挂区块都是8的整数倍，因此当我们需要非8倍数的区块，往往会导致浪费。
* 由于配置器的所有方法，成员都是静态的，那么他们就是存放在静态区。释放时机就是程序结束，这样子会导致自由链表一直占用内存，自己进程可以用，其他进程却用不了。

#### STL中的allocator、deallocator

\1) 第一级配置器直接使用malloc()、free()和relloc()，第二级配置器视情况采用不同的策略：当配置区块超过128bytes时，视之为足够大，便调用第一级配置器；当配置器区块小于128bytes时，为了降低额外负担，使用复杂的内存池整理方式，而不再用一级配置器；

\2) 第二级配置器主动将任何小额区块的内存需求量上调至8的倍数，并维护16个free-list，各自管理大小为8~128bytes的小额区块；

\3) 空间配置函数allocate()，首先判断区块大小，大于128就直接调用第一级配置器，小于128时就检查对应的free-list。如果free-list之内有可用区块，就直接拿来用，如果没有可用区块，就将区块大小调整至8的倍数，然后调用refill()，为free-list重新分配空间；

\4) 空间释放函数deallocate()，该函数首先判断区块大小，大于128bytes时，直接调用一级配置器，小于128bytes就找到对应的free-list然后释放内存。

#### STL 内存池

### 迭代器

#### STL 每种容器的迭代器种类

| 容器                                   | 迭代器         |
| -------------------------------------- | -------------- |
| vector、deque                          | 随机访问迭代器 |
| stack、queue、priority_queue           | 无             |
| list、(multi)set/map                   | 双向迭代器     |
| unordered_(multi)set/map、forward_list | 前向迭代器     |

* 前向迭代器（forward iterator）

 则 p 支持 ++p，p++，*p 操作，还可以被复制或赋值，可以用 == 和 != 运算符进行比较。

* 双向迭代器（bidirectional iterator）

 双向迭代器具有正向迭代器的全部功能，除此之外，假设 p 是一个双向迭代器，则还可以进行 --p 或者 p-- 操作（即一次向后移动一个位置）。

* 随机访问迭代器（random access iterator）

 随机访问迭代器具有双向迭代器的全部功能。除此之外，假设 p 是一个随机访问迭代器，i 是一个整型变量或常量，则 p 还支持以下操作：

 1. p+=i：使得 p 往后移动 i 个元素。
 2. p-=i：使得 p 往前移动 i 个元素。
 3. p+i：返回 p 后面第 i 个元素的迭代器。
 4. p-i：返回 p 前面第 i 个元素的迭代器。
 5. p[i]：返回 p 后面第 i 个元素的引用。

* 输入迭代器 (input iterator)

 可用于读取容器中的元素，但是不保证能支持容器的写入操作。

 只支持自增运算

* 输出迭代器 (output iterator)

 可视为与输入迭代器功能互补的迭代器；

 输出迭代器可用于向容器写入元素，但是不保证能支持读取容器内容。

 只支持自增运算

#### STL迭代器如何实现

迭代器实际上是对“遍历容器”这一操作进行了封装。

在编程中我们往往会用到各种各样的容器，但由于这些容器的底层实现各不相同，所以对他们进行遍历的方法也是不同的。例如，数组使用指针算数就可以遍历，但链表就要在不同节点直接进行跳转。c++我觉得是一门非常讲究方便的语言，显然这种情况是不能够出现的。

因此就出现了迭代器，将遍历容器的操作封装起来，可以针对所有容器进行遍历。重载了，->, * , ++, --等操作符

1、 迭代器是一种抽象的设计理念，通过迭代器可以在不了解容器内部原理的情况下遍历容器，除此之外，STL中迭代器一个最重要的作用就是作为容器与STL算法的粘合剂。

2、 迭代器的作用就是提供一个遍历容器内部所有元素的接口，因此迭代器内部必须保存一个与容器相关联的指针，然后重载各种运算操作来遍历，其中最重要的是*运算符与->运算符，以及++、--等可能需要重载的运算符重载。这和C++中的智能指针很像，智能指针也是将一个指针封装，然后通过引用计数或是其他方法完成自动释放内存的功能。

3、最常用的迭代器的相应型别有五种：value type、difference type、pointer、reference、iterator catagoly;

通过迭代器可以在不了解容器内部原理的情况下遍历容器

它的底层实现包含两个重要的部分：萃取技术和模板偏特化。

* 萃取技术

 萃取技术可以进行类型推导，根据迭代器的不同类型处理不同流程。例如vector的迭代器类型为随机访问迭代器，list为双向迭代器

* 模板偏特化

 比较深澳

迭代器是连接容器和算法的一种重要桥梁，通过迭代器可以在不了解容器内部原理的情况下遍历容器。它的底层实现包含两个重要的部分：萃取技术和模板偏特化。

  萃取技术（traits）可以进行类型推导，根据不同类型可以执行不同的处理流程，比如容器是vector，那么traits必须推导出其迭代器类型为随机访问迭代器，而list则为双向迭代器。

  例如STL算法库中的distance函数，distance函数接受两个迭代器参数，然后计算他们两者之间的距离。显然对于不同的迭代器计算效率差别很大。比如对于vector容器来说，由于内存是连续分配的，因此指针直接相减即可获得两者的距离；而list容器是链式表，内存一般都不是连续分配，因此只能通过一级一级调用next()或其他函数，每调用一次再判断迭代器是否相等来计算距离。vector迭代器计算distance的效率为O(1),而list则为O(n),n为距离的大小。

  使用萃取技术（traits）进行类型推导的过程中会使用到模板偏特化。模板偏特化可以用来推导参数，如果我们自定义了多个类型，除非我们把这些自定义类型的特化版本写出来，否则我们只能判断他们是内置类型，并不能判断他们具体属于是个类型。

```c++
template <typename T>
struct TraitsHelper {
     static const bool isPointer = false;
};
template <typename T>
struct TraitsHelper<T*> {
     static const bool isPointer = true;
};

if (TraitsHelper<T>::isPointer)
     ...... // 可以得出当前类型int*为指针类型
else
     ...... // 可以得出当前类型int非指针类型
```

2、一个理解traits的例子

2、一个理解traits的例子

```c++
// 需要在T为int类型时，Compute方法的参数为int，返回类型也为int，
// 当T为float时，Compute方法的参数为float，返回类型为int
template <typename T>
class Test {
public:
     TraitsHelper<T>::ret_type Compute(TraitsHelper<T>::par_type d);
private:
     T mData;
};

template <typename T>
struct TraitsHelper {
     typedef T ret_type;
     typedef T par_type;
};

// 模板偏特化，处理int类型
template <>
struct TraitsHelper<int> {
     typedef int ret_type;
     typedef int par_type;
};

// 模板偏特化，处理float类型
template <>
struct TraitsHelper<float> {
     typedef float ret_type;
     typedef int par_type;
};
```

  当函数，类或者一些封装的通用算法中的某些部分会因为数据类型不同而导致处理或逻辑不同时，traits会是一种很好的解决方案。

  当函数，类或者一些封装的通用算法中的某些部分会因为数据类型不同而导致处理或逻辑不同时，traits会是一种很好的解决方案。

------------------------------------------------
版权声明：本文为CSDN博主「~青萍之末~」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：<https://blog.csdn.net/daaikuaichuan/article/details/80717222>

traits技法利用“内嵌型别“的编程技巧与**编译器的template参数推导功能**，增强C++未能提供的关于型别认证方面的能力。常用的有iterator_traits和type_traits。

**iterator_traits**

被称为**特性萃取机**，能够方便的让外界获取以下5种型别：

* value_type：迭代器所指对象的型别
* difference_type：两个迭代器之间的距离
* pointer：迭代器所指向的型别
* reference：迭代器所引用的型别
* iterator_category：三两句说不清楚，建议看书

**type_traits**

关注的是型别的**特性**，例如这个型别是否具备non-trivial defalt ctor（默认构造函数）、non-trivial copy ctor（拷贝构造函数）、non-trivial assignment operator（赋值运算符） 和non-trivial dtor（析构函数），如果答案是否定的，可以采取直接操作内存的方式提高效率，一般来说，type_traits支持以下5中类型的判断：

```plaintext
__type_traits<T>::has_trivial_default_constructor
__type_traits<T>::has_trivial_copy_constructor
__type_traits<T>::has_trivial_assignment_operator
__type_traits<T>::has_trivial_destructor
__type_traits<T>::is_POD_type
```

由于编译器只针对class object形式的参数进行参数推到，因此上式的返回结果不应该是个bool值，实际上使用的是一种空的结构体：

```plaintext
struct __true_type{};struct __false_type{};
```

这两个结构体没有任何成员，不会带来其他的负担，又能满足需求，可谓一举两得

当然，如果我们自行定义了一个Shape类型，也可以针对这个Shape设计type_traits的特化版本

```plaintext
template<> struct __type_traits<Shape>{
    typedef __true_type has_trivial_default_constructor;
    typedef __false_type has_trivial_copy_constructor;
    typedef __false_type has_trivial_assignment_operator;
    typedef __false_type has_trivial_destructor;
    typedef __false_type is_POD_type;
};
```

#### 迭代器失效的情况

**插入操作**
对于vector和string，如果容器内存被重新分配，iterators,pointers,references失效；如果没有重新分配，那么插入点之前的iterator有效，插入点之后的iterator失效；

对于deque，如果插入点位于除front和back的其它位置，iterators,pointers,references失效；当我们插入元素到front和back时，deque的迭代器失效，但reference和pointers有效；

对于list和forward_list，所有的iterator,pointer和refercnce有效。

**删除操作**
对于vector和string，删除点之前的iterators,pointers,references有效；off-the-end迭代器总是失效的；

对于deque，如果删除点位于除front和back的其它位置，iterators,pointers,references失效；当我们插入元素到front和back时，off-the-end失效，其他的iterators,pointers,references有效；

对于list和forward_list，所有的iterator,pointer和refercnce有效。

对于关联容器map来说，如果某一个元素已经被删除，那么其对应的迭代器就失效了，不应该再被使用，否则会导致程序无定义的行为。

以vector为例：

**插入元素：**

1、尾后插入：size < capacity时，首迭代器不失效尾迭代失效（未重新分配空间），size == capacity时，所有迭代器均失效（需要重新分配空间）。

2、中间插入：中间插入：size < capacity时，首迭代器不失效但插入元素之后所有迭代器失效，size == capacity时，所有迭代器均失效。

**删除元素：**

尾后删除：只有尾迭代失效。

中间删除：删除位置之后所有迭代失效。

deque 和 vector 的情况类似,

而list双向链表每一个节点内存不连续, 删除节点仅当前迭代器失效,erase返回下一个有效迭代器;

map/set等关联容器底层是红黑树删除节点不会影响其他节点的迭代器, 使用递增方法获取下一个迭代器 mmp.erase(iter++);

unordered_(hash) 迭代器意义不大, rehash之后, 迭代器应该也是全部失效.

> 迭代器失效的定义：对容器的操作影响了元素的存放位置，称为迭代器失效。
>
> 失效情况分为三种：
>
> 1. 当容器调用`erase()`方法后，当前位置到容器末尾元素的所有迭代器全部失效。
> 2. 当容器调用`insert()`方法后，当前位置到容器末尾元素的所有迭代器全部失效。
> 3. 如果容器扩容，在其他地方重新又开辟了一块内存。原来容器底层的内存上所保存的迭代器全都失效了。

**序列式容器失效**

**失效原因：**因为 vetor、deque 使用了连续分配的内存，`erase`操作删除一个元素导致后面所有的元素都会向前移动一个位置，这些元素的地址发生了变化，所以当前位置到容器末尾元素的所有迭代器全部失效。

**解决办法：**由于`erase`可以返回下一个有效的iterator，因此`q.earse(it)`不行，但是`it=q.erase(it)`可以

```c++
int main() {
 vector<int> q{ 1,2,3,4,5,6 };
 // 在这里想把大于2的元素都删除
 for (auto it = q.begin(); it != q.end(); it++) {
  if (*it > 2)
   q.erase(it); // 这里就会发生迭代器失效
 }
 // 打印结果
 for (auto it = q.begin(); it != q.end(); it++) {
  cout << *it << " ";
 }
 cout << endl;
 return 0;
}

//解决办法
for(auto it=q.begin();it!=q.end();)
{
    if(*it>2)
    {
     it=q.erase(it); // 这里会返回指向下一个元素的迭代器，因此不需要再自加了
    }
    else
    {
     it++;
    }
}
```

**链表式容器失效**和**关联式容器失效**

**失效原因：**链表的插入和删除节点不会对其他节点造成影响，因此只会使得当前的iterator失效

**解决办法：**利用`erase`可以返回下一个有效的iteratord的特性，或者直接iterator++

```c++
//方法1
for (iter = cont.begin(); it != cont.end();)
{
   (*iter)->doSomething();
   if (shouldDelete(*iter))
      cont.erase(iter++);
   else
      iter++;
}
//方法2
for (iter = cont.begin(); iter != cont.end();)
{
   (*it)->doSomething();
   if (shouldDelete(*iter))
      iter = cont.erase(iter);  //erase删除元素，返回下一个迭代器
   else
      ++iter;
}
```

#### ++it、it++哪个好，为什么？

```c++
// ++i实现代码为：
int& operator++() {
 *this += 1;
 return *this;
} 

//i++实现代码为：                 
int operator++(int) {
 int temp = *this;                   
    ++*this;                       
    return temp;                  
} 
```

* 返回值不同：前置返回一个引用，后置返回一个对象
* 效率不同：前置不会产生临时对象，后置必须产生临时对象，临时对象会导致效率降低
* 参数不同：**后缀递增多一个 int 参数单纯拿来做区分**
* 赋值顺序不同：++ i 是先加后赋值；i ++ 是先赋值后加
* i++ 不能作为左值，而++i 可以
* ++i和i++都是分两步完成的，两者都是非原子操作

### vector

#### vector 的实现？

vector底层是一个**动态数组**，包含三个迭代器，start和finish之间是已经被使用的空间范围，end_of_storage是整块连续空间包括备用空间的尾部。如图：

<img src="https://cdn.jsdelivr.net/gh/luogou/cloudimg/data/20210906132837.png" alt="在这里插入图片描述" style="zoom: 33%;" />

vector是一种序列式容器，其数据安排以及操作方式与array非常类似，两者的唯一差别就是对于空间运用的灵活性，众所周知，array占用的是静态空间，一旦配置了就不可以改变大小，如果遇到空间不足的情况还要自行创建更大的空间，并手动将数据拷贝到新的空间中，再把原来的空间释放。vector则使用灵活的动态空间配置，维护一块**连续的线性空间**，在空间不足时，可以自动扩展空间容纳新元素，做到按需供给。其在扩充空间的过程中仍然需要经历：**重新配置空间，移动数据，释放原空间**等操作。这里需要说明一下动态扩容的规则：以原大小的两倍配置另外一块较大的空间（或者旧长度+新增元素的个数），源码：

```plaintext
const size_type len  = old_size + max(old_size, n);
```

Vector扩容倍数与平台有关，在Win + VS 下是 1.5倍，在 Linux + GCC 下是 2 倍

测试代码：

```c++
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    //在Linux + GCC下
    vector<int> res(2,0);
    cout << res.capacity() <<endl; //2
    res.push_back(1);
    cout << res.capacity() <<endl;//4
    res.push_back(2);
    res.push_back(3);
    cout << res.capacity() <<endl;//8
    return 0;


    //在 win 10 + VS2019下
    vector<int> res(2,0);
    cout << res.capacity() <<endl; //2
    res.push_back(1);
    cout << res.capacity() <<endl;//3
    res.push_back(2);
    res.push_back(3);
    cout << res.capacity() <<endl;//6

}
```

运行上述代码，一开始配置了一块长度为2的空间，接下来插入一个数据，长度变为原来的两倍，为4，此时已占用的长度为3，再继续两个数据，此时长度变为8，可以清晰的看到空间的变化过程

需要注意的是，频繁对vector调用push_back()对性能是有影响的，这是因为每插入一个元素，如果空间够用的话还能直接插入，若空间不够用，则需要重新配置空间，移动数据，释放原空间等操作，对程序性能会造成一定的影响

#### vector 如何扩容？

<https://blog.csdn.net/qq_44918090/article/details/120583540>

##### **什么时候扩容**

不同编译器在vector的扩容策略上显然不太一致，在vector的size()(当前容器所用空间)等于capacity()(当前容器总空间)时会发生扩容。

* 开辟新空间
* 拷贝元素

* 释放旧空间

##### 如何避免扩容导致效率低

如果要避免扩容而导致程序效率过低问题，其实非常简单：**如果在插入之前，可以预估vector存储元素的个数，提前将底层容量开辟好即可。**如果插入之前进行reserve，只要空间给足，则插入时不会扩容，如果没有reserve，则会边插入边扩容，效率极其低下。

##### 为什么选择以倍数扩容

1. **以等长个数进行扩容**
    等长个数方式扩容，新空间大小就是将原空间大小扩增到capacity+K个空间(capacity为旧空间大小)。假设需要向vector中插入100个元素，K为10，那么就需要扩容10次；每次扩容都需要将旧空间元素搬移到新空间，第i次扩容拷贝的元素数量为：ki(第1次扩容，新空间大小为20，旧空间中有10个元素，需要搬移到新空间中；第2次扩容，新空间大小为30，旧空间中有20个元素，需要全部搬移到新空间中)，假设元素插入与元素搬移为1个单位操作，则n个元素push_back()的总操作次数为：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/9019c9bbbcc842dbad0aedbafa7dc73a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5qOu5piO5biu5aSn5LqO6buR6JmO5biu,size_15,color_FFFFFF,t_70,g_se,x_16)

2. **以倍数方式进行扩容**
假设有n个元素需要像vector插入，倍增因子为m,则完成n个元素像vector的push_back操作需要扩容log以m为低n的次方。比如：以二倍方式扩容，当向vector插入1000个元素，需要扩容log以2为底1000次方，就是扩容10次，第i次增容会把m的i次方个元素搬移到新空间，n次push_back的总操作次数为：

![在这里插入图片描述](https://img-blog.csdnimg.cn/02900243e1624319b16f5ec2e9c1c315.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5qOu5piO5biu5aSn5LqO6buR6JmO5biu,size_15,color_FFFFFF,t_70,g_se,x_16)

​ 可以看到以倍数的方式扩容比以等长个数的扩容方式效率高。

3. **为什么选择1.5倍或者2倍方式扩容，而不是3倍、4倍**
    扩容原理为：申请新空间，拷贝元素，释放旧空间，理想的分配方案是在第N次扩容时如果能复用之前N-1次释放的空间就太好了，如果按照2倍方式扩容，第i次扩容空间大小如下：$1,2,4,8,16,32......2^i$

  可以看到，每次扩容时，前面释放的空间都不能使用。

  比如：第4次扩容时，前2次空间已经释放，第3次空间还没有释放(开辟新空间、拷贝元素、释放旧空间)，即前面释放的空间只有$1 + 2 = 3$，假设第 3 次空间已经释放才只有$1+2+4=7$，而第四次需要 8 个空间，因此无法使用之前已释放的空间，但是按照小于2倍方式扩容，多次扩容之后就可以复用之前释放的空间了。如果倍数超过2倍(包含2倍)方式扩容会存在：

* 空间浪费可能会比较高，比如：扩容后申请了64个空间，但只存了33个元素，有接近一半的空间没有使用。
* 无法使用到前面已释放的内存。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5ccdeb863e3844938b7cc24eea990e91.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5qOu5piO5biu5aSn5LqO6buR6JmO5biu,size_14,color_FFFFFF,t_70,g_se,x_16)

**使用2倍（k=2）扩容机制扩容时，每次扩容后的新内存大小必定大于前面的总和。**
**而使用1.5倍（k=1.5)扩容时，在几次扩展以后，可以重用之前的内存空间了。**

因为STL标准并没有严格说明需要按何种方式进行扩容，因此不同的实现厂商都是按照自己的方式扩容的，即：linux下是按照2倍的方式扩容的，而vs下是按照1.5倍的方式扩容的。

**1.5倍扩容和2倍扩容的区别**

不同的的编译器实现方式不同,vs编译器每次是以1.5倍且向下取整的策略进行扩容，gcc编译器则是每次以2.0倍的策略进行扩容。

1. 扩容因子越大，需要分配的新内存空间越多，越耗时。空闲空间较多，内存利用率低。
2. 扩容因子越小，需要再分配的可能性就更高，多次扩容耗时。空闲空间较少，内存利用率高。

因此，小规模数组，添加元素不频繁的，建议使用扩容因子更小的。当数据规模越大，插入更频繁，大扩容因子更适合。

##### Windows和Linux的扩容底层原理

1.Windows扩容底层
Windows中堆管理系统会对释放的堆块进行合并,因此:vs下的vector扩容机制选择使用1.5倍的方式扩容,这样多次扩容之后,就可以使用之前已经释放的空间。

2.Linux的扩容底层

![在这里插入图片描述](https://img-blog.csdnimg.cn/4f12d834bac147ee9bcba58e5521a961.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5qOu5piO5biu5aSn5LqO6buR6JmO5biu,size_13,color_FFFFFF,t_70,g_se,x_16)

* linux下主要使用glibc的ptmalloc来进行用户空间申请的.如果malloc的空间小于128KB,其内部通过brk()来扩张,如果大于128KB且arena中没有足够的空间时,通过mmap将内存映射到进程地址空间.
* linux中引入伙伴系统为内核提供了一种用于分配一下连续的页而建立的一种高效的分配策略,对固定分区和动态分区方式的折中,固定分区存在内部碎片,动态分区存在外部碎片,而且动态分区回收时的合并以及分配时的切片是比较耗时的.
* 伙伴系统是将整个内存区域构建成基本大小basicSize的1倍、2倍、4倍、8倍、16倍等，即要求内存空间分区链均对应2的整次幂倍大小的空间，整齐划一，有规律的而不是乱糟糟的。
* 在分配和释放空间时，可以通过log2(request/basicSize)向上取整的哈希算法快速找到对应内存块。
* 通过伙伴系统管理空闲分区的了解，可以看到在伙伴系统中的每条空闲分区链中挂的都是2i的页面大小，通过哈希思想进行空间分配与合并，非常高效。估计可能是这个原因SGI-STL选择以2倍方式进行扩容。

##### 总结

![在这里插入图片描述](https://img-blog.csdnimg.cn/b092797cb7a54d8ea79d7b802da3a97d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5qOu5piO5biu5aSn5LqO6buR6JmO5biu,size_20,color_FFFFFF,t_70,g_se,x_16)

* **vector在push_back以成倍增长可以在均摊后达到O(1)的事件复杂度，相对于增长指定大小的O(n)时间复杂度更好。**
* **为了防止申请内存的浪费，现在使用较多的有2倍与1.5倍的增长方式，而1.5倍的增长方式可以更好的实现对内存的重复利用。**

#### vector的增加删除都是怎么做的？

**新增元素**

push_back：在 vector 容器的尾部添加一个元素

新增元素：vector通过一个连续的数组存放元素，如果集合已满，在新增数据的时候，就要分配一块更大的内存，将原来的数据复制过来，释放之前的内存，在插入新增的元素；对vector的任何操作，一旦引起空间重新配置，指向原vector的所有迭代器就都失效了

初始时刻vector的capacity为0，塞入第一个元素后capacity增加为1

insert：在 vector 容器的指定位置插入一个或多个元素

emplace_back：其功能和 push_back() 相同，都是在 vector 容器的尾部添加一个元素

emplace_back() 和 push_back() 的区别，就在于底层实现的机制不同。

* push_back() 向容器尾部添加元素时，首先会创建这个元素，然后再将这个元素拷贝或者移动到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）
* emplace_back() 在实现时，则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程

**删除元素**

pop_back：成员函数pop_back()可以删除最后一个元素

erase：

vector 中 erase的作用是删除掉某个位置position或一段区域（begin, end)中的元素，减少其size，**返回被删除元素下一个元素的位置**。

remove：

**通用算法**remove()来删除vector容器中的元素

vector中remove的作用是将范围内为val的值都remove到后面，**返回新的end()值（非val部分的end）**,**但传入的原vector的end并没有发生改变，因此size也就没有变化**

```c++
vec.erase(remove(vec.begin(),vec.end(),x),vec.end());
```

remove 和 erase 的区别

* erase和remove的区别在于执行函数之后返回值不同
* erase 后 size 会改变，remove 后 size 不会改变

#### vector如何释放空间?

由于 vector 的内存占用空间只增不减，比如你首先分配了10,000个字节，然后erase掉后面9,999个，留下一个有效元素，但是内存占用仍为10,000个。所有内存空间是在vector析构时候才能被系统回收。

empty()用来检测容器是否为空的，clear()可以清空所有元素（只是把 size = 0，不会改变 capacity）。但是即使clear()，vector所占用的内存空间依然如故，无法保证内存的回收。

如果需要空间动态缩小，可以考虑使用deque。如果vector，可以用swap()来帮助你释放内存。

```c++
vector(Vec).swap(Vec); //将Vec的内存清除； 
vector().swap(Vec); //清空Vec的内存；
```

* `vec.clear()`：清空内容，但是不释放内存。
* `vector<int>().swap(vec)`：清空内容，且释放内存，想得到一个全新的vector。
* `vec.shrink_to_fit()`：请求容器降低其capacity和size匹配。
* `vec.clear();vec.shrink_to_fit();`：清空内容，且释放内存。

#### `vector<bool>` 有什么问题？

具体来讲，不推荐使用 `vector<bool>` 的原因有以下 2 个：

* 严格意义上讲，`vector<bool>`并不是一个 STL 容器；
* `vector<bool>` 底层存储的并不是 bool 类型值。

值得一提的是，对于是否为 STL 容器，C++ 标准库中有明确的判断条件，其中一个条件是：如果 cont 是包含对象 T 的 STL 容器，且该容器中重载了 [ ] 运算符（即支持 operator[]），则以下代码必须能够被编译：

```c++
T *p = &cont[0];
```

此行代码的含义是，借助 `operator[]` 获取一个 `cont<T>` 容器中存储的 T 对象，同时将这个对象的地址赋予给一个 T 类型的指针。
这就意味着，如果 `vector<bool>` 是一个 STL 容器，则下面这段代码是可以通过编译的：

```c++
//创建一个 vector<bool> 容器
vector<bool>cont{0,1};
//试图将指针 p 指向 cont 容器中第一个元素
bool *p = &cont[0];
```

但不幸的是，此段代码不能通过编译。原因在于 `vector<bool>` 底层采用了独特的存储机制。

实际上，为了节省空间，`vector<bool>` 底层在存储各个 bool 类型值时，每个 bool 值都只使用一个比特位（二进制位）来存储。也就是说在 `vector<bool>` 底层，一个字节可以存储 8 个 bool 类型值。在这种存储机制的影响下，operator[ ] 势必就需要返回一个指向单个比特位的引用，但显然这样的引用是不存在的，等号左右两边出现冲突！

C++ 标准中解决这个问题的方案是，令 operator[] 返回一个代理对象（proxy object）。有关代理对象，由于不是本节重点，这里不再做描述，有兴趣的读者可自行查阅相关资料。

同样对于指针来说，其指向的最小单位是字节，无法另其指向单个比特位。综上所述可以得出一个结论，即上面第 2 行代码中，用 = 赋值号连接 bool *p 和 &cont[0] 是矛盾的。

由于`vector<bool>` 并不完全满足 C++ 标准中对容器的要求，所以严格意义上来说它并不是一个 STL 容器。可能有读者会问，既然 `vector<bool>` 不完全是一个容器，为什么还会出现在 C++ 标准中呢？

这和一个雄心勃勃的试验有关，还要从前面提到的代理对象开始说起。由于代理对象在 C++ 软件开发中很受欢迎，引起了 C++ 标准委员会的注意，他们决定以开发 `vector<bool>` 作为一个样例，来演示 STL 中的容器如何通过代理对象来存取元素，这样当用户想自己实现一个基于代理对象的容器时，就会有一个现成的参考模板。

然而开发人员在实现 `vector<bool>` 的过程中发现，既要创建一个基于代理对象的容器，同时还要求该容器满足 C++ 标准中对容器的所有要求，是不可能的。由于种种原因，这个试验最终失败了，但是他们所做过的尝试（即开发失败的 `vector<bool>`）遗留在了 C++ 标准中。

至于将 `vector<bool>` 遗留到 C++ 标准中，是无心之作，还是有意为之，这都无关紧要，重要的是让读者明白，`vector<bool>` 不完全满足 C++ 标准中对容器的要求，尽量避免在实际场景中使用它

 那么，如果在实际场景中需要使用 `vector<bool>` 这样的存储结构，该怎么办呢？很简单，可以选择使用 `deque<bool>` 或者 bitset 来替代 `vector<bool>`。

* 要知道，deque 容器几乎具有 vecotr 容器全部的功能（拥有的成员方法也仅差 reserve() 和 capacity()），而且更重要的是，deque 容器可以正常存储 bool 类型元素。
* 还可以考虑用 bitset 代替 `vector<bool>`，其本质是一个模板类，可以看做是一种类似数组的存储结构。和后者一样，bitset 只能用来存储 bool 类型值，且底层存储机制也采用的是用一个比特位来存储一个 bool 值。和 vector 容器不同的是，bitset 的大小在一开始就确定了，因此不支持插入和删除元素；另外 bitset 不是容器，所以不支持使用迭代器

#### `vector` 元素类型可以是引用吗，void 呢？

vector中的元素有两个要求：

1. 元素必须能赋值
2. 元素必须能复制

对于类类型来说，需要拷贝构造和赋值运算符支持，对于像map，set这种容器需要重载运算符<的支持，而引用是必须初始化的，指向一个特定对象，本质上是一个常量指针，因此引用是不能复制，意味着容器的拷贝复制就失效了

容器开辟的时候还没有值，无法初始化，你咋能用引用

**`vector<void>` 合理吗？没法通过编译**

#### 线程安全 vector 设计

众所周知，C++标准库中的vector不保证线程安全。当我们在并发读写vector时，往往会面临两方面的风险：

1. 内容读取异常：例如两个线程一个在读，一个在写，或者两个线程同时在写，都会导致单个数据内部出现不一致的情况。
2. vector扩容时，内存位置发生改变导致Segmentation fault错误。因为vector在扩容时会将内容全部拷贝到新的内存区域中，原有的内存区域被释放，此时如果有线程依然在向旧的内存区域读或写就会出问题。

通过固定vector的大小，避免动态扩容（无push_back）来做到lock-free！即在开始并发读写之前（比如初始化）的时候，给vector设置好大小。代码如下：

```c++
vector<int> v;
v.resize(1000);
```

注意是resize，不是reserve！

举一个简单的例子：

```cpp
vector<int> vec;
void add_vector(int range, unsigned int seed){
    srand(seed);
    for(int i = 0 ; i < range; i++){
        vec.push_back(rand());
    }
}
int main(){
    vec.reserve(100);
    thread t1 = thread(add_vector, 1000, 2);
    thread t2 = thread(add_vector, 1000, 1);

    t1.join();
    t2.join();
}
```

两个线程都在向vec中添加元素，如果没有任何处理，很容易崩溃，就是因为第二个原因。而这种并发写的情况，在很多业务场景中都是很可能出现的，例如：在推荐系统中，为了提高运算效率每个线程都按照不同的策略生产推荐召回，这些线程产生召回后就会向同一个数组中合并。然后根据这些召回中选出最好的推荐结果。

在文章中提出了三种向vector并发添加元素的方案，目的是保证多线程并发条件下能正确向vector中。项目放在了[safe_vector](https://link.segmentfault.com/?enc=2ml8QZTyk2bSInLiXfugIw%3D%3D.qAuCcyYJ1z8u5e7tQ%2FYH2LsjVjMaN9fnynhpzNoZ8HzZ249l4loQ8LxmNcU%2BjPd0)。

##### **多线程安全的vector设计---有锁的设计**

对于解决并发问题中的最简单的设计就是加锁。在这里我们使用标准库为我们提供的mutex来对push_back临界区加锁。

```reasonml
template<typename T>
class lock_vector{
    std::mutex mlock;
    vector<T> mvec;

public:
    T operator[](unsigned int idx){
        return mvec[idx];
    }
    lock_vector()=default;
    lock_vector(lock_vector<T>& vec){
         vec.getVector(mvec);
    };
    lock_vector(lock_vector<T>&& vec){
        vec.getVector(mvec);
    };

    void push_back(const T& value) noexcept{
        mlock.lock();
        mvec.push_back(value);
        mlock.unlock();
    }

    void getVector(vector<T> & res){
        res = mvec;
    }
};
```

##### **多线程安全的vector设计---无锁设计**

除了使用互斥锁，还可以通过无锁的设计来实现线程同步。其中一种常见的思路就是CAS(compare-and-swap)。C++的原子变量（atomic）就提供了compare_exchange_weak和compare_exchange_strong来实现CAS机制。下面的代码是基于CAS实现的多线程安全方案。

```cpp
template<typename T>
class cas_vector{
    std::atomic<bool> flag;
    vector<T> mvec;

    void lock(){
        bool expect = false;
        while(!flag.compare_exchange_weak(expect, true)){
            expect = false;
        }
    }

    void unlock(){
        flag.store(false);
    }

public:
    T operator[](unsigned int idx){
        return mvec[idx];
    }
    cas_vector(){
        flag.store(false);
    };
    cas_vector(const cas_vector& vec){
        mvec = vec;
        flag.store(false);
    };
    cas_vector(cas_vector&& vec){
        mvec = vec;
        flag.store(false);
    };

    void replace(const int idx, const T& value) noexcept{
        lock();
        mvec[idx] = value;
        unlock();
    }

    void push_back(const T& value) noexcept{
        lock();
        mvec.push_back(value);
        unlock();
    }
};
```

##### **多线程安全的vector设计---借助thread_local变量**

thread_local变量简介

thread_local是C++11之后才引入的关键字。thread_local变量与其所在线程同步创建和销毁，并且只能被创建它的线程所访问，也就是说thread_local变量是线程安全的。每个线程在自身TIB(Thread Information Block)中存储thread_local变量的副本。

基于thread_local变量实现的方案

该方案的代码实现如下，vector_thl就是多线程添加元素安全的类型。在我的实现中，每个类分别存在两个vector，一个是thread_local类型，每次调用push_back时，都会向其中添加元素。因为该变量在每个线程中都存在一个副本，因此不需要进行线程同步，但同时，为了获取最终结果，必须将这些分散在各个线程的副本合并到一起。因此在vector_thl增加了merge接口来合并这些线程局部的vector。

```cpp
template<typename T>
class vector_thl{
    vector<T> mvec;
    mutex lock;
public:

    thread_local static vector<T> vec;

    vector_thl()=default;
    vector_thl(const vector_thl& vec){
        mvec = vec;
        vec = vec;
    };
    vector_thl(vector_thl&& vec){
        mvec = vec;
        vec = vec;
    };


    void push_back(const T& value) noexcept{
        vec.push_back(value);
    }

    void merge(){
        mvec.insert(mvec.end(), vec.begin(), vec.end());
    }

    void getVector(vector<T>& res){
        res = mvec;
    }
};

template<typename T>
thread_local vector<T> vector_thl<T>::vec(0);
```

##### 性能比较

对三种实现方案进行基准测试，得到以下结果：

```asciidoc
Run on (12 X 2994.27 MHz CPU s)
CPU Caches:
  L1 Data 32 KiB (x6)
  L1 Instruction 32 KiB (x6)
  L2 Unified 512 KiB (x6)
  L3 Unified 4096 KiB (x1)
Load Average: 0.00, 0.09, 0.22
--------------------------------------------------------
Benchmark              Time             CPU   Iterations
--------------------------------------------------------
BM_VEC_LOC/10    4574464 ns      1072230 ns          639
BM_VEC_LOC/2     6627843 ns       176688 ns         1000
BM_VEC_CAS/10    9280705 ns      1027921 ns          683
BM_VEC_CAS/2     7537580 ns       180541 ns         1000
BM_VEC_THL/10    1108654 ns       993997 ns          687
BM_VEC_THL/2      693148 ns       123333 ns         5723
```

可见借助thread_local实现的方案是运行效率最高的，大概是互斥方案的4倍，是无锁方案的8倍。同时也是CPU利用效率最高的方案。

##### 总结

在文章中我们实现了三种多线程并发添加元素安全的vector。其中利用thread_local实现的方案效率最高，主要原因是thread_local变量在每个线程中都有一个副本，不会并发写，避免了锁竞争。

然而这种方案并非完美，由于每个线程的thread_local变量都是不完整的，因此在添加元素阶段并不能正确的读取元素，只有在每个添加元素的线程都讲元素合并到之后才能进行读。

#### vector 作为函数返回值

1. 在C++11中提供了RVO/NRVO机制可以防止这种重复拷贝开销。另一种是RVO/NRVO机制实现复制消除机制。
2. RVO机制使用父栈帧（或任意内存块）来分配返回值的空间，来避免对返回值的复制。也就是将Base fun();改为void fun(Base &x);

作者：spiritsaway
链接：<https://www.zhihu.com/question/27000013/answer/34846612>
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

根据effective modern c++中介绍，编译器进行RVO条件有二

1. return 的值类型与 [函数签名](https://www.zhihu.com/search?q=函数签名&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"34846612"})的返回值类型相同
2. return的是一个局部对象

现在我们来考虑下面这个语句

```cpp
return std::move(w)
```

此时返回的并不是一个局部对象，而是局部对象的[右值引用](https://www.zhihu.com/search?q=右值引用&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"34846612"})。编译器此时无法进行rvo优化，能做的只有根据std::move(w)来移动构造一个临时对象，然后再将该临时对象赋值到最后的目标。所以，不要试图去返回一个局部对象的右值引用。

下面来谈一下右值引用与函数之间的关系。

第一个例子：

```cpp
std::vector<int> return_vector(void)
{
    std::vector<int> tmp {1,2,3,4,5};
    return tmp;
}
std::vector<int> &&rval_ref = return_vector();
```

此时，并不调用RVO，拷贝构造临时对象，同时临时对象的生命周期延长至与rval_ref相同，等价于下面的代码

```cpp
const std::vector<int>& rval_ref = return_vector();
```

第二个例子：

```cpp
std::vector<int>&& return_vector(void)
{
    std::vector<int> tmp {1,2,3,4,5};
    return std::move(tmp);
}

std::vector<int> &&rval_ref = return_vector();
```

该代码会造成一个运行时错误，因为rval_ref最终指向被析构了的tmp 。类似于返回了内部对象的[左值引用](https://www.zhihu.com/search?q=左值引用&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"34846612"})。

第三个例子：

```cpp
std::vector<int> return_vector(void)
{
    std::vector<int> tmp {1,2,3,4,5};
    return std::move(tmp);
}
std::vector<int> &&rval_ref = return_vector();
```

该例子类似于第一个例子，只不过临时对象的构造是由右值移动构造的。

最好的例子：

```cpp
std::vector<int> return_vector(void)
{
    std::vector<int> tmp {1,2,3,4,5};
    return tmp;
}
std::vector<int> rval_ref = return_vector();
```

该代码会调用RVO，不生成临时对象 ，[返朴归真](https://www.zhihu.com/search?q=返朴归真&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A"34846612"})了。

#### emplace_back和push_back

emplace_back() 和 push_back() 的区别，就在于底层实现的机制不同。

**push_back**

使用push_back()向容器中加入一个右值元素（临时对象）的时候

1. 首先会调用构造函数构造这个临时对象
2. 然后需要调用移动构造函数或拷贝构造函数将这个临时对象放入容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素。当拷贝构造函数和移动构造函数同时存在时，会优先调用移动构造函数。）
3. 原来的临时变量释放。这样造成的问题是临时变量申请的资源就浪费

**emplace_back**

```c++
template <class... Args>
void emplace_back (Args&&... args);
```

emplace_back() 在实现时，则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程

#### 一个vetor内存很大但实际我只用了很小一部分怎么解决

swap

#### 函数里的vector存在堆上还是栈上，为什么？

无论你的定义是：
`vector<int*> *p = new vector<int*>`;
还是
`vector<int*> p`
其元素都是在堆上进行分配。

C++语言中，所有`new`和`malloc`创建的变量均存放在堆区，这已经是一个共识。但是鲜为人知的是，STL库中的容器虽没有经过这两个关键字创建，但同样是存放在堆区。这与动态数组性质相同。如果从汇编角度观察便会发现，容器均调用了`allocator`来创建。

vector就是在堆上的，底层由allocator去维护，所以函数退出时，普通定义的vector也可以由allocator自动回收

std::vector的默认实现是把内部数据分配在堆上，所以vector对象本身不需要再用new。vector对象本身不大

实际上，不论你怎么对vector进行push_back()，sizeof(vector)的值永远都不会变，变的只是vector的size() 因为在c++中，一个变量的类型，就表明了这个变量在内存中占用字节的大小，只要变量类型不变，sizeof()就不会变，而变量的类型是不允许改变的，因此vector自身是不能改变自身的大小的 vector只是一个实现了动态内存管理内存的类，它通过构造函数在堆上创建真正用于储存数据的对象并通过析构函数在堆上销毁储存数据的对象，那么你对vector进行push_back()的时候，都是在对这个储存数据的对象进行修改 因此你根本就不必用new来申请内存，可以理解为vector内部已经这么做了(事实上vector使用的是allocator 来申请内存的)，它只是做了一个简单包装，让你用起来更方便而已

### map/set

#### map 中[] 与 find 的区别？

* map的下标运算符[]的作用是：将关键码作为下标去执行查找，并返回对应的值；**如果不存在这个关键码，就将一个具有该关键码和值类型的默认值的项插入这个map**。

* map的find函数：用关键码执行查找，找到了返回该位置的迭代器；如果不存在这个关键码，就返回尾迭代器。

#### map 插入方式有几种？

```c++
// 用insert函数插入pair数据
mapStudent.insert(pair<int, string>(1, "student_one")); 
// 用insert函数插入value_type数据
mapStudent.insert(map<int, string>::value_type (1, "student_one"));
//  在insert函数中使用make_pair()函数
mapStudent.insert(make_pair(1, "student_one"));
//  用数组方式插入数据
mapStudent[1] = "student_one"; 
```

#### STL中unordered_map和map的区别和应用场景

map支持键值的自动排序，底层机制是红黑树，红黑树的查询和维护时间复杂度均为O(logn)，但是空间占用比较大，因为每个节点要保持父节点、孩子节点及颜色的信息

unordered_map是C++ 11新添加的容器，底层机制是哈希表，通过hash函数计算元素位置，其查询时间复杂度为O(1)，维护时间与bucket桶所维护的list长度有关，但是建立hash表耗时较大

从两者的底层机制和特点可以看出：map适用于有序数据的应用场景，unordered_map适用于高效查询的应用场景

#### （multi）map/set是怎么实现的

map 、set、multiset、multimap的底层实现都是红黑树，epoll模型的底层数据结构也是红黑树，linux系统中CFS进程调度算法，也用到红黑树。

红黑树的特性：

1. 每个结点或是红色或是黑色；

2. 根结点是黑色；

3. 每个叶结点是黑的；

4. 如果一个结点是红的，则它的两个儿子均是黑色；

5. 每个结点到其子孙结点的所有路径上包含相同数目的黑色结点。

![在这里插入图片描述](https://img-blog.csdn.net/20180622213441557?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhYWlrdWFpY2h1YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  对于STL里的map容器，count方法与find方法，都可以用来判断一个key是否出现，mp.count(key) > 0统计的是key出现的次数，因此只能为0/1，而mp.find(key) != mp.end()则表示key存在。

**特点**

map和set的增删改查速度为都是logn，是比较高效的。

* set和multiset会根据特定的排序准则自动将元素排序，set中元素不允许重复，multiset可以重复。
* set的特性是，所有元素都会根据元素的值自动被排序（默认升序），set元素的键值就是实值，实值就是键值，set不允许有两个相同的键值
* **set不允许迭代器修改元素的值，其迭代器是一种constance iterators**

* map和multimap将key和value组成的pair作为元素，根据key的排序准则自动将元素排序（因为红黑树也是二叉搜索树，所以map默认是按key排序的），map中元素的key不允许重复，multimap可以重复。
* map的特性是所有元素会根据键值进行自动排序。map中所有的元素都是pair，拥有键值(key)和实值(value)两个部分，并且不允许元素有相同的key
* 一旦map的key确定了，那么是无法修改的，但是可以修改这个key对应的value，**因此map的迭代器既不是constant iterator，也不是mutable iterator**

**如何同时用 红黑树分别实现 map 和 set**

在这里我们定义了一个模版参数，如果它是key那么它就是set，如果它是map，那么它就是map；底层是红黑树，实现map的红黑树的节点数据类型是key+value，而实现set的节点数据类型是value

**为不用二叉搜索树：**

高度越小越好，BST这种有特殊情况，比如只有左子树有值，导致O(n)复杂度

**为什么不用 AVL 树：**

对于STL中的set和map来说，需要进行频繁的插入和删除，而AVL这种严格平衡二叉树，插入删除太频繁会导致左旋右旋操作频繁，影响性能，AVL只适合查找较多但插入、删除不多的操作。而红黑树也是一种平衡二叉树，但只要求最长路径不超过最短路径的两倍，因此，更适合插入、删除操作较多的结构。

#### set和map的区别，multimap和multiset的区别

set只提供一种数据类型的接口，但是会将这一个元素分配到key和value上，而且它的compare_function用的是 identity()函数，这个函数是输入什么输出什么，这样就实现了set机制，set的key和value其实是一样的了。其实他保存的是两份元素，而不是只保存一份元素

map则提供两种数据类型的接口，分别放在key和value的位置上，他的比较function采用的是红黑树的comparefunction（），保存的确实是两份元素。

他们两个的insert都是采用红黑树的insert_unique() 独一无二的插入 。

multimap和map的唯一区别就是：multimap调用的是红黑树的insert_equal(),可以重复插入而map调用的则是独一无二的插入insert_unique()，multiset和set也一样，底层实现都是一样的，只是在插入的时候调用的方法不一样。

#### 为何map和set的插入删除效率比其他序列容器高，而且每次insert之后，以前保存的iterator不会失效？

用了红黑树数据结构

因为存储的是结点，不需要内存拷贝和内存移动。

因为插入操作只是结点指针换来换去，结点内存没有改变。而iterator就像指向结点的指针，内存没变，指向内存的指针也不会变。

#### 为何map和set不能像vector一样有个reserve函数来预分配数据?

因为在map和set内部存储的已经不是元素本身了，而是包含元素的结点。也就是说map内部使用的Alloc并不是map<Key, Data, Compare, Alloc>声明的时候从参数中传入的Alloc。

### unordered_map

#### STL中hash_map扩容发生什么？

hash table表格内的元素称为桶（bucket),而由桶所链接的元素称为节点（node),其中存入桶元素的容器为stl本身很重要的一种序列式容器——vector容器。之所以选择vector为存放桶元素的基础容器，主要是因为vector容器本身具有动态扩容能力，无需人工干预。

 向前操作：首先尝试从目前所指的节点出发，前进一个位置（节点），由于节点被安置于list内，所以利用节点的next指针即可轻易完成前进操作，如果目前正巧是list的尾端，就跳至下一个bucket身上，那正是指向下一个list的头部节点。

#### STL中unordered_map和map的区别，hash_map如何解决冲突以及扩容

**区别**

* unordered_map和map类似，都是存储的key-value的值，可以通过key快速索引到value。不同的是unordered_map不会根据key的大小进行排序，
* 存储时是根据key的hash值判断元素是否相同，即unordered_map内部元素是无序的，而map中的元素是按照二叉搜索树存储，进行中序遍历会得到有序遍历。
* 所以使用时map的key需要定义operator<。而unordered_map需要定义hash_value函数并且重载operator==。但是很多系统内置的数据类型都自带这些，
* 那么如果是自定义类型，那么就需要自己重载operator<或者hash_value()了。
* 如果需要内部元素自动排序，使用map，不需要排序使用unordered_map
* unordered_map的底层实现是hash_table;
* hash_map底层使用的是hash_table，而hash_table使用的开链法进行冲突避免，所有hash_map采用开链法进行冲突解决。

**什么时候扩容：**当向容器添加元素的时候，会判断当前容器的元素个数，如果大于等于阈值---即当前数组的长度乘以加载因子的值的时候，就要自动扩容啦。

**扩容(resize)**就是重新计算容量，向HashMap对象里不停的添加元素，而HashMap对象内部的数组无法装载更多的元素时，对象就需要扩大数组的长度，以便能装入更多的元素。

#### unordered_map 的实现

STL中的hashtable使用的是**开链法**解决hash冲突问题，如下图所示。

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1566639786045.png)

hashtable中的bucket所维护的list既不是list也不是slist，而是其自己定义的由hashtable_node数据结构组成的linked-list，而bucket聚合体本身使用vector进行存储。hashtable的迭代器只提供前进操作，不提供后退操作

在hashtable设计bucket的数量上，其内置了28个质数[53, 97, 193,...,429496729]，在创建hashtable时，会根据存入的元素个数选择大于等于元素个数的质数作为hashtable的容量（vector的长度），其中每个bucket所维护的linked-list长度也等于hashtable的容量。如果插入hashtable的元素个数超过了bucket的容量，就要进行重建table操作，即找出下一个质数，创建新的buckets vector，重新计算元素在新hashtable的位置。

有一条规则是：**当元素的个数大于buckets的空间个数时，就会把buckets成2倍扩增，然后再调整为质数。这样重新计算后可以很好地把长链变短。**

#### hashtable 解决冲突有哪些方法

**线性探测**

使用hash函数计算出的位置如果已经有元素占用了，则向后依次寻找，找到表尾则回到表头，直到找到一个空位

**开链**

每个表格维护一个list，如果hash函数计算出的格子相同，则按顺序存在这个list中

**再散列**

发生冲突时使用另一种hash函数再计算一个地址，直到不冲突

**二次探测**

使用hash函数计算出的位置如果已经有元素占用了，按照12、22、32...的步长依次寻找，如果步长是随机数序列，则称之为伪随机探测

**公共溢出区**

一旦hash函数计算的结果相同，就放入公共溢出区

#### 如何优化哈希表

### priority_queue

#### priority_queue 的实现？

heap（堆）并不是STL的容器组件，是priority queue（优先队列）的底层实现机制，因为binary max heap（大根堆）总是最大值位于堆的根部，优先级最高。

binary heap本质是一种complete binary tree（完全二叉树），整棵binary tree除了最底层的叶节点之外，都是填满的，但是叶节点从左到右不会出现空隙，如下图所示就是一颗完全二叉树

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1566039990260.png)

完全二叉树内没有任何节点漏洞，是非常紧凑的，这样的一个好处是可以使用array来存储所有的节点，因为当其中某个节点位于i处，其左节点必定位于2i处，右节点位于2i+1处，父节点位于i/2（向下取整）处。这种以array表示tree的方式称为隐式表述法。

因此我们可以使用一个array和一组heap算法来实现max heap（每个节点的值大于等于其子节点的值）和min heap（每个节点的值小于等于其子节点的值）。由于array不能动态的改变空间大小，用vector代替array是一个不错的选择。

那heap算法有哪些？常见有的插入、弹出、排序和构造算法，下面一一进行描述。

**push_heap插入算法**

由于完全二叉树的性质，新插入的元素一定是位于树的最底层作为叶子节点，并填补由左至右的第一个空格。事实上，在刚执行插入操作时，新元素位于底层vector的end()处，之后是一个称为percolate up（上溯）的过程，举个例子如下图：

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1566040870063.png)

新元素50在插入堆中后，先放在vector的end()存着，之后执行上溯过程，调整其根结点的位置，以便满足max heap的性质，如果了解大根堆的话，这个原理跟大根堆的调整过程是一样的。

**pop_heap算法**

heap的pop操作实际弹出的是根节点吗，但在heap内部执行pop_heap时，只是将其移动到vector的最后位置，然后再为这个被挤走的元素找到一个合适的安放位置，使整颗树满足完全二叉树的条件。这个被挤掉的元素首先会与根结点的两个子节点比较，并与较大的子节点更换位置，如此一直往下，直到这个被挤掉的元素大于左右两个子节点，或者下放到叶节点为止，这个过程称为percolate down（下溯）。举个例子：

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102//1566041421056.png)

根节点68被pop之后，移到了vector的最底部，将24挤出，24被迫从根节点开始与其子节点进行比较，直到找到合适的位置安身，需要注意的是pop之后元素并没有被移走，如果要将其移走，可以使用pop_back()。

**sort算法**

一言以蔽之，因为pop_heap可以将当前heap中的最大值置于底层容器vector的末尾，heap范围减1，那么不断的执行pop_heap直到树为空，即可得到一个递增序列。

**make_heap算法**

将一段数据转化为heap，一个一个数据插入，调用上面说的两种percolate算法即可。

代码实测：

```c++
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

int main()
{
    vector<int> v = { 0,1,2,3,4,5,6 };
    make_heap(v.begin(), v.end()); //以vector为底层容器
    for (auto i : v)
    {
        cout << i << " "; // 6 4 5 3 1 0 2
    }
    cout << endl;
    v.push_back(7);
    push_heap(v.begin(), v.end());
    for (auto i : v)
    {
        cout << i << " "; // 7 6 5 4 1 0 2 3
    }
    cout << endl;
    pop_heap(v.begin(), v.end());
    cout << v.back() << endl; // 7 
    v.pop_back();
    for (auto i : v)
    {
        cout << i << " "; // 6 4 5 3 1 0 2
    }
    cout << endl;
    sort_heap(v.begin(), v.end());
    for (auto i : v)
    {
        cout << i << " "; // 0 1 2 3 4 5 6
    }
    return 0;
}
```

priority_queue，优先队列，是一个拥有权值观念的queue，它跟queue一样是顶部入口，底部出口，在插入元素时，元素并非按照插入次序排列，它会自动根据权值（通常是元素的实值）排列，权值最高，排在最前面，如下图所示。

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1566126001158.png)

默认情况下，priority_queue使用一个max-heap完成，底层容器使用的是一般为vector为底层容器，堆heap为处理规则来管理底层容器实现 。priority_queue的这种实现机制导致其不被归为容器，而是一种容器配接器。关键的源码如下：

```c++
template <class T, class Squence = vector<T>, 
class Compare = less<typename Sequence::value_tyoe> >
class priority_queue{
    ...
protected:
    Sequence c; // 底层容器
    Compare comp; // 元素大小比较标准
public:
    bool empty() const {return c.empty();}
    size_type size() const {return c.size();}
    const_reference top() const {return c.front()}
    void push(const value_type& x)
    {
        c.push_heap(x);
        push_heap(c.begin(), c.end(),comp);
    }
    void pop()
    {
        pop_heap(c.begin(), c.end(),comp);
        c.pop_back();
    }
};
```

priority_queue的所有元素，进出都有一定的规则，只有queue顶端的元素（权值最高者），才有机会被外界取用，它没有遍历功能，也不提供迭代器

举个例子：

```c++
#include <queue>
#include <iostream>
using namespace std;

int main()
{
    int ia[9] = {0,4,1,2,3,6,5,8,7 };
    priority_queue<int> pq(ia, ia + 9);
    cout << pq.size() <<endl;  // 9
    for(int i = 0; i < pq.size(); i++)
    {
        cout << pq.top() << " "; // 8 8 8 8 8 8 8 8 8
    }
    cout << endl;
    while (!pq.empty())
    {
        cout << pq.top() << ' ';// 8 7 6 5 4 3 2 1 0
        pq.pop();
    }
    return 0;
}
```

### list/stack/queue/forward_list

#### list的实现

相比于vector的连续线型空间，list显得复杂许多，但是它的好处在于插入或删除都只作用于一个元素空间，因此list对空间的运用是十分精准的，对任何位置元素的插入和删除都是常数时间。list不能保证节点在存储空间中连续存储，也拥有迭代器，迭代器的“++”、“--”操作对于的是指针的操作，list提供的迭代器类型是双向迭代器：Bidirectional iterators。

list节点的结构见如下源码：

```plaintext
template <class T>
struct __list_node{
    typedef void* void_pointer;
    void_pointer prev;
    void_pointer next;
    T data;
}
```

从源码可看出list显然是一个双向链表。list与vector的另一个区别是，在插入和接合操作之后，都不会造成原迭代器失效，而vector可能因为空间重新配置导致迭代器失效。

此外list也是一个环形链表，因此只要一个指针便能完整表现整个链表。list中node节点指针始终指向尾端的一个空白节点，因此是一种“前闭后开”的区间结构

list的空间管理默认采用alloc作为空间配置器，为了方便的以节点大小为配置单位，还定义一个list_node_allocator函数可一次性配置多个节点空间

由于list的双向特性，其支持在头部（front)和尾部（back)两个方向进行push和pop操作，当然还支持erase，splice，sort，merge，reverse，sort等操作，这里不再详细阐述。

 list的底层是一个**双向链表**，以结点为单位存放数据，结点的地址在内存中不一定连续，每次插入或删除一个元素，就配置或释放一个元素空间。

  list不支持随机存取，**如果需要大量的插入和删除**，而不关心随即存取

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE1LmNuYmxvZ3MuY29tL2Jsb2cvNzc5MzY4LzIwMTYxMC83NzkzNjgtMjAxNjEwMjQxNDI2NTU4NTktMTUyMjI3NzQ5Ny5wbmc?x-oss-process=image/format,png)

#### STL中stack和queue的实现

**stack**

stack（栈）是一种先进后出（First In Last Out）的数据结构，只有一个入口和出口，那就是栈顶，除了获取栈顶元素外，没有其他方法可以获取到内部的其他元素，其结构图如下：

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1565957994483.png)

stack这种单向开口的数据结构很容易由**双向开口的deque和list**形成，只需要根据stack的性质对应移除某些接口即可实现，stack的源码如下：

```plaintext
template <class T, class Sequence = deque<T> >
class stack
{
    ...
protected:
    Sequence c;
public:
    bool empty(){return c.empty();}
    size_type size() const{return c.size();}
    reference top() const {return c.back();}
    const_reference top() const{return c.back();}
    void push(const value_type& x){c.push_back(x);}
    void pop(){c.pop_back();}
};
```

从stack的数据结构可以看出，其所有操作都是围绕Sequence完成，而Sequence默认是deque数据结构。stack这种“修改某种接口，形成另一种风貌”的行为，成为adapter(配接器)。常将其归类为container adapter而非container

stack除了默认使用deque作为其底层容器之外，也可以使用双向开口的list，只需要在初始化stack时，将list作为第二个参数即可。由于stack只能操作顶端的元素，因此其内部元素无法被访问，也不提供迭代器。

**queue**

queue（队列）是一种先进先出（First In First Out）的数据结构，只有一个入口和一个出口，分别位于最底端和最顶端，出口元素外，没有其他方法可以获取到内部的其他元素，其结构图如下：

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1565958318457.png)

类似的，queue这种“先进先出”的数据结构很容易由双向开口的deque和list形成，只需要根据queue的性质对应移除某些接口即可实现，queue的源码如下：

```plaintext
template <class T, class Sequence = deque<T> >
class queue
{
    ...
protected:
    Sequence c;
public:
    bool empty(){return c.empty();}
    size_type size() const{return c.size();}
    reference front() const {return c.front();}
    const_reference front() const{return c.front();}
    void push(const value_type& x){c.push_back(x);}
    void pop(){c.pop_front();}
};
```

从queue的数据结构可以看出，其所有操作都也都是是围绕Sequence完成，Sequence默认也是deque数据结构。queue也是一类container adapter。

同样，queue也可以使用list作为底层容器，不具有遍历功能，没有迭代器。

#### forward_list  的实现

list是双向链表，而slist（single linked list）是单向链表，它们的主要区别在于：前者的迭代器是双向的Bidirectional iterator，后者的迭代器属于单向的Forward iterator。虽然slist的很多功能不如list灵活，但是其所耗用的空间更小，操作更快。

根据STL的习惯，插入操作会将新元素插入到指定位置之前，而非之后，然而slist是不能回头的，只能往后走，因此在slist的其他位置插入或者移除元素是十分不明智的，但是在slist开头却是可取的，slist特别提供了insert_after()和erase_after供灵活应用。考虑到效率问题，slist只提供push_front()操作，元素插入到slist后，存储的次序和输入的次序是相反的

slist的单向迭代器如下图所示：

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1566227016872.png)

slist默认采用alloc空间配置器配置节点的空间，其数据结构主要代码如下

```c++
template <class T, class Allco = alloc>
class slist
{
    ...
private:
    ...
    static list_node* create_node(const value_type& x){}//配置空间、构造元素
    static void destroy_node(list_node* node){}//析构函数、释放空间
private:
    list_node_base head; //头部
public:
    iterator begin(){}
    iterator end(){}
    size_type size(){}
    bool empty(){}
    void swap(slist& L){}//交换两个slist，只需要换head即可
    reference front(){} //取头部元素
    void push_front(const value& x){}//头部插入元素
    void pop_front(){}//从头部取走元素
    ...
}
```

举个例子：

```c++
#include <forward_list>
#include <algorithm>
#include <iostream>
using namespace std;

int main()
{
    forward_list<int> fl;
    fl.push_front(1);
    fl.push_front(3);
    fl.push_front(2);
    fl.push_front(6);
    fl.push_front(5);

    forward_list<int>::iterator ite1 = fl.begin();
    forward_list<int>::iterator ite2 = fl.end();
    for(;ite1 != ite2; ++ite1)
    {
        cout << *ite1 <<" "; // 5 6 2 3 1
    }
    cout << endl;

    ite1 = find(fl.begin(), fl.end(), 2); //寻找2的位置

    if (ite1 != ite2)
        fl.insert_after(ite1, 99);
    for (auto it : fl)
    {
        cout << it << " ";  //5 6 2 99 3 1
    }
    cout << endl;

    ite1 = find(fl.begin(), fl.end(), 6); //寻找6的位置
    if (ite1 != ite2)
        fl.erase_after(ite1);
    for (auto it : fl)
    {
        cout << it << " ";  //5 6 99 3 1
    }
    cout << endl;    
    return 0;
}
```

需要注意的是C++标准委员会没有采用slist的名称，forward_list在C++ 11中出现，它与slist的区别是没有size()方法。

#### STL中list、dqueue、vector 之间的区别

**list**

list不支持随机存储，适用于对象大，对象数量变化频繁，插入和删除频繁，比如写多读少的场景。

* 非连续存储
  * list 是一个环状双向链表，所以它只需要一个指针，每个节点包括三个信息：元素本身，指向前一个元素的节点（prev）和指向下一个元素的节点（next）
  * 不再能够像vector一样以普通指针作为迭代器
  * 其节点不保证在存储空间中连续存在
* 访问：随机存取效率很低 O（N），不能用下标直接访问到某个位置的元素。要访问list里的元素只能遍历，不过你要是只需要访问list的最后N个元素的话，可以用反向迭代器来遍历
* 增删改：
  * list不像vector那样有可能在空间不足时做重新配置、数据移动的操作，所以插入前的所有迭代器在插入操作之后都仍然有效
  * 由于链表特性可以高效地增删，改变指针的指向即可。

**deque**

需要从首尾两端进行插入或删除操作的时候需要选择deque

* deque是一种双向开口的连续线性空间，所谓双向开口，意思是可以在头尾两端分别做元素的插入和删除操作；可以在头尾两端分别做元素的插入和删除操作
* deque和vector最大的差异
  * 一在于deque允许常数时间内对起头端进行元素的插入或移除操作
  * 二在于deque没有所谓容量概念，因为它是动态地以分段连续空间组合而成，随时可以增加一段新的空间并链接起来，deque没有所谓的空间保留功

**vector**

vector可以随机存储元素（即可以通过公式直接计算出元素地址，而不需要挨个查找），但**在非尾部插入删除数据时，效率很低**，适合对象简单，**对象数量变化不大，随机访问频繁**。除非必要，我们尽可能选择使用vector而非deque，因为deque的迭代器比vector迭代器复杂很多。

* vector数据结构 vector和数组类似，拥有一段连续的内存空间，并且起始地址不变。因此能高效的进行随机存取，时间复杂度为o(1)
* 因为内存空间是连续的，所以在进行插入和删除操作时，会造成内存块的拷贝，时间复杂度为o(n)。
* 当数组中内存空间不够时，会重新申请一块内存空间并进行内存拷贝。连续存储结构：vector是可以实现动态增长的对象数组，支持对数组高效率的访问和在数组尾端的删除和插入操作，在中间和头部删除和插入相对不易，需要挪动大量的数据。
* 它与数组最大的区别就是vector不需程序员自己去考虑容量问题，库里面本身已经实现了容量的动态增长，而数组需要程序员手动写入扩容函数进形扩容。

* vector的随机访问效率高，但在插入和删除时（不包括尾部）需要挪动数据，不易操作。

### deque

#### deque 的实现

vector是单向开口（尾部）的连续线性空间，deque则是一种双向开口的连续线性空间，虽然vector也可以在头尾进行元素操作，但是其头部操作的效率十分低下（主要是涉及到整体的移动）

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1565876257552.png)

deque和vector的最大差异一个是deque运行在常数时间内对头端进行元素操作，二是deque没有容量的概念，它是动态地以分段连续空间组合而成，可以随时增加一段新的空间并链接起来

deque虽然也提供随机访问的迭代器，但是其迭代器并不是普通的指针，其复杂程度比vector高很多，因此除非必要，否则一般使用vector而非deque。如果需要对deque排序，可以先将deque中的元素复制到vector中，利用sort对vector排序，再将结果复制回deque

deque由一段一段的定量连续空间组成，一旦需要增加新的空间，只要配置一段定量连续空间拼接在头部或尾部即可，因此deque的最大任务是如何维护这个整体的连续性

deque的数据结构如下：

```c++
class deque
{
    ...
protected:
    typedef pointer* map_pointer;//指向map指针的指针
    map_pointer map;//指向map
    size_type map_size;//map的大小
public:
    ...
    iterator begin();
    itertator end();
    ...
}
```

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1565876324016.png)

deque内部有一个指针指向map，map是一小块连续空间，其中的每个元素称为一个节点，node，每个node都是一个指针，指向另一段较大的连续空间，称为缓冲区，这里就是deque中实际存放数据的区域，默认大小512bytes。整体结构如上图所示。

deque的迭代器数据结构如下：

```c++
struct __deque_iterator
{
    ...
    T* cur;//迭代器所指缓冲区当前的元素
    T* first;//迭代器所指缓冲区第一个元素
    T* last;//迭代器所指缓冲区最后一个元素
    map_pointer node;//指向map中的node
    ...
}
```

从deque的迭代器数据结构可以看出，为了保持与容器联结，迭代器主要包含上述4个元素

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1565877658970.png)

deque迭代器的“++”、“--”操作是远比vector迭代器繁琐，其主要工作在于缓冲区边界，如何从当前缓冲区跳到另一个缓冲区，当然deque内部在插入元素时，如果map中node数量全部使用完，且node指向的缓冲区也没有多余的空间，这时会配置新的map（2倍于当前+2的数量）来容纳更多的node，也就是可以指向更多的缓冲区。在deque删除元素时，也提供了元素的析构和空闲缓冲区空间的释放等机制。

#### deque 和 vector 的区别

* vector是单向开口的连续区间，deque是双向开口的连续区间（可以在头尾两端进行插入和删除操作）
* deque没有提供空间保留功能，也就是没有capacity这个概念，而vector提供了空间保留功能。即vector有capacity和reserve函数，deque 和 list一样，没有这两个函数。

deque是在功能上合并了vector和list。

**优点：**

1. 随机访问方便，即支持[ ]操作符和vector.at()

2. 在内部方便的进行插入和删除操作

3. 可在两端进行push、pop

缺点：占用内存多
