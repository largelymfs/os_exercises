#lec9 虚存置换算法spoc练习

## 个人思考题
1. 置换算法的功能？

2. 全局和局部置换算法的不同？

3. 最优算法、先进先出算法和LRU算法的思路？

4. 时钟置换算法的思路？

5. LFU算法的思路？

6. 什么是Belady现象？

7. 几种局部置换算法的相关性：什么地方是相似的？什么地方是不同的？为什么有这种相似或不同？

8. 什么是工作集？

9. 什么是常驻集？

10. 工作集算法的思路？

11. 缺页率算法的思路？

12. 什么是虚拟内存管理的抖动现象？

13. 操作系统负载控制的最佳状态是什么状态？

## 小组思考题目

----
(1)（spoc）请证明为何LRU算法不会出现belady现象


* 我们假设分配的页数为X, Y, 其中 X<=Y，对应的算法为X算法和Y算法
* 那么我们考察算法在执行到时间t时候，由于LRU算法的替换特性，这时候X对应的页面集合SX,Y对应页面集合是SY，这时候SX是SY的子集
* 由 SX <= SY，那么在t时刻，X算法的缺页次数大于等于Y算法
* 上述过程对任意时刻均成立，所以X算法的总却也次数大于等于Y算法，因此不会出现belady现象。


(2)（spoc）根据你的`学号 mod 4`的结果值，确定选择四种替换算法（0：LRU置换算法，1:改进的clock 页置换算法，2：工作集页置换算法，3：缺页率置换算法）中的一种来设计一个应用程序（可基于python, ruby, C, C++，LISP等）模拟实现，并给出测试。请参考如python代码或独自实现。
 - [页置换算法实现的参考实例](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab3/page-replacement-policy.py)

 我设计的程序是关于`工作集页置换算法`，具体的程序如下：

 ```
 
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Author: largelymfs
# @Date:   2015-03-31 20:44:42
# @Last Modified by:   largelymfs
# @Last Modified time: 2015-03-31 21:19:12

from copy import deepcopy

class WorkSetAllocator:
	def __init__(self):
		self.memset = []

	def init_alloc(self, init_state, window_size, visit_seq):
		# each item (page, LVT(last visit time))
		self.memset = init_state
		print 
		print ">>>>>>>>>>>>>>>>>TEST RESULT <<<<<<<<<<<<<<<<<"
		print 
		for (t, pagenumber)  in enumerate(visit_seq):
			t+=1
			print "Time %d Page #%s: " % (t, pagenumber),
			find = False
			tmpmem = deepcopy(self.memset)
			for (page, time) in self.memset:
				if find==False and page==pagenumber:
					print "\tHit!\t",
					find = True
					tmpmem.remove((page, time))
					tmpmem.append((page, t))
					#delete all the pages out of the window
				elif time <= t - window_size:
					tmpmem.remove((page, time))
			self.memset = tmpmem
			if find==False:
				print "\tMiss!\t",
				self.memset.append((pagenumber,t))

			print "[",
			for (page, time) in self.memset:
				print "#%s" % (page),
			print "]"
		print 


if __name__=="__main__":
	visit_seq = ['c', 'c', 'd', 'b', 'c', 'e', 'c', 'e', 'a', 'd']
	window_size = 4
	init_state = [('e', -2),('d', -1), ('a', 0)]
	w = WorkSetAllocator()
	w.init_alloc(init_state, window_size, visit_seq)

```

 对应的输出为：

 ```

>>>>>>>>>>>>>>>>>TEST RESULT <<<<<<<<<<<<<<<<<

Time 1 Page #c:  	Miss!	[ #e #d #a #c ]
Time 2 Page #c:  	Hit!	[ #d #a #c ]
Time 3 Page #d:  	Hit!	[ #a #c #d ]
Time 4 Page #b:  	Miss!	[ #c #d #b ]
Time 5 Page #c:  	Hit!	[ #d #b #c ]
Time 6 Page #e:  	Miss!	[ #d #b #c #e ]
Time 7 Page #c:  	Hit!	[ #b #e #c ]
Time 8 Page #e:  	Hit!	[ #c #e ]
Time 9 Page #a:  	Miss!	[ #c #e #a ]
Time 10 Page #d:  	Miss!	[ #c #e #a #d ]

 ```

 可以看出实现的页面置换算法和视频中老师举的例子是一致的，实现是可靠的。
 在实现的时候我们记录了(page,time)的数据，其中page表示访问的页面标号，time表示最近一次的访问时间。每次进行访存的时候根据time是否在窗口内进行取舍。


## 扩展思考题
（1）了解LIRS页置换算法的设计思路，尝试用高级语言实现其基本思路。此算法是江松博士（导师：张晓东博士）设计完成的，非常不错！

参考信息：

 - [LIRS conf paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang02_LIRS.pdf)
 - [LIRS journal paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang05_LIRS.pdf)
 - [LIRS-replacement ppt1](http://dragonstar.ict.ac.cn/course_09/XD_Zhang/(6)-LIRS-replacement.pdf)
 - [LIRS-replacement ppt2](http://www.ece.eng.wayne.edu/~sjiang/Projects/LIRS/sig02.ppt)