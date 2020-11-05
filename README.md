# 后端知识

## 1. C++基础知识
### 编译相关
* GCC编译过程
  GCC编译分成预处理，编译，汇编，链接
  1. 预处理：是把include插入到代码中，将宏展开生成一个.i文件
  2. 编译：是把.i文件翻译成汇编.s文件
  3. 汇编：是把.s文件翻译成机器代码.o文件
  4. 链接：是把汇编生成的.o文件，库文件链接起来，生成平台可执行程序
### 语言相关
* 虚函数原理

 虚函数需要虚函数表，类如有虚函数，就会生成一个vtable存放虚函数地址；编译器还在类中加入隐藏的
 vptr变量；若子类没有覆写虚函数，该子类的vtable会存放父类的函数地址，调用虚函数时，就会通过
 vtable解析函数地址，因此将子类的对象赋值给基类的指针时，vptr变量会指向子类的vtable
 
* 浅拷贝和深拷贝

 浅拷贝会将对象的所用的成员复制到另一个对象中，除了了复制对象成员的值，深拷贝还会复制所有的指针对象
 
* volatile关键字

 关键字volatile 的作用是指示编译器，即使代码不对变量做任何改动，该变量的值仍可
 能会被外界修改。操作系统、硬件或其他线程都有可能修改该变量。该变量的值有可能遭受意
 料之外的修改，因此，每一次使用时，编译器都会重新从内存中获取这个值
## 2. 网络知识
## 3. 多线程，多进程（线程池）
## 4. 锁(互斥锁，自旋锁，读写锁)的使用场景
高并发的场景下，如果选对了合适的锁，则会大大的提高系统的性能，否则会降低系统的性能；
为了选择合适的锁，不仅需要清楚知道加锁的成本开销多大，还需要分析业务场景中访问共享
资源的方式，再来还要考虑并发访问共享资源时的冲突概率
* 互斥锁和自旋锁：谁更轻松自如？

最底层的2中就是 互斥锁和自旋锁，有很多的高级的锁都是基于它们实现的；当已经有一个线程加锁后，
其他线程加锁则会失败，互斥锁和自旋锁加锁失败后的处理方式是不一样的
* **互斥锁** 加锁失败之后，线程会释放CPU，给其他线程
互斥锁是一种独占锁，比如当线程A加锁成功后，此时互斥锁已经被线程A独占，只要线程A不释放锁，线程B加锁会失败，于是就会释放
CPU让给其他的线程，既然线程B释放掉了CPU，自然线程B加锁的代码会被阻塞，对于互斥锁加锁失败的阻塞的现象是由操作系统的内核
实现的，所以互斥锁加锁失败时，会从用户态陷入到内核态，存在一定的性能开销，会有2次线程上下文切换的开销成本。如果切换线程
是属于同一个进程的话，只需要切换线程的私有数据，寄存器数据；上下文切换的耗时大概在几十纳秒和几微妙之间，如果锁住的代码执
行时间比较短，可能上下文切换的时间都比锁住的代码执行时间还长；
如果能确定锁住的代码执行时间很短，就应该用自旋锁
* **自旋锁** 加锁失败后，线程会忙等待，直到拿到锁
自旋锁是通过CPU提供的CAS函数(compare and swap),在用户态完成加锁和解锁操作，不会自动产生上下文切换，相对互斥锁来说，会开一些
开销也小一些
一般的加锁过程，分2步骤
1 查看锁的状态
2 将锁设置当前线程持有
CAS函数把这2步合并成一条硬件级指令，形成**原子操作**
使用自旋锁的时候，当发生多线程竞争锁的情况，加锁失败的线程会**忙等待**，直到拿到锁，这里的忙等待可以用while循环等待实现，
不过也可用CPU提供的**PAUSE**指令实现，如果被锁住的代码执行时间长，自旋的线程会长时间占用CPU资源，所以自旋的时间和被锁住
的代码执行时间是成**正比**的关系

