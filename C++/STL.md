# STL

## 问题

### 基本认识

* 什么是 STL？

* STL容器是线程安全的吗？

* 常见容器性质总结？

* 容器内部删除一个元素？

* 容器 size 和 capacity 区别

* 容器 reserve 和 resize 区别

* 谈谈仿函数


### 空间配置器

STL中的allocator、deallocator

STL的两级空间配置器

STL 内存池

allocator 的作用

### 迭代器

* 说一下STL每种容器对应的迭代器
* STL迭代器如何实现
* 迭代器失效的情况
* 迭代器：++it、it++哪个好，为什么
* 迭代器的种类

<https://blog.csdn.net/daaikuaichuan/article/details/80717222#t1>

### vector

* 函数里的vector存在堆上还是栈上，为什么？
* 定义了一个vector, 内存是如何管控的， 放在哪里（讲了动态内存， 后面被打断， 想问的是两个阶段的分配地址， 第一个是在连接过程中的重定位， 一个是加载到内存的物理地址
* vector扩容后会做哪些操作vector扩容后把旧空间的数据搬到新空间用拷贝构造还是移动构造
* 什么时候用vector,什么时候用list，有什么区别
* vector 的实现？
* vector的增加删除都是怎么做的？为什么是1.5或者是2倍？为什么不是固定增加
* vector如何释放空间?clear,swap,shrink_to_fit
* `vector<bool>` 有什么问题？
* `vector<void>` 合理吗
* 元素类型可以是引用吗

### map/set

* map 中[] 与 find 的区别？
* map 插入方式有几种？
* STL中unordered_map和map的区别和应用场景

* set 如何实现
* map、set是怎么实现的，红黑树是怎么能够同时实现这两种容器？
* set和map的区别，multimap和multiset的区别

### unordered_map

* STL中hash_map扩容发生什么？
* STL中unordered_map和map的区别，hash_map如何解决冲突以及扩容
* unordered_map 的实现
* hashtable中解决冲突有哪些方法？

### priority_queue

* priority_queue 的实现？
* 从效率角度考虑，通过什么 数据 结构可实现优先级队列？

### list/stack/queue/forward_list

* list的实现

* list 为什么不用单向链表而用双向链表，二者有什么区别，size差多少，各自优缺点

* STL中stack和queue的实现

* forward_list 的实现

* STL中list、dqueue、vector 之间的区别

* t t

### deque

* deque 的实现

## 回答

### 基本认识

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

#### 容器内部删除一个元素？

\1) 顺序容器（序列式容器，比如vector、deque）

erase迭代器不仅使所指向被删除的迭代器失效，而且使被删元素之后的所有迭代器失效(list除外)，所以不能使用erase(it++)的方式，但是erase的返回值是下一个有效迭代器；

It = c.erase(it);

\2) 关联容器(关联式容器，比如map、set、multimap、multiset等)

erase迭代器只是被删除元素的迭代器失效，但是返回值是void，所以要采用erase(it++)的方式删除迭代器；

c.erase(it++)



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



### 空间配置器

STL中的allocator、deallocator

STL的两级空间配置器

STL 内存池

#### allocator 的作用

 new在内存分配上面有一些局限性，new的机制是将内存分配和对象构造组合在一起，同样的，delete也是将对象析构和内存释放组合在一起的。allocator将这两部分分开进行，allocator申请一部分内存，不进行初始化对象，只有当需要的时候才进行初始化操作。



### 迭代器

#### 说一下STL每种容器对应的迭代器

| 容器                                   | 迭代器         |
| -------------------------------------- | -------------- |
| vector、deque                          | 随机访问迭代器 |
| stack、queue、priority_queue           | 无             |
| list、(multi)set/map                   | 双向迭代器     |
| unordered_(multi)set/map、forward_list | 前向迭代器     |

#### STL迭代器如何实现

1、 迭代器是一种抽象的设计理念，通过迭代器可以在不了解容器内部原理的情况下遍历容器，除此之外，STL中迭代器一个最重要的作用就是作为容器与STL算法的粘合剂。

