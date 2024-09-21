# RDD

## 什么是 RDD

RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是 Spark 中最基本的数据处理模型。代码中是一个抽象类，它代表一个弹性的、不可变、可分区、里面的元素可并行计算的集合。

## RDD 特点

* 分区：RDD逻辑上是分区的，每个分区的数据是抽象存在的，计算的时候会通过一个compute函数得到每个分区的数据。如果RDD是通过已有的文件系统构建，则compute函数是读取指定文件系统中的数据，如果RDD是通过其他RDD转换而来，则compute函数是执行转换逻辑将其他RDD的数据进行转换。
* 只读：对RDD进行改动，只能通过RDD的转换操作，由一个RDD得到一个新的RDD
* 依赖：RDDs通过操作算子进行转换，转换得到的新RDD包含了从其他RDDs衍生所必需的信息，RDDs之间维护着这种血缘关系，也称之为依赖。依赖包括两种
  * 窄依赖，RDDs之间分区是一一对应的
  * 宽依赖，下游RDD的每个分区与上游RDD(也称之为父RDD)的每个分区都有关，是多对多的关系。
* 缓存：如果在应用程序中多次使用同一个RDD，可以将该RDD缓存起来，该RDD只有在第一次计算的时候会根据血缘关系得到分区的数据，在后续其他地方用到该RDD的时候，会直接从缓存处取而不用再根据血缘关系计算，这样就加速后期的重用。
* CheckPoint：虽然RDD的血缘关系天然地可以实现容错，当RDD的某个分区数据失败或丢失，可以通过血缘关系重建。但是对于长时间迭代型应用来说，随着迭代的进行，RDDs之间的血缘关系会越来越长，一旦在后续迭代过程中出错，则需要通过非常长的血缘关系去重建，势必影响性能。为此，RDD支持checkpoint将数据保存到持久化的存储中，这样就可以切断之前的血缘关系，因为checkpoint后的RDD不需要知道它的父RDDs了，它可以从checkpoint处拿到数据。

## RDD 算子

RDD整体上分为Value类型和Key-Value类型

比如map、flatmap等这些，回答几个，讲一下原理就差不多了

map：遍历RDD,将函数f应用于每一个元素，返回新的RDD(transformation算子)。

foreach：遍历RDD,将函数f应用于每一个元素，无返回值(action算子)。

mapPartitions：遍历操作RDD中的每一个分区，返回新的RDD（transformation算子）。

foreachPartition：遍历操作RDD中的每一个分区。无返回值(action算子)。

# RDD 五大核心属性

```
 * Internally, each RDD is characterized by five main properties:
 *
 *  - A list of partitions
 *  - A function for computing each split
 *  - A list of dependencies on other RDDs
 *  - Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)
 *  - Optionally, a list of preferred locations to compute each split on (e.g. block locations for
 *    an HDFS file)
 *
 * All of the scheduling and execution in Spark is done based on these methods, allowing each RDD
```

