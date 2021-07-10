# 锁机制的应用

在这个实验中你将从重构代码以提高并行性中获得经验。高锁争用是欠缺并行性的多核心设备的普遍特征。提高并行性通常包括改变数据结构和锁策略以降低争用。你需要在Xv6的内存分配器和磁盘缓存中做这样的事。

在实验开始前请切换到`lock`分支：

`git checkout lock`

## 内存分配

`user/kalloctest`中的程序会对Xv6的内存分配进行压力测试：三个进程扩展和收缩它们的地址空间，结果是大量对`kalloc`的调用以及`kfree.kalloc`和`kfree`获取`kmem.lock`。kalloctest会打印在获取`kmem`锁（和一些其他的锁）中失败的测试设置操作的数量，这粗略衡量了对锁的争用：

```
$ kalloctest
start test0
test0 results:
=== lock kmem/bcache stats
lock: kmem: #test-and-set 161724 #acquire() 433008
lock: bcache: #test-and-set 0 #acquire() 812
=== top 5 contended locks:
lock: kmem: #test-and-set 161724 #acquire() 433008
lock: proc: #test-and-set 290 #acquire() 961
lock: proc: #test-and-set 240 #acquire() 962
lock: proc: #test-and-set 72 #acquire() 907
lock: proc: #test-and-set 68 #acquire() 907
test0 FAIL
start test1
total allocated number of pages: 200000 (out of 32768)
test1 OK
```

`acquire`为每个锁维护试图`acquire`这个锁的调用数量，以及未成功获取锁的测试设置操作的数量。kalloctest使用一个系统调用让内核为kmem和bache lock（这个实验的重点）输出锁争用的数量，以及五个争用最频繁的锁。如果存在锁争用，test-and-set的值会很高因为它在acquire获取锁之前经历了很多循环。这个系统调用会为kmem和bache lock返回#test-and-set的总和。

对于这个实验，你必须使用一个多核的且未执行其他任务的设备。如果你使用的设备在执行其他任务，那么kalloctest打印的test-and-set的数量将不具有参考性。你可以使用一个Athena工作站，或者你的个人电脑，但不要使用一个拨号上网的设备。

kalloctest中锁争用的根本原因是`kalloc()`只有一个空闲内存列表，并由一个锁保护。为了消除锁争用，你需要重新设计内存分配器以避免使用单个锁和单个列表。基本的思路是为每个CPU核心维护一个空闲列表，每个空闲列表由它自己的锁保护。不同CPU核心上的内存分配和释放可以并行运作，因为每个CPU核心在不同的列表上操作。主要的挑战是处理一个CPU核心的空闲内存列表为空，但另一个CPU核心的列表包含空闲内存的情况。在这种情形下，一个CPU核心必须从另一个CPU核心“窃取”空闲内存。内存窃取可能会导致锁争用，但我们希望这不会频繁发生。

你要做的事是实现各个CPU核心的空闲内存列表并在一个CPU核心的空闲内存列表为空时窃取内存。运行kalloctest检查你的实现是否减少了锁争用，以及仍然能分配所有的内存。你的输出看起来会像下面这样，尽管具体的数字会不同。确保仍然能通过usertests。

```
$ kalloctest
start test0
test0 results:
=== lock kmem/bcache stats
lock: kmem: #test-and-set 0 #acquire() 33167
lock: kmem: #test-and-set 0 #acquire() 200114
lock: kmem: #test-and-set 0 #acquire() 199752
lock: bcache: #test-and-set 0 #acquire() 28
=== top 5 contended locks:
lock: proc: #test-and-set 22303 #acquire() 180082
lock: proc: #test-and-set 4162 #acquire() 180130
lock: proc: #test-and-set 4115 #acquire() 180129
lock: proc: #test-and-set 342 #acquire() 180070
lock: proc: #test-and-set 39 #acquire() 180070
test0 OK
start test1
total allocated number of pages: 200000 (out of 32768)
test1 OK
$
$ usertests
...
ALL TESTS PASSED
$
```

请让你所有锁`initlock`的名字以`kmem`开头。

提示

- 你可以使用`kernel/param.h`中的常量`NCPU`
- 让`freerange`为运行它的CPU核心给出所有空闲内存
- 函数`cpuid`返回当前的核心编号，但只有在中断关闭时调用和使用它的结果是安全的。你应该是使用`push_off()`和`pop_off()`以关闭和开启中断。

## 磁盘缓存

如果多个进程密集地使用文件系统，它们有可能会争用`bcache.lock`，一个`kernel/bio.c`中保护磁盘块缓存的锁。`bcachetest`会创建多个重复读取不同文件的进程以产生对`bcache.lock`的争用。它的输出看起来就像这样（在你完成实验之前）：

```
$ bcachetest
start test0
test0 results:
=== lock kmem/bcache stats
lock: kmem: #test-and-set 0 #acquire() 33026
lock: kmem: #test-and-set 0 #acquire() 50
lock: kmem: #test-and-set 0 #acquire() 73
lock: bcache: #test-and-set 186438 #acquire() 65650
=== top 5 contended locks:
lock: bcache: #test-and-set 186438 #acquire() 65650
lock: proc: #test-and-set 52912 #acquire() 66921
lock: proc: #test-and-set 14693 #acquire() 66568
lock: proc: #test-and-set 13379 #acquire() 66568
lock: proc: #test-and-set 12117 #acquire() 66568
test0: FAIL
start test1
test1 OK
```