* **读写锁**
读写锁适用于明确读操作的场景，读写锁的工作原理
1. 当**写锁**没有被线程持有时，多个线程可以并发的持有读锁
2. 一旦**写锁**被线程持有的话，读线程的获取读锁的操作会被阻塞，其他写线程也会被阻塞，写锁是独占锁，读锁是共享锁
知道了读写锁的工作原理，读写锁适用于读多写少的场景。根据实现的不同，读写锁可分为**读优先锁** **写优先锁** **公平读写锁**
* 悲观锁和乐观锁 是对锁使用的一种方式，乐观锁是先修改共享资源，再验证有没有冲突发生，悲观锁是先加锁，再去修改共享资源


## 5. STL容器的特征，使用场景
## 6. 数据结构（队列，数组，链表，字典，树，hash表，跳跃表）
* Tree
二叉树(Binary Tree); 二叉树分为满二叉树，完全二叉树(大小堆)

1. 满二叉树：叶子节点都在最底层，除了叶子节点，每个节点都有左右叶子节点
2. 完全二叉树：叶子节点都在最底下两层，最后一层的叶子节点都靠左侧排列，除了最后一层，其他层节点要达到最大

* Graph
图是一种更加复杂的非线性表结构，树种的元素称为节点，图中的元素称为**顶点（vertex）**；图中顶点可以
与任意的其他顶点建立连接关系，这种连接关系叫做**边（edge）**，跟顶点相连的边的条数叫做**度(degree)**;
如果边是方向的，这样的图叫做“有向图”；没有方向的图叫做“无向图”；在无向图中有“度”这个概念，表示一个顶点有
多少条边，在有向图中，把度分成入度（in-degree）和出度（out-degree）；
1. 入度（in-degree） 表示多少条边指向这个顶点
2. 出度（out-degree）表示多少条边是以这个顶点为起点指向其他顶点
3. 图的存储方式；邻接矩阵存储方法，邻接表存储方法；
4. 搜索算法：广度优先搜索算法(BFS) 和 深度优先搜索算法(DFS)
* [Trie树（前缀树)](https://github.com/lys861205/leetcode/blob/master/trie_tree-inl.h)
* 散列表
* 跳表
* B+树



## 7. 各种算法
* 哈希算法

主要用于数据加密，数据校验，唯一标识，哈希函数；还可以用于**负载均衡**， **数据分片**， **分布式存储**

* 字符串匹配算法
1. 主串 模式串</br>
 在字符串A中查找字符串B，那字符串A是主串，字符串B是模式串；主串长度记作n，模式串的长度记作m，所以n>m; 算法
 最坏的时间复杂度是O(n * m)
2. [BF算法](https://github.com/lys861205/leetcode/blob/master/BF_BM_RK_KMP.cc)
BF算法中BF是Brute Force的缩写，中文叫作暴力匹配算法，也叫朴素匹配算法,时间复杂度O(n * m)
3. [RK算法](https://github.com/lys861205/leetcode/blob/master/BF_BM_RK_KMP.cc)
RK算法的全称叫Rabin-Karp算法，模式串长度是m，主串长度是n，那在主串中，就会有n-m+1个长度是m的字串，如果比较
模式串跟n-m+1的字串进程比较，时间复杂度比较高是O(n * m); RK算法思路是：通过哈希算法对n-m+1个字串分别求哈希值，
然后逐个于模式串的哈希值进行比较大小，如果某个字串的哈希值于模式串哈希值相等，那就是匹配上了,时间复杂度O(n)
4. [BM算法 Boyer-Moore算法](https://github.com/lys861205/leetcode/blob/master/BF_BM_RK_KMP.cc)
5. [KMP算法](https://github.com/lys861205/leetcode/blob/master/BF_BM_RK_KMP.cc)
6. AC算法
7. [贪心算法](https://github.com/lys861205/leetcode/blob/master/0-1-bag.cc)
8. 分治算法
分治算法的核心思想就是四个字，分而治之，就是将原问题划分成n个规模的小问题，并且结构与原问题相似的子问题，递归的
解决这些问题，然后再合并结果，就得到原问题的解

分治算法一般使用递归实现，分治算法的递归实现中，每一层递归都会涉及到3个操作

    * 分解：将原问题分解成一系列的子问题
    * 解决：递归的求解各个子问题，若子问题足够小，则直接求解
    * 合并：将子问题的结果合并成原问题的解
    
分治算法能解决的问题，一般需要满足下面这几个条件

    * 原问题与分解的子问题有相同的模式
    * 原问题分解成的子问题可以独立求解，子问题之间没有相关性
    * 具有分解终止条件，也就是说，问题足够小时，可以直接求解
    * 可以将各个子问题结果合并成原问题结果，而这个合并操作的复杂度不能太高
    
9. 回溯算法
10. 动态规划
11. 朴素贝叶斯算法

|1|2|3|4|5|6|7|8|9|10|
|---|---|---|---|---|---|---|---|---|---|
|晴天|晴天|下雨|晴天|下雨|晴天|晴天|下雨|晴天|下雨|
|上学|没上|上学|上学|没上|上学|上学|没上|上学|上学|

概率：<br>
```
A: 小明没有上学
B：下雨了
  P(A) = 3/10
  P(B) = 4/10
  P(A|B) = 2/4
  P(B|A) = 2/3
```
朴素贝叶斯算法公式：

P(A|B) = P(B|A) * P(A) / P(B)



## 8. 工程架构(微服务，paxos, raft协议)
### 微服务
1. 定义：<br>
微服务就是把复杂的大应用，解耦拆分成几个小的应用
* 优点：有利于团队的拆分；每个应用都可以独立运维，独立扩容，独立上线，各个应用之间互不影响
* 缺点：服务之间调用关系变得更复杂，平台整体复杂商升高，出错的概率，debug问题的难度提高
为了解决这些确定，服务治理应运而生

2. 服务治理：<br>
服务治理就是管理微服务，保证平台整体正常，平稳运行，服务治理涉及的内容，如鉴权，限流，降级，熔断，
监控告警等等。

## 9. 开源组件（redis, kafka, hbase, storm, spark, rpc框架）
### redis
redis是一种键值(Key-Value)数据库，相对于关系型数据库，Redis也被叫作**非关系型数据库**
Redis中只包含“键”和“值”两部分，只能通过“键”来查询“值”；Redis主要是作为内存数据库使用，也就是说，
数据是存储在内存中的。
Redis中，键的数据类型是字符串，但是为了丰富数据的存储的方式，值的数据类型有很多，常用的数据类型有这样几种，
它们分别是字符串，列表，字典，集合，有序集合

<img src="https://github.com/lys861205/backend-knowledge/blob/master/redis_data.png" width="500" height="400">

#### 1. 数据结构

* 字符串（string）
* 列表（list）列表数据类型支持存储一组数据，对应2种实现方法，**压缩列表(ziplist)**, **双向循环链表**
列表中存储的数据量比较小的时候，列表就可以采用压缩列表的方式实现，
需要同时满足以下2个条件：
1. 列表中保存的单个数据小于64B
2. 列表中数据个数少于512个
* 字典(hash), 字典类型是用来存储一组数据对，每个数据包含键值两部分，字典类型也有2种实现方式 **压缩列表(ziplist)**, **散列表**，
使用压缩列表存储，需要同时满足以下2个条件：
1. 字典中保存的键和值的大小都要小于64B
2. 字典中键值对的个数少于512个
当不能同时满足上面2个条件，Redis就使用散列表实现字典类型，Redis使用MurMurHash作为hash函数，对于hash冲突，Redis使用链表法解决，Redis散列表
动态扩容和缩容，使用渐进式扩容方式，当使用数据量大于等于散列表大小时，会触发扩容，扩容大小是使用大小的2倍（2的幂次方）
* 集合（set）集合数据类型用来存储一组不重复的数据，两种实现方式**有序数组（intset）**， **散列表**
使用有序数组存储，需要同时满足以下2个条件：
1. 存储的数据都是整数
2. 存储的数据元素个数不超过512个
* 有序集合（sortedset）用来存储一组数据，并且每个数据会附带一个得分，通过得分，将数据组织成跳表的数据结构，以支持快速的按照得分值，得分区间获取数据
也有2中实现方式**压缩列表**， **跳表**；
使用压缩列表存储，需要同时满足以下2个条件
1. 所有数据的大小要小于64B
2. 元素个数要小于128个

#### 2. redis字典(dict)
渐进式rehash过程 分配空间-->逐个迁移（定时任务事件执行）-->交换哈希表

#### 3. redis单线程模型
* cpu并非瓶颈，redis是基于内存操作的，效率极快
* 内存是瓶颈
* redis文件时间和时间事件
 1. 周期性事件
    * 删除数据库的key
    * 触发RDB和AOF持久化
    * 主从同步
    * 集群化保活
    * 统计更新服务器系统信息
#### 4. redis持久化及其原理
* RDB将数据库快照以二进制的方式保存到硬盘中

  触发逻辑是多长时间有多少条数据被修改 900 1 / 300 10 / 60 10000
  有2种方式save 和 bgsave，save会阻塞主线程，期间不能处理外部任务命令
  bgsave是fork进程把内存数据进行转存，期间可以继续提供服务
  
* AOF以文本方式，把redis更新操作命令追加到AOF文件中，以此记录数据的变化

    选项：appendfsync
    always: 每个redis写命令都需要同步写入硬盘，会严重降低redis的速度
    everysec: 每秒执行一次同步
    no: 让操作系统决定何时同步

#### 5. redis缓存击穿，雪崩
* 击穿：是未命中缓冲，导致直接访问数据库，一些非法访问会构造一些无效请求，增加数据库压力，在访问数据库之前，增加一个过滤操作，使用bloom过滤器过滤
* 雪崩：缓存在同一时间数据因为过期大面积失效，解决办法是在设置数据超时时间尽量使用随机数字

#### 6. redis集群(redis cluster)
redis集群3种解决方案
* 主从复制

过程：
 1. 从服务器连接主服务器，并发送sync命令
 2. 主服务器执行bgsave, 并使用缓存区记录bgsave之后执行的写命令
 3. 从服务器根据配置是继续使用现有的数据来处理客户端命令请求，还是向客户端返回错误
 4.  bgsave执行完成，向从服务器发送快照文件，并在发送期间继续使用缓冲区记录执行的写操作
 5. 丢弃所有的旧数据，开始载入主服务器发来的快照文件
 6. 快照文件发送完成，开始向从服务器发送缓冲区里面的数据
 7. 完成快照文件的解析操作，开始向外提供命令请求服务
 8. 主服务器同步数据完成，开始每执行一个写命令，就向从服务器发送相同的写命令
 9. 从服务器接收主服务发过来的每个写命令
* 哨兵机制

 缺点：不能解决负载均衡的问题
* cluster

 优点：解决负载均衡的问题，通过虚拟slot(16384)

## 10. linux操作系统知识

###  linux文件系统
磁盘和文件系统的管理，也是操作系统最核心的功能
* 磁盘为系统提供了最基本的持久化的存储
* 文件系统在磁盘的基础上，提供了一个管理文件的的树状结构
### 索引节点和目录项
在linux上一切皆文件，不仅普通文件和目录，就连块设备，套接字，管道等也都是统一的文件系统管理的
为了方便管理，linux文件系统为每个文件分配2个数据结构，索引节点(index node)和目录项(directory entry),
它们主要用来记录文件的元信息和目录结构。
* 索引节点，简称inode,用来记录文件的元数据，不如inode编号，文件大小，访问权限，修改日期，数据的位置等，索引节点和文件
一一对应，它跟文件内容一样，都会被持久化存储在磁盘中，所以索引节点同用占用磁盘空间
* 目录项，简称dentry，用来记录文件的名字，索引节点的指针以及其他目录项的的关联关系，多个关联的目录项，
就构成了文件系统的目录结构，目录项是由内核维护的一个内存数据结构，所以通常也被叫做目录项缓存
### VFS虚拟文件系统
VFS内部通过目录项，索引节点，逻辑块以及超级块等数据结构来管理文件
* 目录项，记录文件的名字，以及文件和其他目录项之间的关系
* 索引节点，记录了文件的元数据
* 逻辑块，是由连续磁盘扇区构成的最小读写单元，用来存储文件数据
* 超级块，用来记录文件系统整体的状态，如索引节点和逻辑块的使用情况等
其中目录项是一个内存缓存；超级块，索引节点和逻辑块，都是存储在磁盘中的持久化的数据
### 磁盘性能指标
说到磁盘性能的衡量标准，必须要提到五个常见指标，也就是我们经常用到的，使用率、饱和度、IOPS、吞吐量以及响应时间等。这五个指标，是衡量磁盘性能的基本指标
* 使用率，是指磁盘处理 I/O 的时间百分比。过高的使用率（比如超过 80%），通常意味着磁盘 I/O 存在性能瓶颈
* 饱和度，是指磁盘处理 I/O 的繁忙程度。过高的饱和度，意味着磁盘存在严重的性能瓶颈。当饱和度为 100% 时，磁盘无法接受新的 I/O 请求
* IOPS（Input/Output Per Second），是指每秒的 I/O 请求数
* 吞吐量，是指每秒的 I/O 请求大小
* 响应时间，是指 I/O 请求从发出到收到响应的间隔时间
### 磁盘 I/O 观测
iostat 是最常用的磁盘 I/O 性能观测工具，它提供了每个磁盘的使用率、IOPS、吞吐量等各种常见的性能指标，
当然，这些指标实际上来自  /proc/diskstats

iostat 指标

<img src="https://github.com/lys861205/backend-knowledge/blob/master/iostat.png" width="500" height="700">

这些指标中
* %util  ，就是我们前面提到的磁盘 I/O 使用率
* r/s+  w/s  ，就是 IOPS
* rkB/s+wkB/s ，就是吞吐量
* r_await+w_await ，就是响应时间



## 11. 数据库
## 12. 内存相关知识
### 分配器
一个优秀的内存分配器应具有以下特征：
  * 额外的空间损耗尽量少
  * 分配速度尽可能快
  * 尽量避免内存碎片
  * 缓存本地化友好
  * 通过性，兼容性，可移植性，易调试
    
 目前常用的内存分配器，有以下几个
  * glibc的ptmalloc2
  * google的tcmalloc
  * Facebook的jemalloc
  
  后面2个使用需要设置环境变量LD_PRELOAD使用

#### ptmalloc2
  1. TCMalloc 原理<br>
    ThreadCache --> CentralCache --> PageHeap, 每个线程独有一份ThreadCache,无需加锁
  2. TCMalloc内存分配分三类<br>
      * 小对象分配(0, 256KB)
      * 中对象分配(256KB, 1MB)
      * 大对象分配(1MB, +∞)
  3. TCMalloc几个名词
      * Page TCMalloc管理内存的基本单位是页（跟操作系统所指页不是一个），默认是8KB
      ```
      ./configure --with-tcmalloc-pagesize=32 or 64 (编译时可以指定)
      ```
      * Span Span是有连续的n个Page组成的，一个span记录了起始page的PageID，以及包含的page的数量，span可以被拆分成多个class size的小对象用于小对象的分配；
      也可以作为一个整体用于中对象和大对象分配，多个span以链表的形式连接
      * Size Class TCMalloc将每个小对象大小（1KB-256KB）划分成85个类别<br>
          划分规则：
                * 16字节以内 每8个字节划分一个Size class 
                      * 共有2种 8KB, 16KB
      * PageHeap
#### tcmalloc
#### jemalloc
## 13. 数据序列化(json, xml, protobuf)
* protobuf 编码结构

protobuf使用TLV的格式进行数据的组织,即Tag-Length-Value。 Tag作为该字段的唯一标识，
Length代表Value数据域的长度，Value就是数据

<img src="https://github.com/lys861205/backend-knowledge/blob/master/pb-tlv.png" width="600" height="300">

**wire_type** 类型

| Type | Meaning | Used For |
|------|---------|----------|
|0| Varint|int32, int64, uint32, uint64, sint32, sint64, bool, enum|
|1|64-bit|fixed64,sfixed64,double|
|2|Length-delimited|string, bytes,embedded, messages,packed, repeated field|
|3|Start group|groups(deprecated)|
|4|End group|groups(deprecated)|
|5|32-bit|fixed32, sfixed32, float|

* **Tag-Length-Value**: 编码类型2将使用这种结构
* **Tag-Value**: 编码类型Varint, 64-bit,32-bit将使用这种结构
* **Varint编码**：

  varint是一种对数字进行编码的方案，编码之后的数据是不定长的
  * 编码规则：
  1. 最高位表示是否继续，继续是1，代表后面7位是数字，是0后面7位用原码补齐
  2. protobuf使用小字节
* **Varint具体编码步骤**:

 比如数字300， 二进制的补码（正数的原码跟补码相同）00000001 00101100
  1. 从后往前依次取7位 得到  0000010 0101100
  2. 根据小端存储反转  得到  0101100 0000010
  3. 最高位根据编码规则补1或者0  10101100 00000010
  
  <img src="https://github.com/lys861205/backend-knowledge/blob/master/protobuf_binary.png" width="600" height="200">

* protobuf3 新特性
  1. 去掉了字段前面的修饰关键字 optinoal require(允许添加/删除协议定义中的字段，同时仍然完全向前/向后兼容较新/较旧的二进制文件）
  2. 基本的数据类型(int, string, float, double)没有了has_xxx函数了， 要判断是否有值，在定义field的时候 oneof xxx {}这样方式定义，通过枚举判断是否设置了该值<br>
  如
  ```
  message Test{
    int32 age = 1;
    oneof old {
      int 32 old = 2;
    }
  }
  //判断old是否赋值
  Test t;
  if (t.old_case() == Test::OLD_ONE_NOT_SET) 
  ```

## 14. 系统设计
### 搜索引擎

搜索引擎大致分为四个部分：**搜集，分析，索引，查询**
  * 搜集： 收集就是利用爬虫爬取网页<br>
  搜索引擎把整个互联网看作数据结构种有向图，每个页面看作一个顶点，页面包含另一个页面的链接，2个顶点之间连一条
  有向边，利用图的遍历方法，索引引擎采用广度优先的算法遍历整个互联网网页，爬虫先找一些比较知名的网站（权重较高的）链接，
  作为种子网页链接放入到队列；爬虫程序不停从队列中取出链接，爬取网页，解析网页中包含的网页链接放到队列中。关键的技术细节：
 
  1. **待爬取网页链接文件：links.bin**
    随着爬取的深入，爬虫会把链接不断的放入到队列中，链接会越来越多，多到内存无法放下所有的链接，需要用一个存储在磁盘中的文件
    links.bin作为搜索队列
  2. **网页判重文件: bloom_filter.bin**
   通过bloom过滤器判断重复爬去相同的网页
  3. **原始网页存储文件: doc_raw.bin**
   爬取到网页内容需要存储下来，以备后面离线分析，索引用。可以将多个网页按照一定的规则合并成一个文件存储，并为每个网页编号（docid）
  4. **网页链接及编号的对应文件： doc_id.bin**
   网页编号(docid)实际上为每个网页分配一个唯一的ID，方便后续的分析和索引操作；编号可以由一个中心分配计数器单独进行分配。
   在网页存储的同时，将网页编号跟链接之间的关系，存储在另一个doc_id.bin文件中
   
   
  * 分析： 负责网页内容的抽取，分词，构建临时索引；
   网页爬取下来之后，需要对网页进行离线分析
  1. **抽取网页文本信息**
  2. **分词并创建临时索引**
   对文本信息进行分词，可以使用字典和规则分词方法，将词库的单词构建Trie树，用网页内容在Trie树匹配；并且创建临时索引。
   网页文本信息分词完成之后，得到一组单词列表，把单词与网页之间的对应关系，写入到一个临时索引文件中（tmp_index.bin），
   tmp_index.bin 文件格式 
   
   |单词编号|分隔符|网页编号|
   |-------|------|-------|
   |termid|\t|docid|

   用来构建倒排索引文件。把单词跟编号(termid)关系写到磁盘中(term_id.bin)。
   term_id.bin格式：
   
   |单词|分隔符|单词编号|
   |----|-----|-------|
   |单词|\t| termid|
   
   经过这边得到2个文件
   **临时索引文件 tmp_index.bin**
   **单词编号文件 term_id.bin**
   
  * 索引： 通过分析阶段得到的临时索引，构键倒排，正排索引结构
   索引阶段主要负载将分析阶段产生的临时索引，构建倒排索引。需要对临时索引文件进行归并排序。
   倒排索引(inverted index)中记录每个单词以及包含它的网页列表。
   index.bin格式：
   
   |单词编号|分割符|包含单词的网页列表|
   |-------|------|---------------|
   |termid|\r|docid1, docid2. docid3...|
   
   把倒排索引存储到磁盘中；此外还需要记录单词编号在倒排索引文件中偏移位置，并存储到磁盘中，文件名
   term_offset.bin
   格式：
   
   |单词编号|分隔符|偏移位置|
   |------|-------|-------|
   |termid|\t|offset|
   
   经过这步得到2个文件
   **倒排索引文件 index.bin**
   **单词编号在索引文件中偏移位置文件 term_offset.bin**
   
  * 查询： 根据用户的输入，获取索引相关的网页数据，返回给用户<br>
  利用之前产生的几个文件，实现最终用户的搜索功能
  
    * doc_id.bin
    * term_id.bin:
    * index.bin: 倒排索引文件，记录每个单词编号对应的网页编号列表
    * term_offset.bin:
  
### 鉴权限流
* 鉴权：是对应用访问接口的规则进行匹配，识别，达到控制应用的访问权限
* 限流：就是对接口访问频率进行限制控制。方法有以下几种
  * 固定时间窗口限制算法
  * 滑动时间窗口限制算法
  * 令牌桶算法
  * 漏桶算法



## 15. 负载均衡算法
* 硬件负载均衡
F5商业负载均衡器
* 软件负载均衡
1. 服务端负载均衡
2. 客户端负载均衡
* 算法
随机，轮询，哈希，最小压力，权重

平滑权重A,B,C的权重5，1，1权重总和7
|次数|权重|选择|非固定权重|
|---|----|----|---------|
|1|{5, 1, 1}|A|{-2, 1, 1}|
|2|{3, 2, 2}|A|{-4, 2, 2}|
|3|{1, 3, 3}|B|{1, -4, 3}|
|4|{6, -3, 4}|A|{-1, -3, 4}|
|5|{4, -2, 5}|C|{4, -2, -2}|
|6|{9, -1, -1}|A|{2, -1, -1}|
|7|{7, 0, 0}|A|{0, 0, 0}|
|8|{5, 1, 1}|||

## 16. [程序性能优化](https://github.com/lys861205/server_profile)

## 17. 架构
### 索引架构
1. 索引的定义： 通过指定的值在海量的数据中快速查找某个数据
2. 为什么需要索引： 在实际的软件开发中，不管业务多么复杂纷繁，本质可以抽象成“对数据的存储和计算”；对应的
是存储需要数据结构，计算就是算法；存储的需求功能上就是增删该查；但是一旦数据量很大，那性能就成这些系统的需要
关注的重点。如何节省存储空间，提高数据增删该查的执行效率，就需要用到**索引**
3. 索引的需求定义
* 功能性需求
  * **数据是格式化数据还是非格式化数据**  要构建索引的原始数据，类型很多，分为结构化数据，如mysql中的数据。
   另一类是非结构化数据，如搜索引擎中网页，对于非结构化数据，一般需要做预处理，提取出关键词，对关键词构建索引
  * **数据是静态数据还是动态数据**  如果不会有数据的增加，删除，更新操作，那就是静态数据，静态数据构建索引的
  时候，只需要考虑查询效率就可以了；如果数据是实时动态更新的数据，就是动态数据，动态数据不仅要考虑索引的查询的效率
  在原始数据更新同时，还需要动态的更新索引
  * **索引存储在内存还是磁盘** 为了查询效率，一般索引数据存储在内存中，如果原始数据很大，大到单机内存无法存储
  的时候，一般2种存储方式，存储硬盘，把数据进行分片(sharding)存储到多台机器上
  * **单值查找还是区间查找** 单值查找是根据某个值进行查找；
  * **单关键词查找还是多关键词组合查找** 多关键词查找一般根据每个关键词查找出来的结果进行集合操作（并集，交集）
  得到查询结果
* 非功能性需求
  * **索引对存储空间消耗不能过大**
  * **需要考虑索引的维护成本** 索引的构建是为了提高查询效率，基于动态数据构建的索引，在数据动态更改的时候，需要
  动态更新索引，索引的更新会影响到增删操作的性能
4. 构建索引的数据结构<br>
常用的构建索引的数据结构就是，散列表，红黑树，跳表，B+树；除此以外，位图，布隆过滤器作为辅助

### 搜索架构
### 基础架构
### 业务架构
### 算法架构