* A list of partitions (一个分区列表)：这里表示一个RDD很多分区, 每一个分区内部是包含了该RDD的部分数据, Spark中任务是以Task线程的方式运行, 一个分区就对应一个Task线程, 分区列表是实现分布式并行计算的重要属性
![image](https://github.com/user-attachments/assets/24f8ac56-3357-4911-8cb8-db0c8eb88412)

  * 用户可以在创建RDD时指定RDD的分区个数, 如果没有指定, 那么就会采用默认值.分区数的默认值的计算公式如下: RDD的分区数 = max(文件的block个数, defaultMinPartitions)，通过Spark Context读取HDFS上的文件来计算分区数
* A function for computing each split (作用分区中的函数)：一个计算每个分区的函数，这里表示Spark中RDD的计算是以分区为单位的，每个RDD都会实现compute计算函数以达到这个目的.
* A list of dependencies on other RDDs (对其他RDD的依赖关系)：一个RDD会依赖于其他多个RDD, 这里涉及到RDD与RDD之间的依赖关系,Spark 任务的容错机制就是根据这个特性(血统)而来;
* Optionally, a Partioner for key-value RDDs (针对k-v的分区器)：当数据为 KV 类型数据时，可以通过设定分区器(可选)自定义数据的分区
* Optionally, a list of preferred locations to compute each split on (数据本地性)：一个列表，存储每个Partition的优先位置(可选项)，这里涉及到数据的本地性，数据块位置最优。spark任务在调度的时候会优先考虑存有数据的节点开启计算任务，减少数据的网络传输，提升计算效率


## RDD 和 DataFrame, DataSet 区别和联系

RDD（Resilient Distributed Dataset）

RDD是Spark中最基本的数据结构，代表了一个不可变的、分区记录的集合。它允许程序员在大型集群上以容错的方式执行内存计算。RDD具有惰性机制，只有在遇到action算子时才会开始遍历运算。此外，RDD提供了丰富的转换和动作操作，如map、filter、reduce等，使得用户可以方便地对数据进行处理。

DataFrame

与RDD不同，DataFrame在Spark中是以列的形式组织的数据结构，类似于关系数据库中的表。DataFrame是一个不可变的分布式数据集合，它允许开发人员将数据结构（类型）加到分布式数据集合上，从而实现更高级别的抽象。DataFrame提供了更友好的API，让用户能够更轻松地进行数据查询和分析。

Dataset

Dataset是DataFrame API的扩展，它提供了类型安全（type-safe）和面向对象（object-oriented）的编程接口。Dataset允许用户在Spark中进行高效的数据处理，同时保持了强类型检查的优点。Dataset利用Catalyst optimizer，允许用户通过类似于SQL的表达式对数据进行查询。

三者之间的关系与比较

RDD、DataFrame和Dataset在Spark中各有优势，它们之间的关系可以归结为以下几点：

* 共性：三者都是Spark平台下的分布式弹性数据集，为处理超大型数据提供了便利。它们都具有惰性机制、共同的函数（如map、filter、排序等）以及自动缓存运算的特性。此外，三者都有partition的概念，使得数据可以在集群的不同节点上并行处理。
* 区别：DataFrame和Dataset相比于RDD提供了更高级别的抽象和更友好的API。DataFrame以列的形式组织数据，使得数据查询和分析更加便捷。而Dataset则提供了类型安全，使得代码更加健壮和易于维护。
* 应用场景：对于简单的数据处理任务，RDD可能是一个不错的选择。然而，对于需要更复杂查询和分析的任务，DataFrame和Dataset可能更为合适。在实际应用中，开发人员可以根据具体的需求和场景选择最合适的数据结构。

## 简述RDD的容错机制 ？

https://www.cnblogs.com/duanxz/p/6329675.html

## 简述什么是 RDD 沿袭
在Apache Spark中，RDD沿袭（也称为血统，Lineage）是RDD的一个核心概念，它指的是RDD数据的创建和转换历史。每个RDD都记录了它是如何从其他RDD通过一系列转换操作生成的。以下是RDD沿袭的一些关键点：

转换操作记录：

RDD的沿袭记录了所有转换操作，如map、filter、reduce等，这些操作定义了RDD之间的依赖关系。
依赖关系：

RDD之间的依赖关系可以是窄依赖或宽依赖。窄依赖意味着子RDD的每个分区是由父RDD的一个或少数几个分区经过一对一的转换生成的。宽依赖则意味着子RDD的每个分区可能由多个父RDD的分区生成。
容错能力：

RDD的沿袭为Spark提供了容错能力。如果某个RDD的分区数据丢失，Spark可以利用沿袭信息重新计算丢失的数据。
数据重构：

当RDD被持久化（缓存）时，如果部分数据丢失，Spark可以使用其沿袭信息重新构建丢失的数据，而不需要从头开始重新计算整个数据集。
优化执行计划：

Spark的DAGScheduler可以根据RDD的沿袭信息优化作业的执行计划，包括识别可以并行执行的任务和需要按顺序执行的任务。
内存和存储效率：

通过沿袭信息，Spark可以更有效地管理内存和存储资源，因为只有实际需要的数据才会被重新计算和存储。
转换与行动：

RDD的转换操作是惰性的，不会立即执行，直到遇到行动操作时，才会根据沿袭信息触发实际的计算。
数据流：

RDD沿袭描述了数据在Spark应用程序中的流动方式，从源头数据集开始，通过一系列的转换操作，最终形成结果数据集。
可扩展性：

沿袭机制使得Spark能够轻松扩展新的转换操作，同时保持容错和优化执行计划的能力。
可视化和调试：

RDD的沿袭信息可以被可视化，帮助开发者理解数据的来源和转换过程，从而更容易地调试和优化Spark应用程序。
RDD沿袭是Spark设计中的一个关键特性，它为Spark提供了强大的容错能力、优化执行计划的能力，以及高效的内存和存储管理。


## RDD 的缓存级别


9. RDD的cache和persist的区别？