你也许会看到不同的输出，但是bache锁的test-and-set的数值会很高。如果你阅读`kernel/bio.c`中的代码，你会发现`bcache.lock`保护的缓存块列表，每个缓存块的引用计数（b->refcnt）以及缓存块的其他特征（b->dev和b->blockno）。

修改缓存块让运行`bcachetest`时的bcache中所有锁的test-and-set数量接近于0。理想情况下，所有磁盘块涉及到的锁的test-and-set总和应该为0，但实际上只要总和小于500也是可以的。修改`bget`和`brelse`让对bcache中不同块的查找和释放不太可能出现锁冲突（例如不要让所有的操作都等待`bcache.lock`）。你必须维护的不变量是最多只有一个块的一个副本被缓存。如果你完成了实验，你的输出应该看起来像下面这样（尽管不是完全相同）。保证仍然能通过usertest。

```
$ bcachetest
start test0
test0 results:
=== lock kmem/bcache stats
lock: kmem: #test-and-set 0 #acquire() 32968
lock: kmem: #test-and-set 0 #acquire() 53
lock: kmem: #test-and-set 0 #acquire() 53
lock: bcache: #test-and-set 0 #acquire() 598
lock: bcache.bucket: #test-and-set 0 #acquire() 4139
lock: bcache.bucket: #test-and-set 0 #acquire() 4131
lock: bcache.bucket: #test-and-set 0 #acquire() 2360
lock: bcache.bucket: #test-and-set 0 #acquire() 4307
lock: bcache.bucket: #test-and-set 0 #acquire() 2419
lock: bcache.bucket: #test-and-set 0 #acquire() 4420
lock: bcache.bucket: #test-and-set 0 #acquire() 4934
lock: bcache.bucket: #test-and-set 18 #acquire() 8692
lock: bcache.bucket: #test-and-set 0 #acquire() 6457
lock: bcache.bucket: #test-and-set 0 #acquire() 6197
lock: bcache.bucket: #test-and-set 0 #acquire() 6191
lock: bcache.bucket: #test-and-set 0 #acquire() 6210
lock: bcache.bucket: #test-and-set 0 #acquire() 6198
=== top 5 contended locks:
lock: proc: #test-and-set 1113301 #acquire() 68753
lock: proc: #test-and-set 845107 #acquire() 68685
lock: proc: #test-and-set 822143 #acquire() 68685
lock: proc: #test-and-set 808826 #acquire() 68685
lock: proc: #test-and-set 664514 #acquire() 68727
test0: OK
start test1
test1 OK
$ usertests
  ...
ALL TESTS PASSED
$
```

请让你所有锁的名字以`bcache`开头。这意味着，你应该为每个锁调用`initlock`，并传递一个以`bcache`开头的名字。

减少块缓存中的锁争用比内存分配中的更麻烦，因为bcache缓存确切地被多个进程共享（以及CPU核心）。对于kalloc，我们可以通过为每个CPU核心安排一个内存分配器来消除大部分的锁争用，但这对于块缓存没有帮助。我们建议你使用一个每个哈希桶有一个锁的哈希表来查找缓存中的块编号。

在下面的情形中，你的实现可以出现锁争用：

- 当两个进程同时使用同一个块编号。`bcachetest tset0`几乎不会做这样的操作
- 当两个进程同时缺失缓存，并需要查找一个未被使用的块来代替。`bcachetest tset0`几乎不会做这样的操作
- 当两个进程同时使用无论你使用什么机制来划分块和锁都会冲突的块。例如，如果两个进程使用的块的块编号在哈希表中被哈希到同一个槽。`bcachetest test0`也许会做这样的操作，这取决于你的设计，但你应该试着调整你的机制的细节以避免冲突（例如改变你的哈希表大小）。

`bcachetest`的`test1`会使用更多未被缓存的块，并测试大量的文件系统代码路径。

提示

- 阅读xv6 book中块缓存的描述
- 使用固定数量的哈希桶而不动态地调整哈希表是可以的。使用质数（如13）数量的哈希桶以减少可能的哈希冲突
- 在缓存未能找到时在哈希表中搜索并为这个缓存分配一个记录应该是原子的
- 移除所有缓存（如bcache.head）的列表并用使用它们上次被使用的时间（比如使用`kernel/trap.c`中的`ticks`）的时间戳缓存取而代之。这样`brelse`不需要获取bcache锁，`bget`也可以选择基于时间戳选择最近使用的块。
- 在`bget`中序列化缓存逐出是可以的（例如在缓存中查找缺失时复用`bget`中选择一个缓存以重复使用的部分）
- 你的实现也许在某些情形下需要保持两个锁。例如，在缓存逐出时，你也许需要保持bcache锁和一个哈希桶的锁。确保你避免了死锁。
- 在替换一个块时，你也许从一个桶移动一个`struct buf`到另一个桶，因为新的桶被哈希到另外一个同。你也许会碰到一个麻烦的情形：新的块也许会哈希到与旧的块同一个桶。确保你避免了这个情形下的死锁。

运行`make grade`确保你的代码通过所有测试。

- [Lab: locks](https://pdos.csail.mit.edu/6.828/2019/labs/lock.html)