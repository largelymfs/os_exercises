# lec5 SPOC思考题


NOTICE
- 有"w3l1"标记的题是助教要提交到学堂在线上的。
- 有"w3l1"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题
---

请简要分析最优匹配，最差匹配，最先匹配，buddy systemm分配算法的优势和劣势，并尝试提出一种更有效的连续内存分配算法 (w3l1)
```
  + 采分点：说明四种算法的优点和缺点
  - 答案没有涉及如下3点；（0分）
  - 正确描述了二种分配算法的优势和劣势（1分）
  - 正确描述了四种分配算法的优势和劣势（2分）
  - 除上述两点外，进一步描述了一种更有效的分配算法（3分）
 ```
- [x]  


* 对于**最先匹配**算法，其优点是实现十分简单，在块进行合并的时候时间效率很高。并且能够在高地址处空余出比较大的空闲块，更加方便利用。其缺点是会产生外部碎片，而且由于按照地址进行查找而且地址小的内存块普遍比较小，因此在分配一些比较大的内存时所需要的检索时间很长，时间效率不高。
* 对于**最优匹配**算法，其优点是对于大部分需要分配的内存块比较小的时候效果比较好，因为使用这种方法可以缩小外部碎片的大小同时避免大的空闲空间。缺点是这种方法产生了外部碎片，而且在释放的时候因为要检索地址附近的内存块，因此释放的速度比较慢。第三点是这种方法很容产生许多没有用的小碎片，这些小碎片是无法利用的。
* 对于**最差匹配**算法，其优点是当分配的内存块都是中等大小的时候效果很好，因为这种方法避免产生太多的小碎片。但是缺点是释放分区的时候时间复杂度比较高，很容破坏大的空闲块，因此在分配大空闲块的时候结果不甚理想。
* 对于**伙伴系统**算法，其优点是分配的时候对于比较大的块和比较小的块都能有所考虑，同时折中了最优匹配里面省空间的做法和最先匹配里面便于合并的做法。因此这种做法的优点是在分配的时候和释放的时候时间复杂度都比较低。但是缺点是只会分配2的倍数次幂的空间，因此产生了许多内部碎片。同时由于释放的空间有的时候无法合并，因此也产生了外部碎片。

* 我觉得对于上述算法的改进，我们可以使用两种方法：
	* 第一种是使用平衡二叉树对最优匹配进行改造，这样在release的时候可以将O(n)的时间复杂度降低成O(logn)。
	* 第二种方法是针对分配块的要求是一个几何分布，因此我们可以使用这个几何分布优化伙伴系统，加入这个先验知识。会对分配的效率有很大的提升。

## 小组思考题

请参考ucore lab2代码，采用`struct pmm_manager` 根据你的`学号 mod 4`的结果值，选择四种（0:最优匹配，1:最差匹配，2:最先匹配，3:buddy systemm）分配算法中的一种或多种，在应用程序层面(可以 用python,ruby,C++，C，LISP等高语言)来实现，给出你的设思路，并给出测试用例。 (spoc)

```
如何表示空闲块？ 如何表示空闲块列表？ 
[(start0, size0),(start1,size1)...]
在一次malloc后，如果根据某种顺序查找符合malloc要求的空闲块？如何把一个空闲块改变成另外一个空闲块，或消除这个空闲块？如何更新空闲块列表？
在一次free后，如何把已使用块转变成空闲块，并按照某种顺序（起始地址，块大小）插入到空闲块列表中？考虑需要合并相邻空闲块，形成更大的空闲块？
如果考虑地址对齐（比如按照4字节对齐），应该如何设计？
如果考虑空闲/使用块列表组织中有部分元数据，比如表示链接信息，如何给malloc返回有效可用的空闲块地址而不破坏
元数据信息？
伙伴分配器的一个极简实现
http://coolshell.cn/tag/buddy
```
设计思路：我使用一个mem的列表来描述当前内存状态，每一个节点是一个三元组`(start, finish, name)`，其中`start`,`finish`表示这一段内存的边界地址，`name`是分配名称（当name为0的时候表示空闲），这样我们的算法可以这样实现(最先匹配算法)：

* 在分配的时候，按照顺序找出第一个符合大小限制的节点，然后进行分配即可。注意要按照是否有空闲碎片剩下了两种情况进行考虑。
* 在释放的时候，首先释放当前块，然后和前面，后面的块进行合并，注意最后恢复按照地址排序的列表性质。

测试样例：参见`check`函数的实现过程。


--- 

## 扩展思考题

阅读[slab分配算法](http://en.wikipedia.org/wiki/Slab_allocation)，尝试在应用程序中实现slab分配算法，给出设计方案和测试用例。

## “连续内存分配”与视频相关的课堂练习

### 5.1 计算机体系结构和内存层次
MMU的工作机理？

- [x]  

>  http://en.wikipedia.org/wiki/Memory_management_unit

L1和L2高速缓存有什么区别？

- [x]  

>  http://superuser.com/questions/196143/where-exactly-l1-l2-and-l3-caches-located-in-computer
>  Where exactly L1, L2 and L3 Caches located in computer?

>  http://en.wikipedia.org/wiki/CPU_cache
>  CPU cache

### 5.2 地址空间和地址生成
编译、链接和加载的过程了解？

- [x]  

>  

动态链接如何使用？

- [x]  

>  


### 5.3 连续内存分配
什么是内碎片、外碎片？

- [x]  

>  

为什么最先匹配会越用越慢？

- [x]  

>  

为什么最差匹配会的外碎片少？

- [x]  

>  

在几种算法中分区释放后的合并处理如何做？

- [x]  

>  

### 5.4 碎片整理
一个处于等待状态的进程被对换到外存（对换等待状态）后，等待事件出现了。操作系统需要如何响应？

- [x]  

>  

### 5.5 伙伴系统
伙伴系统的空闲块如何组织？

- [x]  

>  

伙伴系统的内存分配流程？

- [x]  

>  

伙伴系统的内存回收流程？

- [x]  

>  

struct list_entry是如何把数据元素组织成链表的？

- [x]  

>  