2、 迭代器的作用就是提供一个遍历容器内部所有元素的接口，因此迭代器内部必须保存一个与容器相关联的指针，然后重载各种运算操作来遍历，其中最重要的是*运算符与->运算符，以及++、--等可能需要重载的运算符重载。这和C++中的智能指针很像，智能指针也是将一个指针封装，然后通过引用计数或是其他方法完成自动释放内存的功能。

3、最常用的迭代器的相应型别有五种：value type、difference type、pointer、reference、iterator catagoly;

#### 迭代器失效的情况

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

**红黑树概念**

面试时候现场写红黑树代码的概率几乎为0，但是红黑树一些基本概念还是需要掌握的。

1、它是二叉排序树（继承二叉排序树特显）：

若左子树不空，则左子树上所有结点的值均小于或等于它的根结点的值。

若右子树不空，则右子树上所有结点的值均大于或等于它的根结点的值。

左、右子树也分别为二叉排序树。

2、它满足如下几点要求：

树中所有节点非红即黑。

根节点必为黑节点。

* 红节点的子节点必为黑（黑节点子节点可为黑）。
* 从根到NULL的任何路径上黑结点数相同。

3、查找时间一定可以控制在O(logn)。

#### 迭代器：++it、it++哪个好，为什么？

\1) 前置返回一个引用，后置返回一个对象

```plaintext
// ++i实现代码为：
int& operator++()
{

  *this += 1;
  return *this;

} 
```

\2) 前置不会产生临时对象，后置必须产生临时对象，临时对象会导致效率降低

```plaintext
//i++实现代码为：                 
int operator++(int)                 
{
int temp = *this;                   

   ++*this;                       

   return temp;                  
} 
```

### vector

#### vector 的实现？

vector是一种序列式容器，其数据安排以及操作方式与array非常类似，两者的唯一差别就是对于空间运用的灵活性，众所周知，array占用的是静态空间，一旦配置了就不可以改变大小，如果遇到空间不足的情况还要自行创建更大的空间，并手动将数据拷贝到新的空间中，再把原来的空间释放。vector则使用灵活的动态空间配置，维护一块**连续的线性空间**，在空间不足时，可以自动扩展空间容纳新元素，做到按需供给。其在扩充空间的过程中仍然需要经历：**重新配置空间，移动数据，释放原空间**等操作。这里需要说明一下动态扩容的规则：以原大小的两倍配置另外一块较大的空间（或者旧长度+新增元素的个数），源码：

```plaintext
const size_type len  = old_size + max(old_size, n);
```

Vector扩容倍数与平台有关，在Win + VS 下是 1.5倍，在 Linux + GCC 下是 2 倍

测试代码：

