# lec6 SPOC思考题


NOTICE
- 有"w3l2"标记的题是助教要提交到学堂在线上的。
- 有"w3l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题
---

（1） (w3l2) 请简要分析64bit CPU体系结构下的分页机制是如何实现的
```
  + 采分点：说明64bit CPU架构的分页机制的大致特点和页表执行过程
  - 答案没有涉及如下3点；（0分）
  - 正确描述了64bit CPU支持的物理内存大小限制（1分）
  - 正确描述了64bit CPU下的多级页表的级数和多级页表的结构或反置页表的结构（2分）
  - 除上述两点外，进一步描述了在多级页表或反置页表下的虚拟地址-->物理地址的映射过程（3分）
 ```
- [x]  

* 64bit CPU支持的物理内存大小限制是2^64 bit，即16EB，但是很多情况下，硬件无法支持这么大的物理内存。
* 64bit CPU使用4级页表。多级页表的格式是将页表分成多级进行存储，每一级的页表存储下一级页表的起始地址。
* 具体的多级页表中虚拟地址的映射关系是：
  * 给定一个地址X,首先根据第一级页表编号读取二级页表的初始地址
  * 根据二级页表编号，读取三级页表初始地址
  * 根据三级页表编号，读取四级页表初始地址
  * 根据四级页表编号，读取目标的物理地址 

## 小组思考题
---

（1）(spoc) 某系统使用请求分页存储管理，若页在内存中，满足一个内存请求需要150ns (10^-9s)。若缺页率是10%，为使有效访问时间达到0.5us(10^-6s),求不在内存的页面的平均访问时间。请给出计算步骤。 

- [x]  

> 500=0.9\*150+0.1\*x

设不在内存中得页面平均访问时间是x (ns)，那么有：

$$500 = 0.1x + 0.9 \times 150$$解得 $$x = 3650$$

因此不在内存页面的平均访问时间是3650 ns


（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示映射不存在。
PFN6..0:页帧号
PT6..0:页表的物理基址>>5
```
在[物理内存模拟数据文件](./03-2-spoc-testdata.md)中，给出了4KB物理内存空间的值，请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents。
```
Virtual Address 6c74
Virtual Address 6b22
Virtual Address 03df
Virtual Address 69dc
Virtual Address 317a
Virtual Address 4546
Virtual Address 2c03
Virtual Address 7fd7
Virtual Address 390e
Virtual Address 748b
```

比如答案可以如下表示：
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
      
Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```

通过编写程序，得到的结果如下：

```

Virtual Address 0x6c74:
  --> pde index: 0x1b pde contents: (valid 0x1, pfn 0x20)
    --> pte index: 0x3 pte contents: (valid 0x1, pfn 0x61)
      --> Translated to Physical Address 0xc34 --> Value: 0x6
Virtual Address 0x6b22:
  --> pde index: 0x1a pde contents: (valid 0x1, pfn 0x52)
    --> pte index: 0x19 pte contents: (valid 0x1, pfn 0x47)
      --> Translated to Physical Address 0x8e2 --> Value: 0x1a
Virtual Address 0x3df:
  --> pde index: 0x0 pde contents: (valid 0x1, pfn 0x5a)
    --> pte index: 0x1e pte contents: (valid 0x1, pfn 0x5)
      --> Translated to Physical Address 0xbf --> Value: 0xf
Virtual Address 0x69dc:
  --> pde index: 0x1a pde contents: (valid 0x1, pfn 0x52)
    --> pte index: 0xe pte contents: (valid 0x0, pfn 0x7f)
      --> Fault (page table entry not valid)
Virtual Address 0x317a:
  --> pde index: 0xc pde contents: (valid 0x1, pfn 0x18)
    --> pte index: 0xb pte contents: (valid 0x1, pfn 0x35)
      --> Translated to Physical Address 0x6ba --> Value: 0x1e
Virtual Address 0x4546:
  --> pde index: 0x11 pde contents: (valid 0x1, pfn 0x21)
    --> pte index: 0xa pte contents: (valid 0x0, pfn 0x7f)
      --> Fault (page table entry not valid)
Virtual Address 0x2c03:
  --> pde index: 0xb pde contents: (valid 0x1, pfn 0x44)
    --> pte index: 0x0 pte contents: (valid 0x1, pfn 0x57)
      --> Translated to Physical Address 0xae3 --> Value: 0x16
Virtual Address 0x7fd7:
  --> pde index: 0x1f pde contents: (valid 0x1, pfn 0x12)
    --> pte index: 0x1e pte contents: (valid 0x0, pfn 0x7f)
      --> Fault (page table entry not valid)
Virtual Address 0x390e:
  --> pde index: 0xe pde contents: (valid 0x0, pfn 0x7f)
    --> Fault (page directory entry not valid)
Virtual Address 0x748b:
  --> pde index: 0x1d pde contents: (valid 0x1, pfn 0x0)
    --> pte index: 0x4 pte contents: (valid 0x0, pfn 0x7f)
      --> Fault (page table entry not valid)
```

（3）请基于你对原理课二级页表的理解，并参考Lab2建页表的过程，设计一个应用程序（可基于python, ruby, C, C++，LISP等）可模拟实现(2)题中描述的抽象OS，可正确完成二级页表转换。


编写的程序如下：(需要的地址在q.txt,初始的内存分布在mem.in中)


```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Author: largelyfs
# @Date:   2015-03-19 21:29:46
# @Last Modified by:   largelymfs
# @Last Modified time: 2015-03-19 22:09:29

def change(l):
  return [int(i, 16) for i in l]
def getIndex(a, base, index):
  return a[base +index]
PDE_BASE = 0x220

def SetUp(filename):
  mem = []
  with open(filename) as f:
    for l in f:
      words = change(l.strip().split(':')[1].strip().split())
      for item in words:
        mem.append(item)
  return mem

def convert(v, mem):
  print 'Virtual Address 0x%x:' %v

  pdeInd = (v & 0x7c00) >> 10
  pteInd = (v & 0x3e0) >> 5
  objInd = v & 0x1f

  pde = getIndex(mem, PDE_BASE, pdeInd)
  valid = pde >> 7
  pteBase = pde & 0x7f
  print '\t--> pde index: 0x%x pde contents: (valid 0x%x, pfn 0x%x)' % (pdeInd, valid, pteBase)

  if not valid:
    print '\t\t--> Fault (page directory entry not valid)'
    return
  pte = getIndex(mem, pteBase << 5, pteInd)
  pteValid = pte >> 7
  objBase = pte & 0x7f
  print '\t\t--> pte index: 0x%x pte contents: (valid 0x%x, pfn 0x%x)' % (pteInd, pteValid, objBase)
  if not pteValid:
    print '\t\t\t--> Fault (page table entry not valid)'
    return
  obj = getIndex(mem, objBase << 5, objInd)
  print '\t\t\t--> Translated to Physical Address 0x%x --> Value: 0x%x' % ((objBase << 5) + objInd, obj)


def check(mem):
  with open("q.txt") as q:
    for l in q:
      convert(int(l.strip().split()[-1], 16), mem)

if __name__ == '__main__':
  mem = SetUp("mem.in")
  check(mem)
```


（4）假设你有一台支持[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)的机器，请问你如何设计操作系统支持这种类型计算机？请给出设计方案。

 (5)[X86的页面结构](http://os.cs.tsinghua.edu.cn/oscourse/OS2015/lecture06#head-1f58ea81c046bd27b196ea2c366d0a2063b304ab)
--- 

## 扩展思考题

阅读64bit IBM Powerpc CPU架构是如何实现[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)，给出分析报告。

--- 
