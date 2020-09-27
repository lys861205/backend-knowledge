# 后端知识

## 1. C++基础知识
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
  


## 7. 各种算法
* 哈希算法

主要用于数据加密，数据校验，唯一标识，哈希函数；还可以用于**负载均衡**， **数据分片**， **分布式存储**

* 字符串匹配算法
1. BF算法
2. RK算法

## 8. 工程架构(微服务，paxos, raft协议)
## 9. 开源组件（redis, kafka, hbase, storm, spark, rpc框架）
### redis
redis是一种键值(Key-Value)数据库，相对于关系型数据库，Redis也被叫作**非关系型数据库**
Redis中只包含“键”和“值”两部分，只能通过“键”来查询“值”；Redis主要是作为内存数据库使用，也就是说，
数据是存储在内存中的。
Redis中，键的数据类型是字符串，但是为了丰富数据的存储的方式，值的数据类型有很多，常用的数据类型有这样几种，
它们分别是字符串，列表，字典，集合，有序集合

<img src="https://github.com/lys861205/backend-knowledge/blob/master/redis_data.png" width="500" height="400">

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
## 13. 数据序列化(json, xml, protobuf)
## 14. 系统设计（限流计数器，令牌桶，漏桶）
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

## 16. 程序性能优化
[程序优化](https://github.com/lys861205/server_profile)