```plaintext
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

#### vector的增加删除都是怎么做的？为什么是1.5或者是2倍？

\1) 新增元素：vector通过一个连续的数组存放元素，如果集合已满，在新增数据的时候，就要分配一块更大的内存，将原来的数据复制过来，释放之前的内存，在插入新增的元素；

\2) 对vector的任何操作，一旦引起空间重新配置，指向原vector的所有迭代器就都失效了 ；

\3) 初始时刻vector的capacity为0，塞入第一个元素后capacity增加为1；

\4) 不同的编译器实现的扩容方式不一样，VS2015中以1.5倍扩容，GCC以2倍扩容。

对比可以发现采用采用成倍方式扩容，可以保证常数的时间复杂度，而增加指定大小的容量只能达到O(n)的时间复杂度，因此，使用成倍的方式扩容。

\1) 考虑可能产生的堆空间浪费，成倍增长倍数不能太大，使用较为广泛的扩容方式有两种，以2二倍的方式扩容，或者以1.5倍的方式扩容。

\2) 以2倍的方式扩容，导致下一次申请的内存必然大于之前分配内存的总和，导致之前分配的内存不能再被使用，所以最好倍增长因子设置为(1,2)之间：

\3) 向量容器vector的成员函数pop_back()可以删除最后一个元素.

\4) 而函数erase()可以删除由一个iterator指出的元素，也可以删除一个指定范围的元素。

\5) 还可以采用通用算法remove()来删除vector容器中的元素.

\6) 不同的是：采用remove一般情况下不会改变容器的大小，而pop_back()与erase()等成员函数会改变容器的大小。

#### vector如何释放空间?

由于vector的内存占用空间只增不减，比如你首先分配了10,000个字节，然后erase掉后面9,999个，留下一个有效元素，但是内存占用仍为10,000个。所有内存空间是在vector析构时候才能被系统回收。empty()用来检测容器是否为空的，clear()可以清空所有元素。但是即使clear()，vector所占用的内存空间依然如故，无法保证内存的回收。

如果需要空间动态缩小，可以考虑使用deque。如果vector，可以用swap()来帮助你释放内存。

```c++
vector(Vec).swap(Vec); //将Vec的内存清除； 
vector().swap(Vec); //清空Vec的内存；
```

#### STL 中vector删除其中的元素，迭代器如何变化？为什么是两倍扩容？释放空间？

size()函数返回的是已用空间大小，capacity()返回的是总空间大小，capacity()-size()则是剩余的可用空间大小。当size()和capacity()相等，说明vector目前的空间已被用完，如果再添加新元素，则会引起vector空间的动态增长。

由于动态增长会引起重新分配内存空间、拷贝原空间、释放原空间，这些过程会降低程序效率。因此，可以使用reserve(n)预先分配一块较大的指定大小的内存空间，这样当指定大小的内存空间未使用完时，是不会重新分配内存空间的，这样便提升了效率。只有当n>capacity()时，调用reserve(n)才会改变vector容量。

resize()成员函数改变元素的数目，至于空间的的变化需要看具体情况去分析，如下：

```c++
void resize(size_type __new_size, const _Tp& __x) {
      if (__new_size < size()) 
            erase(begin() + __new_size, end());
      else
            insert(end(), __new_size - size(), __x);
      }
```

1、空的vector对象，size()和capacity()都为0

2、当空间大小不足时，新分配的空间大小为原空间大小的2倍。

3、使用reserve()预先分配一块内存后，在空间未满的情况下，不会引起重新分配，从而提升了效率。

4、当reserve()分配的空间比原空间小时，是不会引起重新分配的。

5、resize()函数只改变容器的元素数目，未改变容器大小。

6、用reserve(size_type)只是扩大capacity值，这些内存空间可能还是“野”的，如果此时使用“[ ]”来访问，则可能会越界。而resize(size_type new_size)会真正使容器具有new_size个对象。

不同的编译器，vector有不同的扩容大小。在vs下是1.5倍，在GCC下是2倍；

空间和时间的权衡。简单来说， 空间分配的多，平摊时间复杂度低，但浪费空间也多。

使用k=2增长因子的问题在于，每次扩展的新尺寸必然刚好大于之前分配的总和，也就是说，之前分配的内存空间不可能被使用。这样对内存不友好，最好把增长因子设为(1, 2)，也就是1-2之间的某个数值。

对比可以发现采用采用成倍方式扩容，可以保证常数的时间复杂度，而增加指定大小的容量只能达到O(n)的时间复杂度，因此，使用成倍的方式扩容。

### map/set

#### map 中[] 与 find 的区别？

* map的下标运算符[]的作用是：将关键码作为下标去执行查找，并返回对应的值；如果不存在这个关键码，就将一个具有该关键码和值类型的默认值的项插入这个map。

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

#### map 的实现

map的特性是所有元素会根据键值进行自动排序。map中所有的元素都是pair，拥有键值(key)和实值(value)两个部分，并且不允许元素有相同的key

一旦map的key确定了，那么是无法修改的，但是可以修改这个key对应的value，因此map的迭代器既不是constant iterator，也不是mutable iterator

标准STL map的底层机制是RB-tree（红黑树），另一种以hash table为底层机制实现的称为hash_map。map的架构如下图所示

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1566380621064.png)

map的在构造时缺省采用递增排序key，也使用alloc配置器配置空间大小，需要注意的是在插入元素时，调用的是红黑树中的insert_unique()方法，而非insert_euqal()（multimap使用）

需要注意的是subscript（下标）操作既可以作为左值运用（修改内容）也可以作为右值运用（获取实值）。例如：

```plaintext
maps["abc"] = 1; //左值运用int num = masp["abd"]; //右值运用
```

无论如何，subscript操作符都会先根据键值找出实值，源码如下：

```plaintext
...T& operator[](const key_type& k){    
    return (*((insert(value_type(k, T()))).first)).second;
}...
```

代码运行过程是：首先根据键值和实值做出一个元素，这个元素的实值未知，因此产生一个与实值型别相同的临时对象替代：

```plaintext
value_type(k, T());
```

再将这个对象插入到map中，并返回一个pair：

```plaintext
pair<iterator,bool> insert(value_type(k, T()));
```

pair第一个元素是迭代器，指向当前插入的新元素，如果插入成功返回true，此时对应左值运用，根据键值插入实值。插入失败（重复插入）返回false，此时返回的是已经存在的元素，则可以取到它的实值

```plaintext
(insert(value_type(k, T()))).first; //迭代器
*((insert(value_type(k, T()))).first); //解引用
(*((insert(value_type(k, T()))).first)).second; //取出实值
```

由于这个实值是以引用方式传递，因此作为左值或者右值都可以

#### set 如何实现

STL中的容器可分为序列式容器（sequence）和关联式容器（associative），set属于关联式容器。

set的特性是，所有元素都会根据元素的值自动被排序（默认升序），set元素的键值就是实值，实值就是键值，set不允许有两个相同的键值

set不允许迭代器修改元素的值，其迭代器是一种constance iterators

标准的STL set以RB-tree（红黑树）作为底层机制，几乎所有的set操作行为都是转调用RB-tree的操作行为，这里补充一下红黑树的特性：

* 每个节点不是红色就是黑色
* 根结点为黑色
* 如果节点为红色，其子节点必为黑
* 任一节点至（NULL）树尾端的任何路径，所含的黑节点数量必相同

关于红黑树的具体操作过程，比较复杂读者可以翻阅《算法导论》详细了解。

举个例子：

```plaintext
#include <set>
#include <iostream>
using namespace std;


