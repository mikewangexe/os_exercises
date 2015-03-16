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

>  

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

###first fit in C

	  1 #include <stdio.h>
	  2
	  3 #define MemMax 4096
	  4 #define startaddr 0
	  5
	  6 struct node {
	  7     unsigned int addr;
	  8     unsigned int size;
	  9     struct node *next;
	 10 };
	 11
	 12 struct node freelist;
	 13 struct node usedlist;
	 14
	 15 int adjustlist(struct node *used, unsigned int size)
	 16 {
	 17     if(!used) {
	 18         return -1;
	 19     }
	 20
	 21     struct node new;
	 22     new.addr = used->addr + size;
	 23     new.size = size;
	 24     new.next = NULL;
	 25     struct node *usedhead = &usedlist;
	 26     while(usedhead)
	 27         usedhead = usedhead->next;
	 28     usedhead = &new;
	 29
	 30     used->addr = used->addr + size;
	 31     used->size = used->size - size;
	 32
	 33 }
	 34
	 35 int first_fit(unsigned int size)
	 36 {
	 37     unsigned int fitaddr = 0, fitsize = 0;
	 38
	 39     if (size > MemMax)
	 40         return -1;
	 41     struct node *head = &freelist;
	 42     for(;head != NULL; head = head->next) {
	 43         if(head->size >= size) {
	 44             fitaddr = head->addr;
	 45             fitsize = head->size;
	 46             break;
	 47         }
	 48     }
	 49
	 50     if(fitsize == size) {
	 51         if(head == &freelist) {
	 52             head->size = 0;
	 53             return 0;
	 54         }
	 55
	 56         struct node *pre = &freelist;
	 57
	 58         while(pre->next != head)
	 59             pre = pre->next
	 60         pre->next = head->next;
	 61
	 62         return 0;
	 63     } else {
	 64         adjustlist(head, size);
	 65         return 0;
	 66     }
	 67 }
	 68
	 69 int free(struct node *useless)
	 70 {
	 71     if(!useless)
	 72         return -1;
	 73
	 74     // delete from uselist
	 75     struct node *iter = &uselist;
	 76     while(iter->next != useless) {
	 77         iter = iter->next;
	 78     }
	 79
	 80     iter->next = useless->next;
	 81
	 82     // add to freelist
	 83     iter = &freelist;
	 84     while(iter) {
	 85         if(iter->addr + iter->size == useless->addr) {
	 86             iter->size += useless->size;
	 87             return 0;
	 88         }
	 89         if(!iter->next)
	 90             break;
	 91         iter = iter->next;
	 92     }
	 93     iter->next = useless;
	 94     useless->next = NULL;
	 95     return 0;
	 96
	 97 }
	 98
	 99 int main()
	100 {
	101     freelist.addr = startaddr;
	102     freelist.size = MemMax;
	103     freelist.next = NULL;
	104
	105     int re = first_fit(512);
	106
	107     printf("%d\n%d\n%d\n%d\n", re, usedlist.addr, usedlist.size, freelist.addr);
	108
	109     return 0;
	110 }    

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