int main()
{
    int i;
    int ia[5] = { 1,2,3,4,5 };
    set<int> s(ia, ia + 5);
    cout << s.size() << endl; // 5
    cout << s.count(3) << endl; // 1
    cout << s.count(10) << endl; // 0

    s.insert(3); //再插入一个3
    cout << s.size() << endl; // 5
    cout << s.count(3) << endl; // 1

    s.erase(1);
    cout << s.size() << endl; // 4

    set<int>::iterator b = s.begin();
    set<int>::iterator e = s.end();
    for (; b != e; ++b)
        cout << *b << " "; // 2 3 4 5
    cout << endl;

    b = find(s.begin(), s.end(), 5);
    if (b != s.end())
        cout << "5 found" << endl; // 5 found

    b = s.find(2);
    if (b != s.end())
        cout << "2 found" << endl; // 2 found

    b = s.find(1);
    if (b == s.end())
        cout << "1 not found" << endl; // 1 not found
    return 0;
}
```

关联式容器尽量使用其自身提供的find()函数查找指定的元素，效率更高，因为STL提供的find()函数是一种顺序搜索算法。

#### map、set是怎么实现的，红黑树是怎么能够同时实现这两种容器？

\1) 他们的底层都是以红黑树的结构实现，因此插入删除等操作都在O(logn时间内完成，因此可以完成高效的插入删除；

\2) 在这里我们定义了一个模版参数，如果它是key那么它就是set，如果它是map，那么它就是map；底层是红黑树，实现map的红黑树的节点数据类型是key+value，而实现set的节点数据类型是value

\3) 因为map和set要求是自动排序的，红黑树能够实现这一功能，而且时间复杂度比较低。

#### set和map的区别，multimap和multiset的区别

set只提供一种数据类型的接口，但是会将这一个元素分配到key和value上，而且它的compare_function用的是 identity()函数，这个函数是输入什么输出什么，这样就实现了set机制，set的key和value其实是一样的了。其实他保存的是两份元素，而不是只保存一份元素

map则提供两种数据类型的接口，分别放在key和value的位置上，他的比较function采用的是红黑树的comparefunction（），保存的确实是两份元素。

他们两个的insert都是采用红黑树的insert_unique() 独一无二的插入 。

multimap和map的唯一区别就是：multimap调用的是红黑树的insert_equal(),可以重复插入而map调用的则是独一无二的插入insert_unique()，multiset和set也一样，底层实现都是一样的，只是在插入的时候调用的方法不一样。

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

\1) list不再能够像vector一样以普通指针作为迭代器，因为其节点不保证在存储空间中连续存在；

\2) list插入操作和结合才做都不会造成原有的list迭代器失效;

\3) list不仅是一个双向链表，而且还是一个环状双向链表，所以它只需要一个指针；

\4) list不像vector那样有可能在空间不足时做重新配置、数据移动的操作，所以插入前的所有迭代器在插入操作之后都仍然有效；

\5) deque是一种双向开口的连续线性空间，所谓双向开口，意思是可以在头尾两端分别做元素的插入和删除操作；可以在头尾两端分别做元素的插入和删除操作；

\6) deque和vector最大的差异，一在于deque允许常数时间内对起头端进行元素的插入或移除操作，二在于deque没有所谓容量概念，因为它是动态地以分段连续空间组合而成，随时可以增加一段新的空间并链接起来，deque没有所谓的空间保留功能。

\1) vector数据结构 vector和数组类似，拥有一段连续的内存空间，并且起始地址不变。因此能高效的进行随机存取，时间复杂度为o(1);但因为内存空间是连续的，所以在进行插入和删除操作时，会造成内存块的拷贝，时间复杂度为o(n)。

另外，当数组中内存空间不够时，会重新申请一块内存空间并进行内存拷贝。连续存储结构：vector是可以实现动态增长的对象数组，支持对数组高效率的访问和在数组尾端的删除和插入操作，在中间和头部删除和插入相对不易，需要挪动大量的数据。

它与数组最大的区别就是vector不需程序员自己去考虑容量问题，库里面本身已经实现了容量的动态增长，而数组需要程序员手动写入扩容函数进形扩容。

\2) list数据结构 list是由双向链表实现的，因此内存空间是不连续的。只能通过指针访问数据，所以list的随机存取非常没有效率，时间复杂度为o(n);但由于链表的特点，能高效地进行插入和删除。非连续存储结构：list是一个双链表结构，支持对链表的双向遍历。每个节点包括三个信息：元素本身，指向前一个元素的节点（prev）和指向下一个元素的节点（next）。因此list可以高效率的对数据元素任意位置进行访问和插入删除等操作。由于涉及对额外指针的维护，所以开销比较大。

区别：

* vector的随机访问效率高，但在插入和删除时（不包括尾部）需要挪动数据，不易操作。
* list的访问要遍历整个链表，它的随机访问效率低。但对数据的插入和删除操作等都比较方便，改变指针的指向即可。
* 从遍历上来说，list是单向的，vector是双向的。
* vector中的迭代器在使用后就失效了，而list的迭代器在使用之后还可以继续使用。

3)

int mySize = vec.size();vec.at(mySize -2);

list不提供随机访问，所以不能用下标直接访问到某个位置的元素，要访问list里的元素只能遍历，不过你要是只需要访问list的最后N个元素的话，可以用反向迭代器来遍历：

### deque

#### deque 的实现

vector是单向开口（尾部）的连续线性空间，deque则是一种双向开口的连续线性空间，虽然vector也可以在头尾进行元素操作，但是其头部操作的效率十分低下（主要是涉及到整体的移动）

![img](https://cdn.jsdelivr.net/gh/forthespada/mediaImage1@1.6.4.2/202102/1565876257552.png)

deque和vector的最大差异一个是deque运行在常数时间内对头端进行元素操作，二是deque没有容量的概念，它是动态地以分段连续空间组合而成，可以随时增加一段新的空间并链接起来

deque虽然也提供随机访问的迭代器，但是其迭代器并不是普通的指针，其复杂程度比vector高很多，因此除非必要，否则一般使用vector而非deque。如果需要对deque排序，可以先将deque中的元素复制到vector中，利用sort对vector排序，再将结果复制回deque

deque由一段一段的定量连续空间组成，一旦需要增加新的空间，只要配置一段定量连续空间拼接在头部或尾部即可，因此deque的最大任务是如何维护这个整体的连续性

deque的数据结构如下：

```plaintext
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

```plaintext
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
